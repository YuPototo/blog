---
title: ggplot系列之5：柱状图
date: 2017-05-06 20:25:12
categories:
  - 数据可视化
tags:
  - R
---

{% asset_img plot_2.png "带数字的柱状图" %}

柱状图，个人认为最实用的可视化图形。这里总结如何在 ggplot2 里使用柱状图。

<!-- more -->

## 默认柱状图

先生成一份简单的数据：

{% codeblock lang:r %}
df <- data.frame(
  user_num = c(3000, 5000, 7000),
  version = factor(c('version 1', 'version 2', 'version 3'))
)
{% endcodeblock %}

我们生成了某 app 不同版本下的用户数据，我们需要展示这个信息。

{% codeblock lang:r %}
ggplot(data=df, aes(x=version, y = user_num)) +
  geom_bar(stat="identity", width = 0.5) +
  labs(title = '各个版本的用户数量',
       x = '版本',
       y = '用户数')
{% endcodeblock %}


最重要的代码是第7行的`geom_bar`，其中`stat="identity"`指直接用 data.frame 里的数字作为 y 轴的值。`width` 是柱子的宽度。

结果如下：

{% asset_img plot_0.png "初级柱状图" %}


## 进阶版：分组

app 的用户数据可以按照各个维度做拆分，最常用的一个维度是性别。我们把用户数拆分为不同性别。

{% codeblock lang:r %}
df_2 <- data.frame(
  user_num = c(1600, 1400, 3000, 2000, 4000, 3000),
  version = factor(c('version 1', 'version 1', 'version 2',  'version 2', 'version 3', 'version 3')),
  gender = factor(c('male', 'female', 'male', 'female', 'male', 'female'))
)
{% endcodeblock %}

然后我们展示分组柱状图。

{% codeblock lang:r %}
ggplot(data=df_2, aes(x=version, y = user_num, fill = gender)) +
  geom_bar(stat="identity", width = 0.5, position = 'dodge') +
  scale_fill_brewer(palette="Set2") +
  labs(title = '各个版本的用户数量',
       x = '版本',
       y = '用户数',
       fill = '性别') 
{% endcodeblock %}

我在第一行用了`fill = gender`，实现了对不同组填色。
然后在第二行用了`position = 'dodge'`，实现了对把不同组的柱子并排展示。如果不使用这个参数，柱子会叠加在一起展示。
为了替换默认颜色，我在第三行用了`scale_fill_brewer(palette="Set2")`，这里的 `Set2`是调色板名称。

结果如下：

{% asset_img plot_1.png "分组柱状图" %}


## 在柱子上显示数量

有时为了更清晰地展示信息，我们需要在柱子上展示数字。

这时需要用到 `geom_text()`:

{% codeblock lang:r %}
ggplot(data=df_2, aes(x=version, y = user_num, fill = gender)) +
  geom_bar(stat="identity", width = 0.8, position = 'dodge') +
  geom_text(aes(label = user_num), position = position_dodge(0.8), vjust = -0.2) +
  scale_fill_brewer(palette="Set2") +
  labs(title = '各个版本的用户数量',
       x = '版本',
       y = '用户数',
       fill = '性别') 
{% endcodeblock %}

第3行就是 `geom_text()` 命令，我们令文字内容等于`user_num`。

结果如下：

{% asset_img plot_2.png "带数字的柱状图" %}

更多柱状图控制，可以参考 ggplot2 的文档。
