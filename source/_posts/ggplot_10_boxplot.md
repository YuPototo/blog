---
title: ggplot系列之10：boxplot
date: 2018-07-15 09:07:33
categories:
  - 数据可视化
tags:
  - R
---

{% asset_img plot_2.png "各大俱乐部的工资结构" %}

数据可视化的常见场景之一是：展示一个 categorical variable 和一个 numeric variable 的关系。

这时我们可以使用 boxplot。

这里使用了 [FIFA 的球员数据集](https://www.kaggle.com/karangadiya/fifa19/data)。

<!-- more -->

## 大球队的工资状况如何？

我们想展示几个大球队的工资状况，涉及两个变量：
- 球队：categorical variable
- 每个球员的工资：numerical variable

我们先提取几个大俱乐部的数据，并把周薪修改为万欧：

{% codeblock lang:r %}
# 需要的俱乐部
clubs <- c('Real Madrid', 'FC Barcelona', 
           'Manchester United', 'Manchester City', 'Liverpool', 'Chelsea', 'Arsenal',
           'Borussia Dortmund', 'FC Bayern München')


wage_to_number <- function(wage) {
  # 把周薪转换成万欧
  number_in_string <- str_extract(wage, '\\d+')
  number_in_1000 <- as.numeric(number_in_string)
  number_in_1000 * 1000 / 10000
}

sub_dat <- dat %>%
  filter(Club %in% clubs) %>%
  mutate(Wage = wage_to_number(Wage))
{% endcodeblock %}

下面制作 boxplot：

{% codeblock lang:r %}
ggplot(sub_dat) +
  geom_boxplot(aes(x = Club, y = Wage)) +
  labs(x = NULL,
       y = '工资（周薪：万欧）',
       title = '各大俱乐部的工资结构') +
  coord_flip() 
{% endcodeblock %}

结果如图：

{% asset_img plot_0.png "各大俱乐部的工资结构" %}

从这个图可以看到：
- 西超双雄、曼城和尤文图斯的工资中位数最高
- 多特蒙德的工资水平最低，确实是这几个队里经济实力最低的
- 利物浦、切尔西、阿森纳、拜仁在维持相对均衡的收入的同时，还允许超级巨星有高收入
- 巴塞罗那有一个收入最高的家伙，不用想，当然是梅西啦

## 我们想标注出几个离群值

有几个球队有收入远远高于队友的球员，我们想看看他们究竟是谁（虽然猜也能猜到了 :blush:)，并把他们的名字标注在图片上。

先清理数据：

{% codeblock lang:r %}
outliner_club <- c('FC Barcelona', 'Liverpool', 'Chelsea', 'Arsenal', 'FC Bayern München')
outliner_player <- sub_dat %>% 
  filter(Club %in% outliner_club) %>%
  group_by(Club) %>%
  filter(Wage == max(Wage))
{% endcodeblock %}

我们提取出了几个有离群值的俱乐部收入最高的球员。

然后，我们在图片上添加 label：

{% codeblock lang:r %}
ggplot(sub_dat, aes(x = Club, y = Wage)) +
  geom_boxplot() +
  geom_label(data = outliner_player,
             aes(label = Name),
             nudge_x = 0.25,
             nudge_y = 0.25, 
             label.size = 0.1) +
  labs(x = NULL,
       y = '工资（周薪：万欧）',
       title = '各大俱乐部的工资结构') +
  coord_flip() 
{% endcodeblock %}


结果如下：
{% asset_img plot_1.png "各大俱乐部的工资结构+离群值" %}

各个全队的异常高收入球员如下：
- 巴萨：梅西
- 切尔西：C罗
- 拜仁：J罗
- 利物浦：萨拉赫
- 切尔西：阿扎尔
- 阿森纳：奥巴梅杨

有意思的是，利物浦的最高薪球员萨拉赫，薪资水平在皇马也只属于前1/4，在巴萨甚至不到梅西的一半。可见西超双雄的购买力了。


## 给曼联上色

作为一个曼联球迷，我想给曼联的 box 涂上曼联红色，然后给死敌利物浦涂上他们死敌埃弗顿的蓝色 :smiley:，我该怎么办呢？

{% codeblock lang:r %}
sub_dat <- sub_dat %>%
  mutate(isManU = case_when(
    Club == 'Manchester United' ~ 'ManU',
    Club == 'Liverpool' ~ 'Liv',
    TRUE ~ 'rest'
    ))

ggplot(sub_dat) +
  geom_boxplot(aes(x = Club, y = Wage, fill=isManU)) +
  scale_fill_manual(breaks = c("Liv", "ManU", "rest"), 
                    values=c("#003399", "#DA291C", "white")) +
  labs(x = NULL,
       y = '工资（周薪：万欧）',
       title = '各大俱乐部的工资结构') +
  coord_flip()  +
  theme(legend.position = "none")
{% endcodeblock %}

结果如下图：
{% asset_img plot_2.png "各大俱乐部的工资结构" %}


希望利物浦球迷看到了不要打我 :bomb: