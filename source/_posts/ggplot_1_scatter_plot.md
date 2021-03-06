---
title: ggplot系列之1：散点图
date: 2018-01-05 21:52:50
categories:
  - 数据可视化
tags:
  - R
---

{% asset_img plot_0.png "一个散点图" %}

ggplot2 是 R 语言大神 Hadly Wickham 开发的数据可视化工具。Wickham 为数据可视化抽象出了一套“语法”，掌握这套语法后就可以系统性地生成可视化数据。

这个系列文章会介绍如何使用 ggplot2 做图。

本篇介绍最常见的散点图。

<!-- more -->

## 准备数据

每次我拿到数据的第一件事情，就是看看不同变量的散点图长啥样，看到散点图之后，就能对变量之间的关系做一定猜测了。

我们先准备一份用来画图的数据。

{% codeblock lang:r %}
set.seed(955)
obs_num <- 40
dat <- data.frame(cond = rep(c("A", "B"), each = obs_num / 2),
                  x = 1:obs_num + rnorm(obs_num, sd = 3),
                  y = 1:obs_num + rnorm(obs_num, sd = 3))
{% endcodeblock %}

我们的模拟数据有3个变量，x 和 y 是数字，cond 是一个 categorical 变量。

## 最简单的散点图

下面画一个最简单的散点图。

{% codeblock lang:r %}
ggplot(dat, aes(x = x, y = y)) + 
  geom_point() + 
  labs(title = '这是一个最简单的散点图')
{% endcodeblock %}

结果如下图：
![最简单的散点图](plot_1.png)

## 添加拟合曲线

有时我们想更清晰地看到数据趋势，ggplot 可以帮我们快速添加一条拟合曲线。

{% codeblock lang:r %}
ggplot(dat, aes(x = x, y = y)) + 
  geom_point() + 
  geom_smooth() +
  labs(title = '带拟合曲线的散点图')
{% endcodeblock %}

结果如下图：

![添加拟合曲线的散点图](plot_2.png)

## 给不同组的点上色

如果数据中有不同的分组，我们会想在散点图中看到不同组的情况。

{% codeblock lang:r %}
ggplot(dat, aes(x = x, y = y, color = cond)) + 
  geom_point() + 
  labs(title = '需要用颜色分组的散点图',
       color = '分组')
{% endcodeblock %}

结果如下图：

![带颜色的散点图](plot_3.png)


## 使用 facets

如果组别太多，上色可能会让人眼花缭乱，这时我们可以使用 facets。

{% codeblock lang:R %}
ggplot(dat, aes(x = x, y = y)) + 
  geom_point() + 
  facet_wrap(~ cond) +
  labs(title = '使用 facets 的散点图')
{% endcodeblock %}

结果如下图：

![使用facet的散点图](plot_4.png)

当然，我们也可以把颜色和 cond 结合起来，生成本文最开始那张图片。代码如下：

{% codeblock lang:R %}
ggplot(dat, aes(x = x, y = y, color = cond)) + 
  geom_point() + 
  facet_wrap(~ cond)
{% endcodeblock %}
