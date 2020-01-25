---
title: 模拟“中心极限定理” - 比例篇
date: 2018-08-24 03:13:45
categories:
  - 统计学
tags:
  - R
  - Statistics
---

上一篇模拟了均值的中心极限定理，这一篇再扩展一下。

中心极限定理不只适用于均值，还可用于对“占比”的描述。什么是占比呢？

例子：
- 总统选举中候选人 A 的支持率
- App Store 落地页的下载转化率
- 认为 Google 股票会上涨的股民比例
- ...

在互联网行业，这个指标最重要的应用应该是各种转化率。

中心极限定理公诉我们，当样本足够大时，样本的比例符合正态分布。

该分布的均值等于总体的分布：

{% asset_img mean.png  "mean_of_sample_dist" %}

该分布的标准差可以用如下公式计算：
{% asset_img se.png  "se_of_sample_dist" %}

接下来我们模拟 proportion 的中心极限定理。

<!-- more -->

## 场景

假设某 app 有100万个用户，该 app 想上线一个新功能，但直接全量上线是不太好，风险太大。

为了测试这个新功能是否受欢迎，产品经理决定先让一部分用户试用，然后再对他们做调查，提问他们是否喜欢这个功能。

根据中心极限定理，样本量越大越符合正态分布，且方差越小。我们模拟不同样本量下的估计值的分布。

## R 代码

代码跟上一篇差不多。

### 准备

{% codeblock lang:r %}
library(tidyverse)
library(ggpubr)

set.seed(123)

pop_size <- 1000000
pop_player <- c(rep("like", 0.7 * pop_size), rep("dislike", 0.3 * pop_size))
{% endcodeblock %}

准备需要的 package，并定好随机数。然后创建100万名用户，其中有70%的人会喜欢新功能，有30%的人会不喜欢。

### 函数：抽样、模拟、做图

这段代码跟上一篇差不多：
- `sample_player` 进行一次抽样，可以选择抽样人数
- `simulate_df` 模拟 n 次实验
- `draw_plot` 对 n 次实验的结果做图
- `simulate_and_draw` 封装了上面几个函数

针对这篇的主题，某些地方做了调整。

{% codeblock  lang:r %}
sample_player <- function(size) {
  sample_vec <- sample(pop_player, size = size)
  sum(sample_vec == "like") / size
}

simulate_df <- function(size, n) {
  df <- data.frame(sample_prop = rep(0, n))
  for (i in 1:n) {
    df[i, 1] <- sample_player(size)
  }
  df
}

draw_plot <- function(df, size, bin_width=0.1) {
  stan_dev <- round(sd(df$sample_prop), 2)
  ggplot(df, aes(x = sample_prop)) + 
    geom_histogram(binwidth = bin_width) +
    scale_x_continuous(labels = scales::percent,
                       limits = c(0: 1)) +
    labs(title = str_c('调查', size, '个玩家'),
         subtitle = str_c('点估计标准差 = ', stan_dev),
         x = '表示"喜欢的"玩家占比',
         y = '频数') 
}

simulate_and_draw <- function(size, n = 5000, bin_width = 0.1) {
  simulate_df(size, n) %>%
  draw_plot(size, bin_width)
}
{% endcodeblock %}

### 做图
下面计算抽样5人、10人、20人、40人、100人和1000人的情况，为了节约运算时间，模拟5000轮：

{% codeblock lang:r %}
p1 <- simulate_and_draw(5, bin_width = 0.1)
p2 <- simulate_and_draw(10, bin_width = 0.05)
p3 <- simulate_and_draw(20, bin_width = 0.05)
p4 <- simulate_and_draw(40, bin_width = 0.025)
p5 <- simulate_and_draw(100, bin_width = 0.01)
p6 <- simulate_and_draw(1000, bin_width = 0.003)

ggarrange(p1, p2, p3, p4, p5, p6,
          nrow = 3, ncol = 2)
{% endcodeblock %}

## 结果

做图结果如下：

{% asset_img plot_all.png  "plot_all" %}

可以看到：
- 样本数量足够大时，样本内 proportion 的分布接近正态分布
- 样本数量越大，分布的标准差越小

对于一个100万用户的产品，如果新功能受到70%人喜欢，调查1000个用户就足以知道新功能是否受欢迎了。
