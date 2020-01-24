---
title: ggplot系列之7：别用漏斗图
date: 2017-07-20 06:33:24
categories:
  - 数据可视化
tags:
  - R
---


分析用户的转化路径时，经常需要使用漏斗图。但是，漏斗图并不是多好的数据可视化方案。

漏斗图的问题跟饼图有点相似：用面积表示数量多少，而人类视觉对面积的敏感度不够。

下面这张图是我从网上找到的漏斗图，我会用同样的数据做一个**柱状图**版本。

{% asset_img funnel.png  "funnel" %}


<!-- more -->

## 准备数据

原图展示了销售的四个步骤各自有多少人。

我们先准备一份数据：

{% codeblock lang:R %}
df <- data.frame(
  stage = c('Prospects', 'Price Quotes', 'Negotiations', 'Closed Sales'),
  people = c(12000, 6800, 3000, 850)
)
{% endcodeblock %}

## 做图

漏斗图有个值得借鉴的设计思路：从上到下展示数据的阶段性变化。我们想在柱状图上借用这个设计。

### 基本版

先画一个基本版

{% codeblock lang:R %}
ggplot(df, aes(x = stage, y = people)) + 
  geom_bar(stat = 'identity', width = 0.7) +
  geom_text(aes(label = people), hjust= -0.1) +
  scale_y_continuous(limits = c(0, 13000)) +
  labs(title = '销售漏斗图',
       y = '人数',
       x= NULL) +
  coord_flip() +
  theme_classic(16) 
{% endcodeblock %}

几个要点：
- 使用 `coord_flip` 可以把柱状图变成横向
- 使用`geom_text`标注人数
- 使用`theme_classic` 主题，减少画面中的元素。

结果如图：

{% asset_img plot_bar_1.png  "bar_1" %}

### 加入百分比

有的漏斗图会显示每个步骤还剩下多少比例的人数。我也想借用这个思路。

先加工数据：

{% codeblock lang:R %}
df_2 <- df %>%
  mutate(pct = str_c(round(people / max(people) * 100), '%'))
{% endcodeblock %}

我新建了一列，计算各个阶段相对于最开始人数的百分比。

然后做图：

{% codeblock lang:R %}
ggplot(df_2, aes(x = stage, y = people)) + 
  geom_bar(stat = 'identity', width = 0.7) +
  geom_text(aes(label = people), hjust= 1.1, color = 'white') +
  geom_text(aes(y = max(people) * 1.05, label = pct), hjust= 0) +
  scale_y_continuous(limits = c(0, 13000)) +
  labs(title = '销售漏斗图',
       y = '人数',
       x= NULL) +
  coord_flip() +
  theme_classic(16)
{% endcodeblock %}

跟之前相比有两个变化：
- 人数移到柱子内部，调整为白色
- 新增一个 `geom_text`，用于展示百分比

结果如下：

{% asset_img plot_bar_2.png  "bar_2" %}

### 视觉调整

仔细看漏斗图，我发现漏斗图还有一个好处：图片内的元素很少，让人感觉舒服。

参考漏斗图，我们修改自己的柱状图设计。

代码：

{% codeblock lang:R %}
# p1 是上一个图片
p1 + theme(axis.title.x=element_blank(),
           axis.text.x=element_blank(),
           axis.ticks =element_blank(),
           axis.line = element_blank(),
           plot.title = element_text(hjust = 0.3)) +
  scale_y_continuous(limits = c(0, 14500), expand = c(0,0))
{% endcodeblock %}

要点：
- 隐藏 x 轴和 y 轴的线、刻度
- 图片的标题相对居中
- 减少柱子和 y 轴之间的 padding

结果如下：

{% asset_img plot_bar_3.png  "bar_3" %}

能够修改的地方还有很多，比如柱子是不是太短太粗，颜色是不是可以优化，百分比的展示是不是可以更明确。这里就不再细究了。

## 参考

- [Bad Graphics – Funnel Chart](https://peltiertech.com/bad-graphics-funnel-chart/)
- [Funnel Charts Suck and You Shouldn’t Use Them](https://crmchartguy.com/2015/10/26/funnel-charts-in-dynamics-crm/)