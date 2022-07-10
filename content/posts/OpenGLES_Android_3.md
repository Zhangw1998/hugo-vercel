+++
author = "Zhangwww"
title = "OpenGLES 2.0 学习笔记(3)"
date = "2021-10-04"
tags = ["OpenGL"]
+++
# OpenGLES(Android)学习笔记3

## 1.归一化坐标

## 2.矩阵

### 2.1 单位矩阵

$$
\left[
 \begin{matrix}
   1 & 0 & 0 & 0 \\
   0 & 1 & 0 & 0 \\
   0 & 0 & 1 & 0 \\
   0 & 0 & 0 & 1 \\
  \end{matrix} 
\right]
$$



### 2.2 平移矩阵

$$
\left[
 \begin{matrix}
   1 & 0 & 0 & x_{translation} \\
   0 & 1 & 0 & y_{translation} \\
   0 & 0 & 1 & z_{translation} \\
   0 & 0 & 0 & 1 \\
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