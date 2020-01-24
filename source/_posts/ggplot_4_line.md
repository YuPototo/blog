---
title: ggplot系列之4：折线图
date: 2017-04-01 20:35:36
categories:
  - 数据可视化
tags:
  - R
---

{% asset_img plot_example.png "折线图" %}


折线图，经常用来展示时间序列数据。

<!-- more -->

## 准备数据

这里使用 R 自带的 [UKLungDeaths](https://stat.ethz.ch/R-manual/R-devel/library/datasets/html/UKLungDeaths.html) 数据，记录了英国1974到1979年每个月因为肺病死亡的人数。

源数据是 ts 格式，需要现转换成 ggplot 需要的 data.frame 格式，使用 [tsbox 包](https://cran.r-project.org/web/packages/tsbox/vignettes/tsbox.html)。

{% codeblock lang:r %}
# 用于基础做图
df <- ts_df(ts_c(ldeaths))

# 用于分组做图
df_2 <- ts_df(ts_c(fdeaths, mdeaths))
names(df_2)[1] <- 'gender'
df_2 <- df_2 %>%
  mutate(gender = ifelse(gender == 'fdeaths', 'female', 'male'))
{% endcodeblock %}

这里准备了两个 data.frame。第一个 df 是每月因为肺病死亡的总人数，第二个 df 是分性别的死亡人数。

第1组数据的前几行:

|    time    | value |
|:----------:|:-----:|
| 1974-01-01 | 3035  |
| 1974-02-01 | 2552  |
| 1974-03-01 | 2704  |
| 1974-04-01 | 2554  |
| 1974-05-01 | 2014  |
| 1974-06-01 | 1655  |


第2组数据的前几行：

|    time    | value | gender |
|:----------:|:-----:|:------:|
| 1974-01-01 |  901  | female |
| 1974-02-01 |  689  | female |
| 1974-03-01 |  827  | female |
| 1974-04-01 |  677  | female |
| 1974-05-01 |  522  | female |
| 1974-06-01 |  406  | female |

第2组数据多了一个表示性别的变量`gender`。

## 基本折线图

先画一个每月总死亡人数的图：

{% codeblock lang:r %}
df %>%
  ggplot(aes(x = time, y = value)) + 
  geom_line() +
  scale_x_date(date_breaks = '2 years',
               date_labels = "%Y-%m") +
  labs(title = '1974-1979年英国死于肺病的人数/每月',
       x = '月份',
       y = '死亡人数')
{% endcodeblock %}

`geom_line` 是画折线图的函数。

这里还出现了一个常用的方法`scale_x_date`，处理时间格式时很方便。我用 `date_breaks = '2 years'` 表示 x 轴的标签每隔两年显示，然后用`date_labels = '%Y-%m'`将显示的格式设定为"xxxx年-xx月"。

做图结果如下：
{% asset_img plot_0.png "折线图" %}

## 分组折线图

有时我们需要对不同的组别画折线：

{% codeblock lang:r %}
df_2 %>%
  ggplot(aes(x = time, y = value, color = gender)) + 
  geom_line() +
  scale_x_date(date_breaks = '2 years',
               date_labels = "%Y-%m") +
  labs(title = '1974-1979年英国死于肺病的人数/每月',
       x = '月份',
       y = '死亡人数',
       color = '性别')
{% endcodeblock %}

这里的关键是第2行的`color = gender`。

做图结果如下：
{% asset_img plot_1.png "折线图" %}


## 折线图 + 点

有时我们想要同时展示折线和点：

{% codeblock lang:r %}
df_2 %>%
  ggplot(aes(x = time, y = value, color = gender)) + 
  geom_line() +
  geom_point() + 
  scale_x_date(date_breaks = '2 years',
               date_labels = "%Y-%m") +
  labs(title = '1974-1979年英国死于肺病的人数/每月',
       x = '月份',
       y = '死亡人数',
       color = '性别')
{% endcodeblock %}

加上 `geom_point` 即可。

做图结果如下：
{% asset_img plot_2.png "折线图" %}