---
title: Test of Independence
date: 2020-01-23 19:26:24
categories:
  - 统计理论
tags:
  - R
---

在产品运营中，我们经常需要关心如下问题：

- F2P游戏：用户的是否付费与性别相关吗？
- 电子杂志：用户是否订阅与手机类型相关吗？
- 懂球帝 App：用户是否推荐新闻与自己的球队是否赢球相关吗？

以上问题有个共性：判断两个 categorical variable 是否相关。

这个时候，我们可以用 chi-square test 做 test of independence。

<!-- more -->


## 例子

某杂志调查了公务出行人群在出差时的航班选择，得到数据如下：


|       | 国内航班 | 国际航班 |
|:------|:--------:|:--------:|
|头等舱 |    29    |    22    |
|商务舱 |    95    |   121    |
|经济舱 |   518    |   135    |

提问：旅行目的地与机票类型相互独立吗？

假设检验如下：
- H0:旅行目的地与机票类型相互独立
- Ha：旅行目的地与机票类型不相互独立

我们选定的显著性水平是 5%。

### 数据

我们先生成需要的数据：

{% codeblock lang:r %}
ma <- matrix(data = c(29, 95, 518, 22, 121, 135), nrow = 3)
df <- data.frame(ma)
names(df) <- c('国内航班', '国际航班')
row.names(df) <- c('头等舱', '商务舱', '经济舱')
{% endcodeblock %}

在 R 里做 chi-square test 可以直接用 matrix 对象，但我们还想做图看看，所以生成了一个 data.frame。

### 做图

按照一般步骤，我们可以先做个柱状图看看。

先把数据变成 ggplot 需要的 long format。
{% codeblock lang:r %}
df_long <- df %>%
  mutate(ticket_type = row.names(df)) %>%
  gather('flight_type', 'value', -ticket_type)
{% endcodeblock %}

然后做图：
{% codeblock lang:r %}
ggplot(df_long, aes(x = ticket_type, y = value, fill = flight_type)) + 
  geom_bar(stat = 'identity', position = 'dodge') +
  scale_x_discrete(limits = c('头等舱', '商务舱', '经济舱')) +
  labs(x = NULL, y = NULL, fill = NULL) +
  theme_classic(14) +
  scale_fill_brewer(palette='Set2')
{% endcodeblock %}

如下图：

{% asset_img plot_bar.png  "plot" %}

可以看到对经济舱来说，国内航班的数量明显高于国内航班。


我们还可以从另一个视角查看（代码略）：

{% asset_img plot_2.png  "plot" %}

这里能更清晰地看到，国内航班跟国外航班的“图片形状”不太一样。

我们可以推测出：两个变量不独立。接下来我们做统计检验。

### 检验

这里略过 [chi-squre test 的数学推导](https://online.stat.psu.edu/stat414/node/312/)，直接做检验。

{% codeblock lang:r %}
chisq.test(ma)
{% endcodeblock %}

结果：p-value < 2.2e-16。即使显著性水平是1%，我们也可以拒绝原假设。

我怀疑是因为国内经济舱的数量太多，才导致两个变量不独立。假设国内经济舱的数量从518变成145， 这个检验还能拒绝原假设吗？

{% codeblock lang:r %}
ma_2 <- matrix(data = c(29, 95, 145, 22, 121, 135), nrow = 3)
chisq.test(ma_2)
{% endcodeblock %}

结果：p-value = 0.1164，不能拒绝原假设。跟我们的推测一致。

## 优化一下柱状图

这是一个跟 test of independence 不相关的话题：感觉这里的柱状图可以用极简风格优化一下。

### 代码

{% codeblock lang:r %}
ggplot(df_long, aes(x = ticket_type, y = value, fill = flight_type)) + 
  geom_bar(stat = 'identity', position = 'dodge') +
  geom_text(aes(label = value), 
            color = '#444444',
            position = position_dodge(0.9),
            vjust = -0.2) +
  scale_x_discrete(limits = c('头等舱', '商务舱', '经济舱')) +
  scale_y_continuous(expand = c(0, 0), limits = c(0, 540)) +
  labs(x = NULL, 
       y = NULL, 
       fill = NULL) +
  theme_classic(14) +
  theme(legend.position = 'top',
        axis.ticks = element_blank(),
        axis.line = element_blank(),
        axis.text.y = element_blank()) +
  scale_fill_brewer(palette='Set2') 
{% endcodeblock %}

做了如下几个事情：
- 删除 x 轴的 axis.line, axis.ticks
- 删除 y 轴
- 缩短 bar 和 x 轴之间的 padding
- 在柱子上增加表示数量的 text
- legend 移动到顶部

### 效果

{% asset_img plot_3.png  "plot" %}

还不错 :smiley: