+++
author = "Zhangwww"
title = "Android面试题(初级)"
date = "2021-10-31"
tags = [
	"面试",
]
+++


## 1.Android四大组件

### 1.1 Activity相关

1.Activity生命周期及各个方法作用



2.Activity启动模式及应用场景



3.Activity启动另外一个启动Activity生命周期的回调



### 1.2 Service相关

1.Service两种启动模式，有什么区别



### 1.3 Broadcast相关

1.广播的几种类型



2.有序广播怎么做到有序的



### 1.4 ContentProvider

1.项目中用过ContentProvicer，怎么用



2.第三方库使用ContentProvider获取Context，ContentProvider的加载



## 2.View部分

1.自定义View



2.View绘制流程



3.View事件分发机制

   Activty -> Window -> DecorView (dispatchTouchEvent)

   ViewGroup (onInterceptTouchEvent)

   View (onTouchEvent)

   主要方法：dispatchTouchEvent

4.View滑动冲突

参考ViewPager2滑动冲突 https://github.com/android/views-widgets-samples/blob/main/ViewPager2/app/src/main/java/androidx/viewpager2/integration/testapp/NestedScrollableHost.kt

## 3.其他部分

### 3.1.Context

具体实现类是ContextImpl，ContextWrapper是包装类，方便使用并拓展ContextImpl的功能。

Context 抽象类

ContextImpl 具体实现类 

ContextWrapper 包装类

ContextThemeWrapper、Service、Application继承ContextWrapper

Activity 继承 ContextThemeWrapper

Context个数 = Service 个数 + Activity 个数 + Application 个数

https://juejin.cn/post/6844903745814265870


### 3.2Handler机制

- Looper
- Handler
- MessageQueue

```java
public final class Looper {
    
    // 主线程不允许退出，参数为false，其他线程是参数是true
    private static void prepare(boolean quitAllowed) {
        if (sThreadLocal.get() != null) {
            throw new RuntimeException("Only one Looper may be created per thread");
        }
        sThreadLocal.set(new Looper(quitAllowed));
    }

    // Looper初始化创建MessageQueue，并记录当前Thread
    private Looper(boolean quitAllowed) {
        mQueue = new MessageQueue(quitAllowed);
        mThread = Thread.currentThread();
    }

    // 阻塞当前线程，开始从MessageQueue中获取消息并交给Handler进行处理
    public static void loop() {
        final Looper me = myLooper();
        if (me == null) {
            throw new RuntimeException("No Looper; Looper.prepare() wasn't called on this thread.");
        }
        final MessageQueue queue = me.mQueue;

        // 省略其他代码...

        for (;;) {
            Message msg = queue.next(); // might block
            if (msg == null) {
                // No message indicates that the message queue is quitting.
                return;
            }

            // 省略其他代码...
            try {
                // msg.target实际上就是Handler
                msg.target.dispatchMessage(msg);
            } catch (Exception exception) {
                throw exception;
            }
            msg.recycleUnchecked();
        }
    }
    
    // 退出循环，
    public void quit() {
        mQueue.quit(false);
    }
    
    // 安全的退出循环，会处理完MessageQueue中的Message
    public void quitSafely() {
        mQueue.quit(true);
    }

}
```

1.主线程的`Looper`是在`ActivityThread`中的`main`方法中调用的，`Loop.prepareMainLooper()`方法

2.判断当前线程是否为主线程可以通过 `Looper.getMainLooper() == Looper.meLooper()` 

3.Looper.loop()方法为什么不会产生ANR

nativeWake()方法和nativePollOnce()方法采用了Linux的epoll机制，其中nativePollOnce()的第二个值，当它是-1时会一直沉睡，直到被主动唤醒为止，当它是0时不会沉睡，当它是大于0的值时会沉睡传入的值那么多的毫秒时间。epoll机制实质上是让CPU沉睡，来保障当前线程一直在运行而不中断或者卡死，这也是Looper.loop()死循环为什么不会导致住县城ANR的根本原因。



```java
public class Handler {
    
    // snedMessage方法和sendMessageDelayed方法都会调用sendMessageAtTime方法，post方法也会调用sendMessage方法
    // 最终会调用enqueueMessage方法
    private boolean enqueueMessage(@NonNull MessageQueue queue, @NonNull Message msg, long uptimeMillis) {
        msg.target = this;
        msg.workSourceUid = ThreadLocalWorkSource.getUid();

        if (mAsynchronous) {
            msg.setAsynchronous(true);
        }
        return queue.enqueueMessage(msg, uptimeMillis);
    }
    
    // Looper中取到消息后会调用该方法，消费Message
    public void dispatchMessage(@NonNull Message msg) {
        if (msg.callback != null) {
            handleCallback(msg);
        } else {
            if (mCallback != null) {
                if (mCallback.handleMessage(msg)) {
                    return;
                }
            }
            handleMessage(msg);
        }
    }
}   


```

使用Hanlder可能会产生内存泄漏，内部类持用外部类的引用




```java
public final class MessageQueue {
    // 省略部分代码
    Message next() {
        // Return here if the message loop has already quit and been disposed.
        // This can happen if the application tries to restart a looper after quit
        // which is not supported.
        final long ptr = mPtr;
        if (ptr == 0) {
            return null;
        }

        int pendingIdleHandlerCount = -1; // -1 only during first iteration
        int nextPollTimeoutMillis = 0;
        for (;;) {
            if (nextPollTimeoutMillis != 0) {
                Binder.flushPendingCommands();
            }

            // nextPollTimeoutMillis = -1时会阻塞
            nativePollOnce(ptr, nextPollTimeoutMillis);

            synchronized (this) {
                // Try to retrieve the next message.  Return if found.
                final long now = SystemClock.uptimeMillis();
                Message prevMsg = null;
                Message msg = mMessages;
                if (msg != null && msg.target == null) {
                    // Stalled by a barrier.  Find the next asynchronous message in the queue.
                    do {
                        prevMsg = msg;
                        msg = msg.next;
                    } while (msg != null && !msg.isAsynchronous());
                }
                if (msg != null) {
                    if (now < msg.when) {
                        // Next message is not ready.  Set a timeout to wake up when it is ready.
                        nextPollTimeoutMillis = (int) Math.min(msg.when - now, Integer.MAX_VALUE);
                    } else {
                        // Got a message.
                        mBlocked = false;
                        if (prevMsg != null) {
                            prevMsg.next = msg.next;
                        } else {
                            mMessages = msg.next;
                        }
                        msg.next = null;
                        if (DEBUG) Log.v(TAG, "Returning message: " + msg);
                        msg.markInUse();
                        return msg;
                    }
                } else {
                    // No more messages.
                    nextPollTimeoutMillis = -1;
                }
                
                if (mQuitting) {
                    dispose();
                    return null;
                }

                // If first time idle, then get the number of idlers to run.
                // Idle handles only run if the queue is empty or if the first message
                // in the queue (possibly a barrier) is due to be handled in the future.
                if (pendingIdleHandlerCount < 0
                        && (mMessages == null || now < mMessages.when)) {
                    pendingIdleHandlerCount = mIdleHandlers.size();
                }
                if (pendingIdleHandlerCount <= 0) {
                    // No idle handlers to run.  Loop and wait some more.
                    mBlocked = true;
                    continue;
                }

                if (mPendingIdleHandlers == null) {
                    mPendingIdleHandlers = new IdleHandler[Math.max(pendingIdleHandlerCount, 4)];
                }
                mPendingIdleHandlers = mIdleHandlers.toArray(mPendingIdleHandlers);
            }

            // Run the idle handlers.
            // We only ever reach this code block during the first iteration.
            for (int i = 0; i < pendingIdleHandlerCount; i++) {
                final IdleHandler idler = mPendingIdleHandlers[i];
                mPendingIdleHandlers[i] = null; // release the reference to the handler

                boolean keep = false;
                try {
                    keep = idler.queueIdle();
                } catch (Throwable t) {
                    Log.wtf(TAG, "IdleHandler threw exception", t);
                }

                if (!keep) {
                    synchronized (this) {
                        mIdleHandlers.remove(idler);
                    }
                }
            }

            // Reset the idle handler count to 0 so we do not run them again.
            pendingIdleHandlerCount = 0;

            // While calling an idle handler, a new message could have been delivered
            // so go back and look again for a pending message without waiting.
            nextPollTimeoutMillis = 0;
        }
    }
   
    // 主要是把消息放入队列中，然后判断是否需要
	boolean enqueueMessage(Message msg, long when) {
        if (msg.target == null) {
            throw new IllegalArgumentException("Message must have a target.");
        }
        synchronized (this) {
            if (msg.isInUse()) {
                throw new IllegalStateException(msg + " This message is already in use.");
            }

            if (mQuitting) {
                IllegalStateException e = new IllegalStateException(
                        msg.target + " sending message to a Handler on a dead thread");
                Log.w(TAG, e.getMessage(), e);
                msg.recycle();
                return false;
            }

            msg.markInUse();
            msg.when = when;
            Message p = mMessages;
            boolean needWake;
            if (p == null || when == 0 || when < p.when) {
                // New head, wake up the event queue if blocked.
                msg.next = p;
                mMessages = msg;
                needWake = mBlocked;
            } else {
                // Inserted within the middle of the queue.  Usually we don't have to wake
                // up the event queue unless there is a barrier at the head of the queue
                // and the message is the earliest asynchronous message in the queue.
                needWake = mBlocked && p.target == null && msg.isAsynchronous();
                Message prev;
                for (;;) {
                    prev = p;
                    p = p.next;
                    if (p == null || when < p.when) {
                        break;
                    }
                    if (needWake && p.isAsynchronous()) {
                        needWake = false;
                    }
                }
                msg.next = p; // invariant: p == prev.next
                prev.next = msg;
            }

            // We can assume mPtr != 0 because mQuitting is false.
            if (needWake) {
                nativeWake(mPtr);
            }
        }
        return true;
    }
}

```

`MessageQueue`主要是通过`next()`方法取消息，在for循环里通过一系列的条件判断，是否能获取到Message对象，然后返回，否则会判断`IdleHandler`中是否有任务。

![MessageQueue.drawio](https://cdn.jsdelivr.net/gh/Zhangw1998/BlogImages/img/Message.png)

- IdleHandler，面试被问到

详情参考：https://zhuanlan.zhihu.com/p/345819916



### 3.3 App启动流程



### 3.4 进程间通信





## 4.Java部分

### 4.1 HashCode

- hashcode的作用

  1. HashCode的存在主要是为了查找的快捷性，HashCode是用来在散列存储结构中确定对象的存储地址的
  2. 如果两个对象equals相等，那么这两个对象的HashCode一定也相同
  3. 如果对象的equals方法被重写，那么对象的HashCode方法也尽量重写
  4. 如果两个对象的HashCode相同，不代表两个对象就相同，只能说明这两个对象在散列存储结构中，存放于同一个位置

- 重写equals方法

  1. 自反性：A.equals(A)要返回true.
  2. 对称性：如果A.equals(B)返回true, 则B.equals(A)也要返回true.
  3. 传递性：如果A.equals(B)为true, B.equals(C)为true, 则A.equals(C)也要为true. 说白了就是 A = B , B = C , 那么A = C.
  4. 一致性：只要A,B对象的状态没有改变，A.equals(B)必须始终返回true.
  5. A.equals(null) 要返回false.



### 4.2 HashMap

内部结构：数组 + 链表 + 红黑树

扩容机制

链表 -> 红黑树, 链表长度达到了8

红黑树 -> 链表, 节点个数为6

多线程引发的问题



### 4.3 集合框架

List

Map

### 4.4 线程池

线程池参数

线程池类型

使用场景

线程间同步



### 4.5 设计模式

单例模式


### 4.5 Java内存模型
