---
title: ggplot系列之二：mosaic图
date: 2017-02-03 12:46:47
---

{% asset_img plot_0.png "一个mosaic图" %}

[上一篇文章](https://yupototo.github.io/blog/2017/01/05/2017-01-05-ggplot%E6%95%A3%E7%82%B9%E5%9B%BE/)介绍了 ggplot 的散点图。散点图太常见了，这篇文章介绍下不那么常见，但用起来很酷的 mosaic 图。

mosaic 图在如下场景使用：展示两组 categorical data 的情况。

<!-- more -->

## 使用 ggmosaic 包

> 君子性非异也，善假于物也。

ggplot2 可以用柱状图（geom_bar）的方法生成 mosaic 图，需要对参数做一些调整。

但聪明的开发者们早已经基于 ggplot 封装了一个包：ggmosaic。我们可以像调用 geom_bar 一样调用 geom_mosaic。

我们先安装 ggmosaic 包：

{% codeblock lang:r %}
install.packages('ggmosaic')
{% endcodeblock %}

这里使用的是 ggmosaic 包里的 [fly 数据集](https://www.rdocumentation.org/packages/ggmosaic/versions/0.2.0/topics/fly)。

Fly 数据集调查了乘飞机的礼貌问题，比如：
- 你认为在飞机上把座位往后倾斜礼貌吗
- 你会倾斜座椅吗
- 你觉得带小宝宝坐飞机礼貌吗

数据共有 1040 行，共27个变量。

## 问题1：态度和行为的关系

我想了解的问题是：觉得倾斜座椅很粗鲁都人，自己会倾斜座椅吗？换句话说，大家言行合一吗？

我的猜测是：觉得倾斜椅子粗鲁的人，自己应该也不怎么会倾斜椅子。

代码如下：

{% codeblock lang:r %}
ggplot(data = fly) + 
  geom_mosaic(aes(x = product(RudeToRecline), fill=DoYouRecline)) +
  labs(title = '倾斜座椅的态度 vs 行为',
       x = '你觉得倾斜座椅粗鲁吗', 
       y = '你倾斜座椅吗',
       fill = '你倾斜座椅吗')
{% endcodeblock %}

图片如下（为了 x 轴坐标的展示，图片拉长了）：

{% asset_img plot_1.png "态度和行为" %}

可以从图中看到，认为座椅倾斜很粗鲁的人，确实也更不会倾斜座椅。符合猜测。


## 问题2：有小孩的人是否更能容忍小孩呢

数据集中有这么两个变量：
- 你是否有18岁以下的小孩
- 你觉得带小宝宝坐飞机粗鲁吗

这让我产生了一个猜想：有小孩的人会更能容忍别人带小宝宝。

为了验证这个假设，我们对上面两个变量做个 mosaic 图。

代码如下：

{% codeblock lang:r %}
ggplot(data = fly) + 
  geom_mosaic(aes(x = product(Child18), fill=RudeToBringBaby)) +
  labs(title = '“有小孩"和"觉得带小宝宝乘飞机粗鲁吗"的关系',
       x = '你有18岁以下的小孩吗', 
       y = '带小宝宝乘飞机粗鲁吗',
       fill = '带小宝宝乘飞机粗鲁吗')
{% endcodeblock %}

图片结果如下：
{% asset_img plot_2.png "带宝宝乘飞机" %}

可以从图片中看出，有18岁小孩的人，更倾向于认为带宝宝坐飞机是不粗鲁的。

## 问题3：年纪越大是否越能忍受小孩呢？

我想到一个新问题，是否年纪越大的人越能忍受小孩呢？

这个问题需要两个变量：
- 年龄，在这里也是 categorical data
- 你觉得带小宝宝坐飞机粗鲁吗

{% codeblock lang:r %}
ggplot(data = fly) + 
  geom_mosaic(aes(x = product(Age), fill=RudeToBringBaby)) +
  labs(title = '"年龄"和“觉得带小宝宝乘飞机粗鲁吗”的关系',
       x = '你的年龄', 
       y = '带小宝宝乘飞机粗鲁吗',
       fill = '带小宝宝乘飞机粗鲁吗')
{% endcodeblock %}


图片结果如下：
{% asset_img plot_3.png "年龄和带宝宝乘飞机" %}


可以看出：30岁以下的人会相对不能接受带宝宝乘飞机的行为。


## 小结

- Mosaic 图适合用在两个 categorical variable 的展示上
- ggmosaic 是个基于 ggplot 的 package，能方便地制作 mosaic 图
