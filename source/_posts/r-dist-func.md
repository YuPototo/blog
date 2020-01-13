---
title: R 语言里常用的分布函数
date: 2020-01-14 00:59:44
---

这篇博文小结 R 语言里涉及概率分布的几个函数。

每个概率分布函数在 R 里都有4个对应的函数，分别以1个字母开头：

- d：表示 density，指概率密度函数 probability density function (pdf)
- p：表示 probability，指累计分布函数 cumulative density function (cdf)
- q: 表示 quantile，是 cdf 的逆函数
- r：表示 random，符合该概率分布的随机数

如果你像我一样已经不记得概率论的老师长啥样，上个的一些名词应该会让你一头雾水。没关系，我们接下来就用 R 代码一一测试。

<!-- more -->

## 二项分布 Binomial Distribution

我们先以二项分布作为例子。二项分布是一个离散随机变量。

二项分布描述的问题是：每次试验有两个结果，成功或失败，n 次试验后成功 k 次的概率有多少？

R 语言里是 binom。

### dbinom

`dbinom(x, size, prob)` 就是计算概率的公式，其中：
- x：成功次数
- size：总试验次数
- prob：单次试验的成功概率

比如，扔5次硬币，正面2次的概率可以计算为：

{% codeblock R代码 lang:r %}
dbinom(2, size=5, prob=0.5) 
{% endcodeblock %}

结果：0.3125

### pbinom

`pbinom(q, size, prob)` 是计算累计概率的函数，其中 q 是 quantile。

例子：扔5次硬币，最多有1次正面的概率是对少？(即0次或1次)

{% codeblock R代码 lang:r %}
pbinom(1, size=5, prob=0.5) 
{% endcodeblock %}

答案：0.1875。

我们可以用 `dbinom(0, 5, 0.5) + dbinom(1, 5, 0.5)` 来做验证


### qbinom

`qbinom` 是 `pbinom` 的逆函数。我可以计算累计概率为 p 的对应 quntile 是多少。

使用刚才的0.1875去计算：

{% codeblock R代码 lang:r %}
qbinom(0.1875, size=5, prob=0.5) 
{% endcodeblock %}

我们应该得到 1。

### rbinom

`rbinom(n, size, prob)` 给出 n 个符合二项分布的随机数。

我们刚才计算了扔5次硬币时，成功2次的概率是 0.3125。

现在我们分别生成100、1000和10000个随机数，看看 x = 2 的频率是不是接近这个数字。

{% codeblock R代码 lang:r %}
set.seed(123)
sum(rbinom(100, 5, 0.5) == 2) / 100  # 0.28
sum(rbinom(1000, 5, 0.5) == 2) / 1000   # 0.294
sum(rbinom(10000, 5, 0.5) == 2) / 10000  # 0.3139
{% endcodeblock %}

可以看到，随着随机次数的增加，成功2次的频率越来越接近理论值。


## 正态分布

正态分布是一个连续随机变量。

### dnorm

dnorm(x) 计算 x 的对应概率密度函数值。这个函数用处不大。因为连续随机变量在某一点的概率为 0。

这个函数的唯一用处，大概是方便大家画出概率密度函数图。

### pnorm

`pnorm(5)` 实际上就是  P(X <= 5)，计算了 X 小于等于5的概率。

所以，`pnorm(0)` 的结果是0.5，标准正态分布的一半嘛。

如果想计算期望等于1，标准差等于2的正太分布在 -3 < x <= 0 的概率呢？

{% codeblock R代码 lang:r %}
pnorm(0, 1, 2) - pnorm(-3, 1, 2)
{% endcodeblock %}

结果：0.2857874

### qnorm

`qnorm` 是 `pnorm` 的逆函数。
因为 pnorm(0)等于0.5，所以 qnorm(0.5)肯定等于0，不信你试试 :smirk:

### rnorm

`rnorm` 生成一组符合正态分布的随机数。

我们可以生成1000个数据，然后画图看看效果。

{% codeblock R代码 lang:r %}
n1000 <- rnorm(1000, mean = 70, sd = 5)
hist(n1000, breaks = 20)
{% endcodeblock %}

确实很符合正态分布。

{% asset_img hist.png "直方图-例子" %}

`rnorm` 是个好伙伴，在 simulation study 时用得非常多。
