+++
author = "Zhangwww"
title = "OpenGLES 2.0 简介"
date = "2021-10-01"
description = "Guide to emoji usage in Hugo"
tags = [ "OpenGL" ]
+++

# OpenGLES 2.0

## 1. 基本概念

- 几何图元：包括点、直线、三角形，均是通过顶点（vertex）来指定的
- 模型：根据几何图元创建的物体
- 渲染：计算机根据模型创建图像的过程

## 渲染管线

1. 指定几何对象
2. 顶点处理
3. 图元组装
4. 栅格化操作
5. 片元处理
6. 帧缓冲操作

Vertex Shader(顶点着色器) 用来替代顶点处理阶段
Fragment Shader(片元着色器) 用来替换片元处理阶段

##  顶点着色器（Vertex Shader）

- 可操作属性：
  - 位置
  - 颜色
  - 纹理坐标

## GLSL 语法

（1）GLSL 的修饰符与基本数据类型

- 修饰符
	const: 用于声明非可写的编译时常量变量
	attribute: 用于经常更改的信息，只能在顶点着色器中使用
	uniform: 用于不经常更改的信息，可用于顶点着色器和片元着色器
	varying: 用于修饰从顶点着色器向片元着色器传递的变量
	
- 基本数据类型
	int, bool, float
	float 的修饰符，可以指定精度。三种修饰符的范围和应用情况具体如下：
	 - highp:32bit, 一般用于顶点坐标（vertex Coordinate）
	 - medium:16bit, 一般用于纹理坐标（texure Coordinate）
	 - lowp:8bit, 一般用于颜色显示（color）

- 向量类型：vec4
- 矩阵类型：mat4 (表示4x4的浮点矩阵)
- 纹理类型：sample2D（一般仅在Fragment Shader中使用）


（2）内置变量
- Vertex Shader:
	位置：vec4 gl_position;
	大小：float gl_pointSize;
- Fragment Shader:
	颜色：vec4 gl_FragColor;
