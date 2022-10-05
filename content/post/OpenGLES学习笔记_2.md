---
title: "OpenGLES学习笔记_2"
description: "OpenGLES学习笔记_2"
keywords: "OpenGLES学习笔记_2"

date: 2020-10-06T01:44:11+08:00
lastmod: 2020-10-06T01:44:11+08:00

categories:
  - 博客
tags:
  - OpenGLES
  - Android

# 原文作者
# Post's origin author name
#author:
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
#url: "opengles学习笔记_2.html"
# 开启文章置顶，数字越小越靠前
# Sticky post set-top in home page and the smaller nubmer will more forward.
#weight: 1
# 开启数学公式渲染，可选值： mathjax, katex
# Support Math Formulas render, options: mathjax, katex
#math: mathjax
# 开启各种图渲染，如流程图、时序图、类图等
# Enable chart render, such as: flow, sequence, classes etc
mermaid: true
---

## 1.基本概念

### 1.1 顶点(Vertex)

- 一个顶点就是代表几何图形的拐点，这个点有很多属性，最重要属性的是位置

```kotlin
// 定义顶点用数组保存，这个数组通常称为顶点属性数组
val vertices = floatArrayOf(
        //Triangle
        -0.5f, -0.5f,
        0.5f, 0.5f,
        -0.5f, 0.5f,
    	// Line
    	-0.5f, 0f,
        0.5f, 0f,
    	// Point
    	0f, 0.25f
)
```

- 在OpenGL ES中只能绘制点、直线和三角形
- 定义三角形的顶点的时候，是逆时针的顺序排列顶点，称为卷曲顺序

### 1.2 片段(Fragment)

- 一个片段是一个小的、单一颜色的长方形区域，类似计算机屏幕上的一个像素
- OpenGL通过“光栅化”的过程把每个点、直线及三角形分解成大量的的小片段

### 1.3 OpenGL管道

- 顶点着色器(vertex shader)
	针对每个顶点执行一次
- 片段着色器(fragment shader)
	针对每个片段执行一次
	
- 管道概述
  ```mermaid
  graph TB
  id1(读取顶点数据) -->
  id2(执行顶点着色器) -->
  id3(组装图元) -->
  id4(光栅化图元) -->
  id5(执行片段着色器) --> 
  id6(写入帧缓冲) -->
  id7(显示在屏幕上)
  ```
  

{{< mermaid align="center" bc="#EEE">}}
  graph TB
  id1(读取顶点数据) -->
  id2(执行顶点着色器) -->
  id3(组装图元) -->
  id4(光栅化图元) -->
  id5(执行片段着色器) --> 
  id6(写入帧缓冲) -->
  id7(显示在屏幕上)
{{< /mermaid >}}

## 2.着色器程序

> 着色器程序使用GLSL编写

### 2.1 顶点着色器

```glsl
attribute vec4 a_Position;

void main() {
    gl_Position = a_Position;
    gl_PointSize = 10.0;
}
```

- attribute: 表示属性
- vec4: 包含4个分量的向量
- a_Position: (x,y,z,w)，x、y、z对应一个三维位置，w是一个特殊的坐标
- gl_Position是程序内置变量，表示当前顶点的最终位置，必须赋值
- gl_PointSize表示点的大小，可以不赋值

### 2.2 片段着色器

```glsl
precision mediump float;

uniform vec4 u_Color;

void main() {
    gl_FragColor = u_Color;
}
```

- precision: 精度限定符，可选lowp、mediump、highp
- uniform: 每次都使用相同的值，除非再次改变
- u_Color: (r,g,b,a)
- gl_FragColor是内置变量，必须赋值

### 2.3 使用着色器

- 加载着色器
- 编译着色器
- 链接着色器
- 验证着色器
- 使用着色器

注：具体代码参考GLUtil工具类，使用着色器实际上是通过使用句柄实现的，实际上是int类型的数据。

## 3.绘制

在使用着色器的时候，需要传递数据到OpenGL中，通过FloatBuffer实现数据的传递。

- 顶点数据

  ```kotlin
  // 三角形
  val vertices = floatArrayOf(
  	-0.5f, -0.5f,
  	0.5f, -0.5f,
  	0f, 0.5f
  )
  // 顶点数据
  val vertexData = ByteBuffer
              // 申请了一块本地内存，大小为数组的长度*数据类型所占的字节数
              .allocateDirect(vertices.size * 4)
              // 按照本地字节序组织内容
              .order(ByteOrder.nativeOrder())
              // 转换为FloatBuffer类型
              .asFloatBuffer()
  			// 把顶点放到FloatBuffer
              .put(vertices)
  // 从最开始的位置读取数据
  vertexData.position(0)
  ```

  

------
- **参考资料**

  OpenGL ES应用开发实践指南(Android卷)，第二章、第三章

- **代码链接**

  [chapter2](https://github.com/Zhangw1998/OpenGLESLearning/tree/master/app/chapter2)
[practice module](https://github.com/Zhangw1998/OpenGLESLearning/tree/master/practicemodule)
