---
title: ggplot系列之9：ggTimeSeries
date: 2018-04-30 06:33:41
categories:
  - 数据可视化
tags:
  - R
---

{% asset_img daily_heatmap.png  "heatmap" %}

今天看到一个有趣的 R pakcage：[ggTimeSeries](https://github.com/AtherEnergy/ggTimeSeries) 能够对时间序列数据做出有趣的图片。

<!-- more -->

## 日期热点图

我最喜欢的是下面这个日期热点图，它能够展示连续变量在一年里每天的数据情况，月份在 x 轴，工作日在 y 轴，数据大小用填充色表示。


我随便找了个每日温度数据，做出了最上面那个图，做图代码如下：

{% codeblock lang:R %}
# 基本图
p0 <- df %>%
  ggplot_calendar_heatmap('date','meantemp', dayBorderColour = '#cccccc', monthBorderColour = '#777777')

# 加工
p0 +
  scale_fill_continuous(low = 'green', high = 'red') + 
  facet_wrap(~Year, ncol = 1) +
  labs(x = NULL,
       y = NULL,
       fill = NULL,
       title = '每日平均气温') +
  theme_minimal() +
  theme(text=element_text(family="Songti TC Regular")) 
{% endcodeblock %}

这个展示方式很适合用来展示有工作日趋势和季度趋势的时间序列数据，比如每日销售、网站访问量。

寒假和暑假经常是游戏流量的高峰，这个信息就能在这个图片上一目了然。

工作日的游戏流量低于周末，也很适合在这个图表上展示。

## waterfall 图

这个 package 里还有一个我喜欢的图，waterfall 图：

{% asset_img gg_waterfall.png  "heatmap" %}

Waterfall 图用箭头标注了数据的每日变化，展示每天都有波动的数据很合适，比如股票价格等。

上图的做图代码：

{% codeblock lang:R %}
# 基本图
df %>%
  ggplot_waterfall('date','meantemp')

# 加工
p0 +
  labs(x = NULL,
       y = NULL,
       color = '变化',
       title = '每日平均气温') +
  theme_classic(16) +
  theme(text=element_text(family="Songti TC Regular"),
        plot.title = element_text(hjust = 0.5)) 

  theme(text=element_text(family="Songti TC Regular")) 
{% endcodeblock %}

这个 package 里还有其他几个可视化方法，个人觉得不太实用，有兴趣的同学可以去看看。
