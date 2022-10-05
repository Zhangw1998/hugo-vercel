---
title: "OpenGLES学习笔记_1"
description: "OpenGLES学习笔记_1"
keywords: "OpenGLES学习笔记_1"

date: 2020-10-06T01:43:10+08:00
lastmod: 2020-10-06T01:43:10+08:00

categories:
  - 博客
tags:
  - OpenGLES
  - Andorid
Author: zhangwww
# 原文链接
# Post's origin link URL
#link:
# 图片链接，用在open graph和twitter卡片上
# Image source link that will use in open graph and twitter card
#imgs:
# 在首页展开内容
# Expand content on the home page
#expand: true
# 外部链接地址，访问时直接跳转
# It's means that will redirecting to external links
#extlink:
# 在当前页面开启或关闭评论功能
# Switch to enabled or disabled comment plugins in this post
#comment:
#  enable: false
# 开启文章目录功能
# Enable table of content
#toc: false
# 绝对访问路径
# Absolute link for visit
#url: "opengles学习笔记_1.html"
# 开启文章置顶，数字越小越靠前
# Sticky post set-top in home page and the smaller nubmer will more forward.
#weight: 1
# 开启数学公式渲染，可选值： mathjax, katex
# Support Math Formulas render, options: mathjax, katex
#math: mathjax
# 开启各种图渲染，如流程图、时序图、类图等
# Enable chart render, such as: flow, sequence, classes etc
#mermaid: true
---

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
