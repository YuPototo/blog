---
title: 数据科学：用 R 还是 Python?
date: 2019-04-05 16:05:05
---

{% asset_img RorPython.png "R or Python" %}


数据科学届最常见的一个争论是：R 和 Python，哪个更好呢？

我不太喜欢这个问题，因为“好”的标准实在太多，不同场景有不同的标准。就拿这个话题来说，初学者为了了解统计学，最合适的肯定是 R；做人工智能的团队为了使用 Tensorflow 等开源工具，最合适的肯定是 python。

所以在讨论这个话题之前，我先限定好场景：**一个大公司内需要进行庞大业务分析的数据团队。在这个场景内，应该使用哪个工具呢？**

我的方案是：R 和 python 都使用，但在不同的地方使用。

<!-- more -->

## python

python 明显强过 R 的地方有三点：
- 生态：python 的各种 library 实在是太强大了
- 机器学习和人工只能
- python 是更“优雅”的语言


先说生态。如果我想做 http 访问，我会使用 python 的 requests 包。如果我想写爬虫，我会使用 python 的 scrapy 包。如果我想连接 SQL 数据库，我会使用 SQLAlchemy 包.....所有计算机工作，几乎都能在 python 上找到对应的工具。而且，这些工具大概率比 R 语言的 library 要强大。毕竟，给 R 语言写工具的，一般是统计学家，给 python 写工具的，一般是 coder，后者的工具开发能力、文档的可读性都强于前者。

再说机器学习和人工智能，python 是这个领域的默认语言，tensorflow、pytorch 等开源方案都优先支持 python。

Python 创建之初，就是在设计一个“简单易用”的语言（这里的“简单易用”要打一个双引号，因为牺牲了其他的东西，但这对数据分析影响不大）。写 python 代码是一种享受。阅读写得好的 python 代码也是一种享受。

## R

R 比 python 强大的地方也有两点：
- R是专门为统计学设计的语言
- 强大的统计生态

R 是专门为统计学设计的语言，python 是更底层的语言，所以做统计分析时，python 需要使用 pandas 等 package，而 R 可以快速上手，直接开始分析。想要使用 python 做数据分析，其实需要学习两个东西：python 和 pandas。

python 有强大的整体生态，R 则有强大的统计生态。至少在我写这篇文章时，统计学届的默认分析语言基本是 R 语言。世界上最新的统计方法，都能在 R 语言中找到。

另外，R 语言的数据清洗工具、数据可视化工具和数据报告工具都强过 python（当然，这是个人观点）。
- 数据清洗：Hadly Wickham 大神的 tidyverse 系列简直是人类智慧的结晶，这套工具集把数据清洗变成了一种享受。
- 数据可视化：还是 Hadly Wickham 大神，他的 ggplot 工具，帮助我们重新重构了数据可视化的思考模式。
- 数据报告工具：这要感谢中国的 Yihui 大神，他的 knitr、rmarkdown 等工具，让数据报告的产出变得更轻松、简单且科学规范。

## python 和 R 结合

说完了两者的优点，现在来说说如何结合两者。

我使用 python 的场合：
- 与数据库的沟通（代替 SQL 语句），因为 SQLAlchemy 的 API 写得非常好
- Restful API 访问，使用 requests 包
- 非结构化数据的获取和清洗
- 机器学习

我使用 R 的场合：
- 规范性数据的清洗
- 统计学假设检验
- 统计学建模
- 数据可视化和数据报告

上面的使用场景应该是最能发挥两者优势的。不过按照 python 生态的发展速度，也许很快 python 就会在统计学、可视化和报告产出上超过 R 语言。如果到了那一天，也许只使用 python 就够了。

## 如果必须二选一呢

如果必须2选1呢？这个时候当然要使用 python 了，原因：
- python 的底层生态实在是太棒了，使用过一次之后很难再愿意去用 R 的方案
- python 一直在高速发展，R 的优秀工具会很快被 python 复制

当然，学习 python 和 R 都挺容易的，如果只是做数据分析的话，会 python 的人一定可以在两周内掌握 R，反之也成立。

小孩子才做选择题，成年人全都要。

{% asset_img all.jpg "我全都要" %}

那么，excel 呢？都2019年了，严谨的数据分析团队还是不要把 excel 用来做分析工具了。我唯一能想到的 excel 应用场景，是用来“展示”报告。因为，有的决策者希望看到 excel，而不是一个 Word 文档。




