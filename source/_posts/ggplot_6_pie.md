---
title: ggplot系列之6：饼图
date: 2017-06-01 13:20:15
categories:
  - 数据可视化
tags:
  - R
---

说到数据可视化，就逃不了饼图。我还记得在学校里做 presentation 的时候，饼图大概是最常见的图片类型。

这篇文章介绍怎么用 ggplot 做饼图。

<!-- more -->

## 别用饼图

其实，饼图是糟糕的数据可视化方法，**别用饼图**。

原因：饼图用面积表示数量的大小，但视觉对面积的感知能力很弱，使用饼图会导致较低的信息传递效率。

先看下面这个饼图：

{% asset_img pie_1.png "pie one" %}

上面这个图让人有至少两个困惑：
- 第2和第4位是不是一样多呢？
- 第1位到底比第2位多多少呢？

一个更好的可视化，是使用柱状图：

{% asset_img bar_1.png "bar one" %}

柱状图符合人类进化的结果：眼睛对长度的敏锐度远远高于面积。

## Less is More

有的时候，我们可能想通过饼图强调某一个分类的占比最大。其实这里也可以用柱状图。

先看下图：
{% asset_img pie_2.png "pie two" %}

使用饼图已经足够给人困扰了，使用3D形式简直就是在犯罪。

看看Joey Cherdarchuk的优化成果：
{% asset_img bar_2.png "bar two" %}

清晰，简洁，美观，舒服。

[Joey Cherdarchuk 的原文](https://www.darkhorseanalytics.com/blog/salvaging-the-pie)一步步把前面那个丑陋的饼图简化成了后面的样子，非常震撼。

## 如何用 ggplot 做饼图

回答最开始提出的问题，如何用 ggplot 做饼图呢？答案：**别做饼图，改用柱状图**。

如果你真的非饼图不画，ggplot 可能不是个好工具。饼图的缺点基本已经是统计学届的共识，所以 ggplot 完全没有提供制作饼图的 `geom_pie`。

当然，ggplot 灵活的语法还是可以画出饼图的，可以先用`geom_bar`，然后做一个坐标变换`coord_polar`。具体代码可以[参考这里](http://www.sthda.com/english/wiki/ggplot2-pie-chart-quick-start-guide-r-software-and-data-visualization)。

