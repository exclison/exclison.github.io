---
title: border-image用法
date: 2022-11-16 17:11:01
tags: css
---
@[TOC]( border-image用法)
转载:菜鸟教程评论区
## 语法:
border-image: source slice width outset repeat|initial|inherit;

| 属性|描述|
|---|---|
|border-image-source|	用于指定要用于绘制边框的图像的位置|
|border-image-slice|	图像边界向内偏移|
|border-image-width	|图像边界的宽度|
|border-image-outset	|用于指定在边框外部绘制 border-image-area 的量|
|border-image-repeat	|用于设置图像边界是否应重复（repeat）、拉伸（stretch）或铺满（round）|

![在这里插入图片描述](https://img-blog.csdnimg.cn/c0f6e22fbc544d239e90425da9020b94.gif#pic_center)
根据图示，切割完 border 的背景切片后，并且也已经设置了 border 的宽度（重要）。将相应的切片填充到 border 的相应位置。

需要注意的是：不论 border 的宽度设置的多大，后面切割的参数都是根据 border-image 引入图片的尺寸设置的参数, 或者说是根据引入图片大小设置的切割参数。

切割后的四周的八个切片，四个角根据 border 设置的大小全尺寸自动缩放显示到 border 对应的四个角。

除四个角外的其他中间切片（上中，右中间，下中，左中间），可以根据设置做拉伸或重复的设置操作显示到对应的 border 位置。

注: 上边四个30部分指定的区域是不拉伸的,中间区域会拉伸,这对使用小尺寸背景图拉伸到大尺寸图形非常有用,可以自定义四个角的展示效果