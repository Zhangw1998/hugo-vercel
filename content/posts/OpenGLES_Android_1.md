+++
author = "Zhangwww"
title = "OpenGLES 2.0 学习笔记(1)"
date = "2021-10-02"
tags = [ "OpenGL" ]
+++

# OpenGLES学习笔记(1)

## 1. 入门

> 在Android中使用OpenGL ES需要使用到GLSurfaceView和Renderer

### 1.1 GLSurfaceView

```kotlin
// 设置OpenGLES版本
gl_surfaceView.setEGLContextClientVersion(2)
// 设置渲染器
gl_surfaceView.setRenderer(AirHockeyRender())
// 设置渲染模式 (RENDERMODE_CONTINUOUSLY | RENDERMODE_WHEN_DIRTY)
// RENDERMODE_CONTINUOUSLY 表示自动渲染(默认)
// RENDERMODE_WHEN_DIRTY   表示手动渲染，需要调用 GLSurfaceView.requestRender()方法渲染
gl_surfaceView.renderMode = GLSurfaceView.RENDERMODE_CONTINUOUSLY

// 注：gl_surfaceView是布局文件中GLSurfaceView的id
```

GLSurfaceView的渲染在OpenGL线程中，如果需要相互通信可以使用如下的方法

- 通信方式
  - UI线程 -> OpenGL线程：`GLSurfaceView.queueEvent()`
  - OpenGL线程 -> UI线程：`runOnUiThread()`


### 1.2  Renderer

```kotlin
class OpenGLRender : GLSurfaceView.Renderer{

    override fun onSurfaceCreated(gl: GL10?, config: EGLConfig?) {
        // 设置清空屏幕时用的颜色(R, G, B, A)
        glClearColor(1f, 0f, 0f, 0f)
    }

    override fun onSurfaceChanged(gl: GL10?, width: Int, height: Int) {
        // 设置窗口大小
        glViewport(0, 0, width, height)
    }

    override fun onDrawFrame(gl: GL10?) {
        // 清空屏幕
        glClear(GL_COLOR_BUFFER_BIT)
    }
}
```

- onSurfaceCreated

  在Surface被创建时的时候，GLSurfaceView会调用这个方法（可能会被调用多次: Activity切换，唤醒）

- onSurfaceChanged

  在Surface创建后，每次Surface的尺寸发生改变时会调用

- onDrawFrame

  在绘制的时候会被调用，在这个方法中需要绘制东西或者清空屏幕

------
- **参考资料**

  OpenGL ES应用开发实践指南(Android卷)，第一章

- **代码链接**

  [chapter1](https://github.com/Zhangw1998/OpenGLESLearning/tree/master/app/chapter1)

