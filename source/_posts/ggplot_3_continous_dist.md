---
title: ggplot系列之3：查看一组连续变量的分布
date: 2018-05-25 17:40:26
categories:
  - 数据可视化
tags:
  - R
---

{% asset_img plot_4.png "直方图-例子" %}


我们拿到了一个调查数据，每个用户输入了自己的年龄，我们想查看用户的年龄分布。这个问题场景其实可以抽象为：**查看一个连续变量的分布情况**。这时，我们可以使用直方图（histogram plot）或密度图（density plot）。

这篇文章展示两种图片的代码，并讨论该选择哪一种图片。

<!-- more -->


## 模拟数据

我们先生成 simulated data：
- 变量1：性别，男或女
- 变量2：重量，符合正太分布
- 数量：男女各两百各观测值

{% codeblock lang:r %}
set.seed(1234)
wdata <- data.frame(
  sex = factor(rep(c("女", "男"), each=200)),
  weight = c(rnorm(200, 55), rnorm(200, 58)))
head(wdata)
{% endcodeblock %}


## histogram

histogram 把一组连续变量分成若干组，并展示各个组的频数。ggplot 可以对连续变量自动分组，我们也可以手动设定每一组的区间范围。具体使用多大的组距，要根据业务情况确定。

我们先做第一张图，不区分男女。

{% codeblock lang:r %}
ggplot(data = wdata) + 
  geom_histogram(aes(x = weight)) +
  labs(title = '体重分布',
       x = '重量（公斤）',
       y = '数量')
{% endcodeblock %}

结果如下图：

{% asset_img plot_1.png "直方图" %}


可以看到全部人群的重量分布有两个中心。如果这是一个正常的数据分析，我们会猜测可能有其他因素影响了分布，其中性别是最符合直觉的，所以这个时候我会对性别做一个分组，再画一张图。（虽然这里的数据是我直接 simulate 的，我已经提前知道答案了，但这个分析思路不会有改变）。

更新做图代码：

{% codeblock lang:r %}
ggplot(data = wdata) + 
  geom_histogram(aes(x = weight, fill = sex)) +
  labs(title = '体重分布',
       x = '重量（公斤）',
       y = '数量',
       fill = '性别')
{% endcodeblock %}


{% asset_img plot_2.png "直方图" %}


这个时候我们可以清晰地看到不同性别有自己的体重分布。



## density plot

density plot 按照一个公式把数据转换成了“密度”，它跟 histogram 的区别可以查看[这个回答](https://math.stackexchange.com/questions/2666834/what-is-the-difference-between-frequency-and-density-in-a-histogram)。

代码如下：

{% codeblock lang:r %}
ggplot(data = wdata) + 
  geom_density(aes(x = weight, fill = sex)) + 
  labs(title = '体重分布',
       x = '重量（公斤）',
       y = '数量',
       fill = '性别')
{% endcodeblock %}

细心的你会发现，我们只修改了一行代码，具体来说，我们把 `geom_histogram` 变成了 `geom_density`，非常方便地生成了我们需要的图片。这就是 ggplot 厉害的地方。

结果如下图：

{% asset_img plot_3.png "直方图" %}

可以看到，两个组别的正态分布更加明显。

## 用 histogram 还是 density 呢？

对比上面的两个图片，我们会看到：density 对趋势的展示更加明显。

我的思路如下：
- 在样本足够多的情况下，我会优先查看 density，然后查看 histogram
- 但如果样本量不足，用 density 可能会对数据做出**过度处理**，这个时候优先查看 histogram
- 如果数据需要跟外部团队沟通，优先展示 histogram，因为同事可能不太理解 density 的意思，而解释起来的沟通成本也非常高，这个时候，我永远选择最容易帮助沟通的方案。有些想展示自己专业性的分析师，可能会选择使用 density plot，这不是个对团队效率有帮助的习惯。

> 在两个方案能达到相同效果时，永远选择最利于沟通的方案，而不是最利于展示自己“实力”的方案。

上面这句话是我说的，也是我检验一个数据分析师是否合格的重要标准。当一个数据分析师优先展示自我而不是高效沟通时，他可能存在如下的问题：

- 缺乏沟通能力，甚至不是个 team player
- 缺乏成本意识，很难成为企业里足够好的 leader，毕竟，管理者最重要的工作之一是做投入产出分析
- 不真诚，甚至会有不诚实的嫌疑，这对数据工作是非常致命的

以上内容，也是我对自己的警醒。


## 引申：用 geom_area 实现类似 density 的效果

ggplot 还有一个 `geom_area` 函数，能实现类似直方图的功能。

我们试试用 `geom_area` 修改代码，并尝试使用另一个主题。代码如下：

{% codeblock lang:r %}
ggplot(data = wdata) + 
  geom_area(aes(x = weight, fill = sex), stat ="bin", alpha=0.6) +
  theme_classic() + 
  theme(text=element_text(size=20,  family="STFangsong"),
        plot.title = element_text(hjust = 0.5)) +
  labs(title = '体重分布',
       x = '重量（公斤）',
       y = '数量',
       fill = '性别') 
{% endcodeblock %}

结果如图：

{% asset_img plot_4.png "geom_area" %}

这是一个看起来很舒服的可视化数据了，相信也会让你的报告更有说服力。
