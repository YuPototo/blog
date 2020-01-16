---
title: 线性回归的假设
date: 2020-01-16 20:56:06
---

工作以后经常发现理论与实践的巨大落差，具体表现在：最应该尊重理论的数据工作者，竟然表现得像是完全不了解统计学的基本理论。我印象最深的例子是，一份使用了 linear regression 的报告完全没有对回归分析的假设做任何诊断。

这样的报告在大学作业里会被判断为不及格，但却成为了一个上市公司现金牛产品的决策依据。也难怪这个产品日后的收入逐渐减少了。

这篇文章小结一下 linear regression 的几个核心假设，就当是大学知识的一次复习了。

<!-- more -->

## 几个假设

1. 线性关系
1. 自变量之间不具有多重共线性 multicollinearity
1. 扰动项符合正态分布
1. 同方差 homoscedasticity
1. 没有自相关 auto-correlation：这种情况在非时间序列里很少见，所以我们不讨论了


## 1. 线性关系

第一个假设是最直接的，自变量和因变量之间是线性关系。

反例：` y = a + b * x ^ 3`，这就不是一个适合用线性回归拟合的关系，如果是1元回归，我们可以通过画散点图发现自变量与因变量之间是否符合线性关系。

如果是多元回归，我们可以使用 R 的 `lm` 对象自带的 `plot` 函数。这个函数的第1个图片：Residuals vs Fitted 能够用来检查线性假设。

simulated data 和拟合模型如下：

{% codeblock lang:r %}
size <- 30
x <- rnorm(size, 15, 10)

y1 <- 3 + 5 * x + rnorm(size, 5, 3)
y2 <- 3 + 5 * x^3 + rnorm(size, 5, 3)

fit_1 <- lm(y1 ~ x)
fit_2 <- lm(y2 ~ x)

p1 <- autoplot(fit_1)
p2 <- autoplot(fit_2)

p1_1 <- p1[[1]] + ggtitle('满足线性假设') + theme(plot.title = element_text(size = 18))
p2_1 <- p2[[1]] + ggtitle('不满足线假设') + theme(plot.title = element_text(size = 18))

ggarrange(p1_1, p2_1 + rremove('y.title'), ncol = 2, nrow = 1)
{% endcodeblock %}

我使用了 ggfortify 包处理 lm 的图片，并用 ggpubr 包把两个模型的图片并列在一起。

图片如下：

{% asset_img plot_1.png "线性假设" %}

当模型满足线性假设时，fitted value 和 residuals 的关系接近一条直线（如左图），右图则明显是一条非直线。

## 2. 多重共线性

要求：自变量之间没有**过于强**的线性关系。

有一些线性关系是没问题的，实际上在研究现实问题时，我们很难找到完全没线性关系的自变量。

比如研究收入的影响因素，我们会考虑智商和学历。智商越高，自然收入越高；学历越高，当然收入也越高。可是，学历也同时跟智商有线性关系。

只要这两个变量不是完全的线性关系，我们就不用担心。这两个因素当然不是完全线性关系，除了智商之外，家庭条件、考生的所处省份、个人兴趣等因素也跟学历有巨大关系。

我们可以查看所有自变量之间的相关性。ggplot 生态里有个非常好用的 `ggcorrplot` 包：

{% codeblock lang:r %}
library(ggcorrplot)
data(mtcars)
corr <- round(cor(mtcars), 1)
ggcorrplot(corr, hc.order = TRUE, type = "lower",
           outline.col = "white",
           ggtheme = ggplot2::theme_gray,
           colors = c("#6D9EC1", "white", "#E46726")) 
{% endcodeblock %}

结果如下图：

{% asset_img plot_2.png "多重共线性" %}

如果想更严格地描述多重共线性问题，可以参考一个叫 [variance inflation factor](https://en.wikipedia.org/wiki/Variance_inflation_factor) 的指标。

## 3. 扰动项符合正态分布

扰动项 error term 是一个理论概念：所有没有被观察到的影响因变量的因素，都算在扰动项里。扰动项是没有办法被观察到的，所以我们用残差 residuals 来模拟。残差是真实值和拟合值之间的差。

一般使用 Normal QQ 图来检查残差是否符合正态分布。

我们先 simulate 两组数据，然后对比它们的 QQ 图：

{% codeblock lang:r %}
size <- 100
x <- rnorm(size, 15, 10)
err <- rnorm(size, 5, 3)
  
y1 <- 3 + 5 * x + err
y2 <- 3 + 5 * x + err ^ 3

fit_1 <- lm(y1 ~ x)
fit_2 <- lm(y2 ~ x)

p1 <- autoplot(fit_1)
p2 <- autoplot(fit_2)

p1_2 <- p1[[2]] + ggtitle('扰动项正态分布') + theme(plot.title = element_text(size = 18))
p2_2 <- p2[[2]] + ggtitle('非正态分布') + theme(plot.title = element_text(size = 18))

ggarrange(p1_2, p2_2 + rremove('y.title'),
           ncol = 2, nrow = 1)
{% endcodeblock %}

我们让第1个模型的扰动项完美符合正态分布，让第2个模型第扰动项严重偏离正态分布。
得到的 QQ 图如下：

{% asset_img plot_3.png "扰动项符合正态分布" %}

在完美假设的情况下，所有的点几乎跟图中直线重合。右图中的点则严重偏离了图中直线。


## 4. 同方差

同方差假设的内容是：对不同的自变量值，扰动项的方差是相同的。如果违反了这个假设，就叫“异方差”。

典型的的异方差场景：研究收入和学历的关系，我们可以大概预料到，学历越高的人，收入的反差越大（有的人混得非常好，有的人混得一般）。

这是线性回归里非常重要的假设，我们可以用 `ncvTest()` 检测异方差。也可以在做出模型后做可视化检查，即使用 scale-location 图。

跟之前的思路一样，我在下面的代码里模拟一个正常模型和一个异方差模型。

{% codeblock lang:r %}
set.seed(123)
size <- 100
x <- rnorm(size, 15, 10)
err_1 <- rnorm(size, 5, 3)
err_2 <- (x -1)* rnorm(size, 5, 3)

y1 <- 3 + 5 * x + err_1
y2 <- 3 + 5 * x + err_2

fit_1 <- lm(y1 ~ x)
fit_2 <- lm(y2 ~ x)
{% endcodeblock %}

这里我们查看两个图，代码量不少，就不展示代码了。

第1个图是 scale-location 图：下图中同方差的情况是一条比较直的线。

{% asset_img plot_4.png "scale-location" %}

第2个图是 x 和对应残差的散点图：下图的异方差现象很明显，残差随着x的变大而变大。

{% asset_img plot_5.png "x和残差的散点图" %}


## 小结

线性回归模型的前提是模型满足假设，这是科学研究的规范。在 R 语言里查看这几个假设很简单，只需要在 `fit = lm(y ~ x)`使用`plot(fit)`就行了。
