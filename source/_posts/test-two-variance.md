---
title: 对两个方差做统计检验
date: 2020-01-23 23:22:15
categories:
  - 统计理论
tags:
  - R
---

最近在工作上遇到一个问题：如何评估两个买量渠道的质量。

一般来说，最好的指标是如下几个：
- LTV：越高越好
- CPI：越低越好
- 量级：越大越好

游戏最关心的是 LTV（用户生命周期价值）。我们日常定义的 LTV 是个均值，但实际上它应该是个分布。描述两个分布时，除了期望（均值），我们还关心方差。

假设两个渠道的 LTV 一样，则方差越低的渠道效果越好，因为渠道回收的不确定性更低。

这篇文章介绍如何在 R 里对两个方差做统计检验。

<!-- more -->

## 在 R 里做 F-test

当两个样本都符合正态分布时，可以用 F-test 检查两个样本的方差是否一样。

在 R 里做 F-test 的函数是 `var.test`。

{% codeblock lang:r %}
x <- rnorm(50, mean = 0, sd = 2)
y <- rnorm(30, mean = 1, sd = 1)
var.test(x, y)  
{% endcodeblock %}

我们会发现 p-value 远远小于0。

## 例子

衡量机器质量的重要指标是产品的方差不会太高。给定两台机器和两台机器的一组产品，我们想判断是否两台机器的质量一样。

H0：两台机器产品的方差一样
Ha：两台机器产品的方差不一样

### 数据

{% codeblock lang:r %}
machine_1 <- c(2.95, 3.45, 3.5, 3.75, 3.48, 3.26, 3.33, 3.2,
               3.16, 3.2, 3.22, 3.38, 3.9, 3.36,  3.25, 3.28,
               3.2, 3.22, 2.98, 3.45, 3.7, 3.34, 3.18, 3.35, 3.12)

machine_2 <- c(3.22, 3.3, 3.34, 3.28, 3.29, 3.25, 3.3, 3.27,
               3.38, 3.34, 3.35, 3.19, 3.35, 3.05, 3.36, 3.28,
               3.3, 3.28, 3.3, 3.2, 3.16, 3.33)
{% endcodeblock %}

上面的数据来自 Statistics for Business and Economics 一书的习题。

### 做图

我们可以先做图看看分布：

{% codeblock lang:r %}
df <- data.frame(value = c(machine_1, machine_2),
                 machine = c(rep('machine_1', length(machine_1)),
                             rep('machine_2', length(machine_2))))

df %>% ggplot(aes(x = value, fill = machine)) + 
  geom_density() +
  scale_fill_manual(values = alpha(c('blue', 'red'), 0.5)) +
  labs(x = NULL,
       fill = NULL) + 
  theme_classic(14) +
  theme(legend.position = 'top')
{% endcodeblock %}

结果如图：

{% asset_img plot_density.png  "density" %}

在图片里可以看到两个机器的分布确实不太一样，机器1的方差明显要大一些。

### F-test

使用 `var.test` 对两组数据做检测。

{% codeblock lang:r %}
var.test(machine_1, machine_2)  
{% endcodeblock %}

结果：p-value = 0.000007，我们非常有把握拒绝原假设。结论：两台机器的方差不一样。

## 引申：非正态分布呢？

F-test 的一个重要前提是：两个样本符合正态分布。再看我最开始提出的问题：不同渠道 LTV 的方差是否一致。这个问题能够用 F-test 吗？

如果你对游戏用户的 LTV 有所了解就会知道，LTV 分布不是正态分布。所以，我不能用 F-test 处理上面的问题。

如果真的想要解决这个问题，我会这样做：
1. Google 查询 compare variances of non-normal data
2. 找到教程或大家对这个问题的讨论
3. 寻找能满足自己需求的方法、并找到 R 代码
4. 测试

简单搜索之后就会定位到这个页面：[Compare Multiple Sample Variances in R](http://www.sthda.com/english/wiki/compare-multiple-sample-variances-in-r#statistical-tests-for-comparing-variances)。它提供了偏离正态假设时也会比较健壮的 test：
- Levene’s test
- Fligner-Killeen test

接下来就按照上面的步骤走就可以了。


> 重要的不是解决问题的方法，而是寻找解决方法的能力。

