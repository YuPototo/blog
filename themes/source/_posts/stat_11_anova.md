---
title: 统计推断之 anova
date: 2019-01-01 10:49:45
categories:
  - 统计学
tags:
  - R
  - Statistics
---

T 检验用来检验1组或2组总体的均值差异，但不能用来检验3组或更多总体的均值差异。

想要检验3组或更多总体的差异，我们需要 anova：analysis of variance。

这篇文章介绍如何用 R 做 anova。

<!-- more -->

## 原假设和模型前提

Anova 的原假设和备择假设分别是：

H0:各个组的均值一样
Ha:至少一组的均值跟其他组不一样

Anova 需要满足如下前提：
- 随机抽样
- 总体符合正态分布
- 各个总体同反差

## 案例

某市餐饮协会想调查本市不同类型餐馆的价格水平，锁定了3种餐馆：
- 中餐
- 西餐
- 日式料理

该协会随机抽样了若干餐馆，并调查了人均消费，结果如下：

| 中餐 | 西餐 | 日式料理 |
|:------------:|:------------:|:-------------:|
|      12      |      16      |      24       |
|      13      |      18      |      19       |
|      15      |      17      |      23       |
|      17      |      26      |      25       |
|      18      |      23      |      21       |
|      20      |      15      |      22       |
|      17      |      19      |      27       |
|      24      |      18      |      31       |

### 数据

先把数据整理成需要的形式：

{% codeblock lang:r %}
chinese_food <- c(12, 13, 15, 17, 18, 20, 17, 24)
western_food <- c(16, 18, 17, 26, 23, 15, 19, 18)
japanese_food <- c(24, 19, 23, 25, 21, 22, 27, 31)

price_vec <- c(chinese_food, western_food, japanese_food)
group_vec <- c(rep('chinese', length(chinese_food)),
               rep('western', length(western_food)),
               rep('japanese', length(japanese_food)))

df <- data.frame(price = price_vec, group = group_vec)
{% endcodeblock %}

### 查看均值和方差

第一件事情是查看各个组的均值和方差：

{% codeblock lang:r %}
df %>%
  group_by(group) %>%
  summarize(mean = mean(price),
            sd = sd(price))
{% endcodeblock %}


|group    | mean|       sd|
|:--------|----:|--------:|
|chinese  |   17| 3.854496|
|japanese |   24| 3.741657|
|western  |   19| 3.703280|

### 可视化

然后可以把三个组的分布用 boxplot 展示：

{% codeblock lang:r %}
ggplot(df, aes(x = group, y = price)) +
  geom_boxplot() +
  theme_classic(14) +
  xlab('')
{% endcodeblock %}

{% asset_img box_plot.png  "boxplot" %}

### anova

最后是做 anova 分析（我们这里省略了 anova 假设是否满足的判断）。

{% codeblock lang:r %}
fit <- aov(price ~ group, data = df)
summary(fit)
{% endcodeblock %}

结果如下：

{% codeblock lang:r line_number:false %}
           Df Sum Sq Mean Sq F value  Pr(>F)   
group        2    208  104.00   7.329 0.00385 **
Residuals   21    298   14.19                   
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
{% endcodeblock %}

可以看到 p-value = 0.00385，小于0.01，我们可以拒绝原假设。

### pairwise comparison

我们接受了备择假设：至少一组的均值跟其他组不一样。
那么问题来了：两组之间的关系是怎样的呢？一共有三个组，存在三个配对关系：
- 中餐 vs. 西餐
- 中餐 vs. 日式料理
- 日式料理 vs. 西餐

我们确定了至少一组的均值有差别，现在我们想明确知道到底是哪些组存在均值差异。

这时可以使用 Tukey's test：

{% codeblock lang:r %}
TukeyHSD(fit)
{% endcodeblock %}

结果：

{% codeblock lang:r line_number:false %}
  Tukey multiple comparisons of means
    95% family-wise confidence level

Fit: aov(formula = price ~ group, data = df)

$group
                 diff       lwr        upr     p adj
japanese-chinese    7  2.252475 11.7475254 0.0035046
western-chinese     2 -2.747525  6.7475254 0.5474395
western-japanese   -5 -9.747525 -0.2524746 0.0378404
{% endcodeblock %}

上面展示了每一组之间的关系：
- diff 是两组之间的均值差异
- lwr 和 upr 分别是置信区间的上限和下限
- p adj 是 p 值

可以看到第1组和第3组的 p 值都小于0.05，说明存在两组差异：
- 日式料理 vs 中餐
- 日式料理 vs 西餐

我不喜欢看上面那样的表格，信息获取的难度有点大。

`TukeyHSD`返回的对象可以用`plot`方法做图。

我更喜欢 ggplot 生成的图片，下面是用 ggplot 做图的代码：

{% codeblock lang:r %}
# 清理数据
pair_test <- TukeyHSD(fit)

pair_test_df <- as.data.frame(pair_test$group)

pari_test_df_2 <- pair_test_df %>%
  rename(p_value = `p adj`) %>%
  mutate(pair = row.names(.)) %>%
  mutate(sig = cut(p_value, c(0, 0.05, 1),  label=c("p<0.05","Non-Sig")))

# 做图
ggplot(pari_test_df_2, aes(color = sig)) +
  geom_hline(yintercept=0, lty="11", colour="grey30") +
  geom_errorbar(aes(pair, ymin=lwr, ymax=upr), width=0.2) +
  geom_point(aes(pair, diff)) +
  coord_flip() +
  labs(colour = NULL, 
       x = NULL,
       y = "均值差",
       title = '95% 置信区间') +
  theme_classic(16) +
  theme(plot.title = element_text(hjust = 0.5),
        text=element_text(family="Songti TC Regular"),
        legend.position = 'top')
{% endcodeblock %}

生成图片如下：

{% asset_img confidence_interval.png  "confidence_interval" %}

这个图片就能清晰地看到置信区间和 p 值。
