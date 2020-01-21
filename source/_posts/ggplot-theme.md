---
title: ggplot系列之：theme
date: 2020-01-21 22:21:31
---

{% asset_img plot_all.png "带数字的柱状图" %}

$\alpha$

人靠衣装，图靠 theme 装。好看的数据可视化，会一眼抓住人眼球，让你的报告更有说服力。

ggplot 生态提供了丰富的 theme 选择，如果你对已有的 theme 不满意，还可以自己创造 theme。

这篇文章介绍几个不错的 theme。

<!-- more -->

## 数据和图片

先用 [Gapminder 数据](https://www.rdocumentation.org/packages/gapminder/versions/0.3.0)做一个基础图。

{% codeblock lang:R %}
df <- gapminder %>%
  filter(year == 2007)
{% endcodeblock %}

Gapminder 里有1950年代到2007年各国的预期寿命、人口、人均 GDP 等数据。

我们只看2007年的样本，前几列数据如下：

|   country   | continent | year | lifeExp |   pop    | gdpPercap  |
|:-----------:|:---------:|:----:|:-------:|:--------:|:----------:|
| Afghanistan |   Asia    | 2007 | 43.828  | 31889923 |  974.5803  |
|   Albania   |  Europe   | 2007 | 76.423  | 3600523  | 5937.0295  |
|   Algeria   |  Africa   | 2007 | 72.301  | 33333216 | 6223.3675  |
|   Angola    |  Africa   | 2007 | 42.731  | 12420476 | 4797.2313  |
|  Argentina  | Americas  | 2007 | 75.320  | 40301927 | 12779.3796 |
|  Australia  |  Oceania  | 2007 | 81.235  | 20434176 | 34435.3674 |

我比较感兴趣的是人均 GDP 和预期寿命的相关性。所以，我会画一个人均 GDP 和预期寿命的散点图。除此之外，这里还有人口数据和国家所属的大洲，为了展示更多信息，我们可以用点的大小表示人口，用颜色表示所属的大洲。

做图代码如下：

{% codeblock lang:R %}
p0 <- ggplot(data = df, 
             aes(x = gdpPercap, 
                 y = lifeExp, 
                 size = pop,
                 color = continent)) + 
  geom_point() +
  scale_x_continuous(trans = 'log',
                     labels = round) +
  labs(title = '人均gdp vs. 预期寿命',
       x = 'log(人均 GDP)',
       y = '预期寿命',
       color = '大洲',
       size = '人口',
       caption = "数据来源: gapminder")
{% endcodeblock %}

我怀疑人均 GDP 用 log 形式更能展示线性关系，所以这里对人均 GDP 数据做 log 处理。

## ggplot 的内置 theme

ggplot 内置了至少8个 theme，我选出了几个自己比较喜欢的。

### 默认: theme gray

ggplot 的默认 theme 是 theme_gray：

{% asset_img plot_1.png "theme_gray" %}

这个 theme 已经可以应付大部分场合了。

### theme dark

既然有 gray，当然就有 dark。theme_dark 会把底色变黑，适用于在黑暗的环境做报告。

{% asset_img plot_2.png "theme_dark" %}

### theme minimal

theme_minimal 是一个贯彻简单原则的 theme。

{% asset_img plot_3.png "theme_minimal" %}

### theme classic

theme_classic 是我的最爱，这应该是数据可视化最经典的主题。

{% asset_img plot_4.png "theme_classic" %}


## package：ggthemes

ggthemes 是一个包含了十数个主题的 package，这里介绍其中的几个。

### theme few

`theme_few` 也是个简单原则的主题。

{% asset_img plot_5.png "theme_few" %}


### theme economist
`theme_economist` 是个参考《经济学人》风格设计的主题。

{% asset_img plot_6.png "theme_economist" %}

### theme gdocs
`theme_gdocs` 是个参考 google docs 风格设计的主题。

{% asset_img plot_7.png "theme_gdocs" %}

### theme 538
`theme_fivethirtyeight` 是个参考数据网站 [fivethirtyeight](https://fivethirtyeight.com/) 设计的主题。

{% asset_img plot_8.png "theme_fivethirtyeight" %}

### theme_excel

package 作者出于搞笑目的做了一个经典 excel 主题。

{% asset_img plot_9.png "theme_excel" %}


作者在 [ggthemes 的文档](https://cran.r-project.org/web/packages/ggthemes/ggthemes.pdf)里写道：

> Theme to replicate the ugly monstrosity that was the old gray-background Excel chart. Please never
use this. 

:stuck_out_tongue:

## 修改主题

如果想要微调某个主题，需要用到 ggplot 的 `theme` 方法。

比如我们想修改默认主题：
- 标题和副标题剧中
- 标题改为红色、加粗
- 副标题改为深绿色p

我们可以这样做：

{% codeblock lang:R %}
# p1 是最开始画的图
p1 + theme(plot.title = element_text(color = 'red',  hjust = 0.5, size = 20),
           plot.subtitle = element_text(color = 'darkgreen', hjust = 0.5))
{% endcodeblock %}

结果如下：

{% asset_img plot_10.png "theme_edit" %}

这只是个说明性的例子，ggplot 提供了非常丰富的图片修改可能，图片上的任何“元素”都可以使用某个命令修改。
