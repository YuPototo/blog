---
title: 模拟“中心极限定理” - 均值篇
date: 2020-01-23 01:39:54
categories:
  - 统计理论
tags:
  - R
---

中心极限定理是统计学里最重要的定理，可能没有之一。

按照中心极限定理，在样本量足够大的时候，任何分布的样本均值，都会接近正态分布。

不管原先的分布是二项分布、泊松分布或任何千奇百怪的分布，如果我们对这个分布做抽样，只要样本量够大，作为随机变量的样本均值会呈现正态分布。

下图是个形象的说明：

{% asset_img clt.png  "clt" %}

这个定理在数学上有严谨的证明。数学证明太抽象，我们可以用 simulation 更好地展示这个定理。

<!-- more -->

## 样本数量与均值的分布

根据中心极限定理，样本均值在样本量足够大时符合正态分布，且该正态分布的均值是：

{% asset_img mean_of_sample_mean_dist.png  "mean_of_sample_dist" %}

另外，样本均值的正态分布的标准差是：

{% asset_img se_of_sample_mean_dist.png  "sd_of_sample_dist" %}

所以，随着样本逐渐增大，我们应该能观察到：
- 均值的分布逐渐接近正态分布
- 该正态分布的标准差越来越小


## 扔骰子

扔骰子是个经典的概率现象。从1到6，概率都是1/6。这个分布的数学期望是3.5（有兴趣的小伙伴可以手动算算）。

如果投1次骰子，相当于抽取一个样本，虽然数学期望仍然是3.5，但我们只会观察到有1/6的概率落在1到6中任意一个数字上。我们重复10000次抽样，并记录每次的均值（其实就是投到的数字，因为只有1次），应该会看到1到6的数字都占1/6。

如果投10次骰子，相当于抽取10个样本，均值会相对集中。我们重复10000次抽样，并记录每次的均值，应该会看到均值相对之前集中。

如果投100次骰子，相当于抽取100个样本，均值会非常接近3.5.我们重复10000次抽样，并记录每次的均值，按照中心极限定理，均值的分布会是个方差很小的正态分布。

在现实生活里，如果模拟10000次抽样，且每次抽样要投100次骰子，我们一共要投个100万次骰子。实在不可能。好在现在有了计算机，我们可以让计算机模拟投掷骰子的过程。

## R 代码

用 R 语言模拟投掷结果并做图。

### 准备

{% codeblock R代码 lang:r %}
library(tidyverse)
library(ggpubr)

set.seed(123)
{% endcodeblock %}

使用 tidyverse 包，主要是为了使用 dplyr 和 ggplot2。
ggpubr 包是为了合并图片。
为了保证分析的可重复性，需要先设定一个随机数种子，这样能保证每次“随机”的结果是一样的。

### 函数：掷骰子

{% codeblock R代码 lang:r %}
simu_dice_mean <- function(size) {
  dice_vec <- 1:6
  sample_mean <- sum(sample(dice_vec, size, replace = T)) / size
  sample_mean
}
{% endcodeblock %}

`simu_dice_mean` 函数模拟了掷骰子后计算均值的过程，我给了一个参数`size`，表示掷骰子的次数。这里会返回本次投掷的均值。

### 函数：重复实验 n 次
{% codeblock R代码 lang:r %}
simulate_df <- function(size, n) {
  df_mean <- data.frame(sample_mean = rep(0, n))
  for (i in 1:n) {
    df_mean[i, 1] <- simu_dice_mean(size)
  }
  df_mean
}
{% endcodeblock %}

`simulate_df` 重复了 n 次 `simu_dice_mean`，生成一个 data frame，包含 n 个均值。
如果需要经常做模拟，这一步代码可以大大优化。不过我只模拟这一次，所以用了最直接的 for loop。


### 函数：做图
{% codeblock R代码 lang:r %}
draw_plot <- function(df, size, bin_width) {
  stan_dev <- round(sd(df$sample_mean), 2)
  ggplot(df, aes(x = sample_mean)) + 
    geom_histogram(binwidth = bin_width) +
    scale_x_continuous(limits = c(0.5, 6.5)) + 
    labs(title = str_c('投掷骰子', size, '次'),
         subtitle = str_c('点估计标准差 = ', stan_dev),
         x = '均值',
         y = '频数') 
}
{% endcodeblock %}

前一步返回了均值的 data frame，我们可以用其画出直方图，展示分布情况。
同时我在这一步计算了这组分布的标准差。

### 函数：汇总

{% codeblock R代码 lang:r %}
simulate_and_draw <- function(size, n = 10000, bin_width) {
  simulate_df(size, n) %>%
    draw_plot(size, bin_width)
}{% endcodeblock %}

把 `simulate_df` 和 `draw_plot` 两个函数结合，生成一个新的函数`simulate_and_draw`。
准备工作结束。

### 调用函数
我们依次计算并画出掷骰子1次、5次、10次、20次、50次和100次的模拟结果（模拟10000次实验）：

{% codeblock R代码 lang:r %}
p1 <- simulate_and_draw(size = 1, bin_width = 0.2)
p2 <-  simulate_and_draw(size = 5, bin_width = 0.2)
p3 <- simulate_and_draw(size = 10,  bin_width = 0.2)
p4 <- simulate_and_draw(size = 20,  bin_width = 0.15)
p5 <- simulate_and_draw(size = 50,  bin_width = 0.1)
p6 <- simulate_and_draw(size = 200,  bin_width = 0.03)

# 结合6个图片
ggarrange(p1, p2, p3, p4, p5, p6,
          nrow = 3, ncol = 2)
{% endcodeblock %}

## 模拟结果

如下图：

{% asset_img plot_all.png  "plot_all" %}

可以清晰地看到：
- 分布的均值越来越集中在3.5
- 分布的标准差越来越小

类似的模拟，不只是掷骰子所属的离散均匀分布能做，其他分布也能做。如果我是老师，就会在这里布置一门作业：在总体分布符合泊松分布的情形下模拟中心极限定理。

以上就是中心极限定理的直观展示了。有了中心极限定理，对均值做统计推断才成为可能。
