---
title: 统计推断之 t 检验
date: 2020-01-24 00:49:49
categories:
  - 统计学
tags:
  - R
  - Statistics
---

T-test 应该是统计学里学习的第1个检验，可谓大家的统计推断初恋。

T-test 可以用来：
- 比较一个总体的均值和一个常数
- 比较两个总体的均值

这篇文章介绍如何用 R 做 t-test。

<!-- more -->

## 模拟

R 提供了 `t.test` 函数来做 t-test，非常直观。

### t-test: 同方差

我们随机生成两个标准正态分布，然后调用 `t.test` 做个检测。

原假设：两个分布的均值相等
备择假设：两个分布的均值不同

{% codeblock lang:r %}
x <- rnorm(50)
y <- rnorm(30)
ts <- t.test(x, y, var.equal = TRUE)  
ts
{% endcodeblock %}

注意 `t.test` 函数里的 `var.equal = TRUE`，我们的检测假设两个分布的方差相同。

返回结果如下：
{% codeblock lang:r line_number:false mark:4 %}
	Two Sample t-test

data:  x and y
t = -0.83616, df = 78, p-value = 0.4056
alternative hypothesis: true difference in means is not equal to 0
95 percent confidence interval:
 -0.5532166  0.2259611
sample estimates:
mean of x mean of y 
0.1215518 0.2851795 
{% endcodeblock %}

可以看到 p-value 很大，我们不能拒绝原假设。

### t-test：异方差

上面的 t.test 假设两个分布的方差相同，我们知道这个假设成立，因为是我们自己生成的随机数。

如果两个分布的方差不相同呢？

{% codeblock lang:r %}
x <- rnorm(50, mean = 0, sd = 3)
y <- rnorm(30, mean = 0, sd = 10)
ts <- t.test(x, y)  
ts
{% endcodeblock %}

我们删除了 `t.test` 里的 `var.equal = TRUE`。

结果如下：
返回结果如下：
{% codeblock lang:r line_number:false mark:1 %}
	Welch Two Sample t-test

data:  x and y
t = 0.8916, df = 32.443, p-value = 0.3792
alternative hypothesis: true difference in means is not equal to 0
95 percent confidence interval:
 -1.964907  5.027061
sample estimates:
 mean of x  mean of y 
 0.4192239 -1.1118530 
{% endcodeblock %}

注意第一行的检测名称跟之前不一样了。

如果想要假设两个样本的方差是否一样，可以考虑使用 F-test，在 R 里是 `var.test` 函数。

## 案例

### 背景
父母的学历（只算最高一方）会影响孩子的成绩吗？研究者搜集了相关数据：

{% codeblock lang:r %}
colleage_grads <- c(485, 534, 650, 554, 550, 572, 497, 592, 487, 533, 526, 410, 515, 578, 448, 469)
high_school_grads <- c(442, 580, 479, 486, 528, 524, 492, 478, 425, 485, 390, 535)
{% endcodeblock %}

注意，上面两组 vector 的长度不一样。

针对研究问题，我们的原假设是：

H0：父母是高中学历的学生的考试成绩均值 = 父母是大学学历的学生的考试成绩均值
Ha：父母是高中学历的学生的考试成绩均值 != 父母是大学学历的学生的考试成绩均值

我们选定显著性水平为 5%。

### 数据可视化

在做进一步研究前，我们应该先看看两组数据的分布：

{% codeblock lang:r %}
# 整理做图需要的 long format
df <- data.frame(
  value = c(colleage_grads, high_school_grads),
  parent_deg = c(
    rep('大学', length(colleage_grads)),
    rep('高中', length(high_school_grads))
  )
)

# 使用 ggplot2 做图
ggplot(df, aes(x = parent_deg, y = value)) + 
  geom_boxplot() +
  theme_classic(14) + 
  labs(x = '父母最高学历',
       y = '小孩的SAT成绩')
{% endcodeblock %}

图片如下：

{% asset_img boxplot.png  "boxplot" %}

可以看到“大学”组的 box 比“高中”组更高，但这个差距是否具有统计显著性呢？

### 检验

{% codeblock lang:r %}
t.test(colleage_grads, high_school_grads)  
{% endcodeblock %}


{% codeblock lang:r mark:4 %}
	Welch Two Sample t-test

data:  colleage_grads and high_school_grads
t = 1.8038, df = 25.34, p-value = 0.08317
alternative hypothesis: true difference in means is not equal to 0
95 percent confidence interval:
 -5.359164 81.359164
sample estimates:
mean of x mean of y 
      525       487 
{% endcodeblock %}

p-value = 0.08，大于我们商量好的显著性水平 0.05，所以我们无法拒绝原假设。

虽然从图片来看，学历会对小孩成绩有影响，但数据表现出的差异并不能通过显著性检验。我们无法做出“父母学历会对小孩成绩有影响”的判断。

这个例子很好，它说明了一个重要的道理：**不要仅仅依靠数据可视化做判断**。

> 数据可视化代替不了统计检验。
