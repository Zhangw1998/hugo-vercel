---
title: "OpenGLES学习笔记_3"
description: "OpenGLES学习笔记_3"
keywords: "OpenGLES学习笔记_3"

date: 2020-10-06T01:44:22+08:00
lastmod: 2020-10-06T01:44:22+08:00

categories:
  - 博客
tags:
  - OpenGLES
  - Android

author: zhangwww
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
#url: "opengles学习笔记_3.html"
# 开启文章置顶，数字越小越靠前
# Sticky post set-top in home page and the smaller nubmer will more forward.
#weight: 1
# 开启数学公式渲染，可选值： mathjax, katex
# Support Math Formulas render, options: mathjax, katex
math: mathjax
# 开启各种图渲染，如流程图、时序图、类图等
# Enable chart render, such as: flow, sequence, classes etc
#mermaid: true
---



## 1.归一化坐标

## 2.矩阵

### 2.1 单位矩阵

$$
\left[
 \begin{matrix}
   1 & 0 & 0 & 0 \\\\
   0 & 1 & 0 & 0 \\\\
   0 & 0 & 1 & 0 \\\\
   0 & 0 & 0 & 1 \\\\
  \end{matrix} 
\right]
$$



### 2.2 平移矩阵

$$
\left[
 \begin{matrix}
   1 & 0 & 0 & x_{translation} \\\\
   0 & 1 & 0 & y_{translation} \\\\
   0 & 0 & 1 & z_{translation} \\\\
   0 & 0 & 0 & 1 \\\\
  \end{matrix} 
\right]
$$

## 3.正交投影

```kotlin
    /**
     * 生成正交投影
     * @param m 目标叔祖，长度只少为16
     * @param mOffset 结果矩阵的偏移量
     * @param left x轴的最小范围
     * @param right x轴的最大范围
     * @param bottom y轴的最小范围
     * @param top y轴的最大范围
     * @param near z轴的最小范围
     * @param far x轴的最大范围
     */
    fun orthoM(m: FloatArray, mOffset: Int,
        left: Float, right: Float, bottom: Float, top: Float,
        near: Float, far: Float
    )
```

## 4.坐标系

### 4.1 左手坐标系 && 右手坐标系

- 早期的OpenGL默认使用右手坐标系