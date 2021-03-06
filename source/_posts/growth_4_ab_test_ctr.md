---
title: AB测试的例子：转化率
date: 2019-06-15 22:26:03
categories:
  - 增长黑客
tags:
  - R
---

{% asset_img abtest.jpeg "题图" %}

AB 测试是互联网产品优化的科学方法，甚至可能是唯一科学的方法。在这篇文章里，我用 R 语言模拟 AB 测试的流程。

<!-- more -->

## 场景

假设我们有一个购物 APP，我们重新设计了商品页面，现在我们要回答的问题是：新设计方案对用户的购买率有提高吗？令原方案为 A，新方案为 B。

我们的原假设和备择假设是：
- 原假设：新方案的转化率 <= 老方案的转化率
- 备择假设：新方案的转化率 > 老方案的转化率

因为我们只关心 B 方案是否会比 A 方案好，所以我们使用 one-sided test。原假设是我们想要通过数据去“证伪”的假设，所以原假设是“新方案是没有用处的”，即新方案的转化率低于或等于老方案。

因为需要判断的是转化率，我们可以使用 [Z-test](https://www.statisticshowto.datasciencecentral.com/z-test/)。

## 参数设定和样本数量估计

在开始搜集数据前，我们需要设定几个参数。
- 显著性水平 significance level：我们愿意接受的犯第1类错误的概率是多少？
- 统计功效 power：我们希望的统计功效是多少？
- 给定当前的转化率（10%），我们希望 B 方案的转化率提高到多少？

我们依次设定参数如下：
- 显著性水平 5%
- 统计功效 80%
- 转化率提高到 12%

我们假设样本会平均分配到两个方案中。

我们使用 `pwr` packag 来计算需要的样本量

{% codeblock lang:r %}
pwr.2p.test(h=ES.h(p1 = 0.10, p2 = 0.12), 
            sig.level = 0.05, 
            power=0.8, 
            alternative = 'less')
{% endcodeblock %}

这个计算告诉我们，每组大概需要 3021 个样本，一共需要大约6100个样本。

根据 app 流量，我们预计1天的时间可以搜集完所有的数据。于是我们使用现成的工具（或者让工程师开发），并开始测试。

## 检查数据

一天之后，我们搜集够了6100个样本，我们开始检查数据。

我们发现两个组的数据如下：
- 原始方案：3500个样本中，有360个用户发生了转化
- 新方案：3500个样本中，有400个用户发生了转化

看上去是新方案“获胜”了，可是，这个差异在统计学上显著吗？

我们做一个使用 r 的 `prop.test` 函数：

{% codeblock lang:r %}
prop.test(x = c(360, 400), n = c(3500, 3500), alternative = 'less')
{% endcodeblock %}

结果：p 值 = 0.067，比预先设定的显著性水平高，我们不能拒绝原假设。

结论：我们没有理由认为新方案更好。

想象再另一个平行宇宙，新方案有410个用户发生了转化，我们的 p 值会是多少呢？


{% codeblock lang:r %}
prop.test(x = c(360, 410), n = c(3500, 3500), alternative = 'less')
{% endcodeblock %}


结果：p 值 = 0.031，我们可以拒绝原假设了。

结论：新方案的转化率更高。

## 为什么是 80% 和 5%？

在上面的例子中，我们选择了 80% 的 power 和 5% 的 significane level。为什么呢？Stackoverflow 的数据科学家给了[一个非常好的解释](https://stackoverflow.blog/2017/10/17/power-calculations-p-values-ab-testing-stack-overflow/)。

如果选择95%的 power 和1%的 significance level，我们的结论准确度会有极大提高，但测试会消耗更多的时间，导致影响商业决策的效率。

如果我们在刚才的例子追求这样的准确度，我们需要的样本量会翻倍，也就是说，时间成本翻倍。

如果只看单个测试，翻倍似乎是可以接受的，但一个成熟的互联网产品可能在同时进行几十个优化测试，如果每个测试都追求极端准确性，产品的迭代速度会减少50%。对于“天下武功，唯快不破”的互联网产品，这样的效率损失是绝对不可接受的。

## 更多阅读

1. {% post_link growth_4_ab_test_ctr AB测试之CTR %} （本文）
1. {% post_link growth_5_ab_test_mean AB测试之均值 %} 

参考：

- [为什么使用单侧检验](https://www.invespcro.com/blog/one-tailed-vs-two-tailed-a-b-testing-everything-you-possibly-need-to-know/)
- [pwr package](https://cran.r-project.org/web/packages/pwr/vignettes/pwr-vignette.html)
