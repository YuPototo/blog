---
title: 为什么数据团队应该采用 reproducible research
date: 2019-05-19 18:07:01
categories:
  - 数据科学
---

{% asset_img plot.jpg "plot" %}

Reproducible research （可重复研究）是学术界的一个潮流。它的意思是，所有研究都应该是可以“复制”的。如果你给我一个研究报告，你不能只给我看结果，你还要给我看到**整个分析过程**，而且我能够在自己的电脑上**复制**出同样的结果。

经历过几个公司的数据相关业务后，我深深地感觉到：这个规范不只应该用在科学研究里，它也应该用在商业公司的数据团队里。

<!-- more -->

## 传统的分析报告

为了简化讨论，我们假设数据已经自动搜集并存储在数据仓库中。我们先不讨论研究团队需要自己搜集数据的情况。

产品经理想了解一个问题：“我们为提升用户留存设计了一个新功能，这个新功能已经上线了1个月，它是否有效果呢？”

某分析师做了如下操作：
1. 从数据库提取数据，或从数据后台“下载”数据
1. 把数据导入 excel 中，开始清洗数据
1. 做某些假设检验、或模型分析
1. 把分析结果展示在 excel 中，或另外写一份 word 报告

在某些公司里，上面的做法已经“足够”了。但是，经历过学术训练的人会有如下问题：
- 怎么判断报告作者使用了“正确”的数据源？例子：提取数据的时候日期搞错了。
- 怎么判断作者正确清洗了数据？例子：float 变 int。
- 怎么判断作者做了需要做的检验？例子：使用 lm 模型却没检查假设是否能被满足。
- 怎么判断作者没有为了推销自己的结论而“调整”数据？例子：选择性展示数据。
- ......

上面这些问题可以总结为：**怎么知道分析师没有犯错，无论是有意的错误还是无意的错误**。

按照传统的方法，“找分析报告的 bug”是个成本很高的活儿，我们几乎只能被动接受分析师给的结果。

这意味着，我们没有办法系统性地保证数据分析的质量。一个小公司也许可以容忍这样的错误可能，但对严重依赖数据做出决策、并且需要做出大量决策的公司，这不是最优解。

## 一个可复制的分析报告

用可复制的分析报告，之前的研究问题会用下面的方法做。

### 数据获取

要求：数据的获取必须调用 API 或者写 SQL 语句。

作用：
- 当我拿到你的分析，我能复制这段代码，然后调出完全一样的数据
- 如果我怀疑数据提取过程有问题，我可以检查代码，判断是不是取数据的代码写错了

禁止行为：在后台“下载”数据。没人知道你在下载前做了哪些操作。

### 数据清洗

要求：
- 清洗数据必须使用代码完成。
- 整个数据清洗流程，都用注释清晰的标注。

作用：其他人能检查整个分析流程，判断是否有某些地方出错。

禁止行为：在 excel 或其他工具上用**鼠标点选**完成数据清洗。鼠标点选也是不可复制的，不只别人复制不了，分析师自己都很难复制（也许第一次分析时点错了某个地方却不自知）。

### 数据分析过程

要求：
- 需要给出使用某个分析模型的原因
- 需要说明参数选择的过程
- 需要用代码展示完整的分析流程


作用：
- 数据分析包含一定的理论知识，也包含很多相对主观的判断，记录这些判断过程，有助于其他人理解
- 方便检查代码正确情况

禁止行为：不能把数据分析的过程当成“黑箱子”，过程和结果同样重要。

### 报告形式

要求：
- 核心分析内容和分析结论在报告主干部分
- 从数据获取到分析部分的代码，都在备注部分，或者放在另一个文件里


作用：
- 报告使用者只需要阅读主要部分
- 报告**审阅者**可以在备注部分查找可复制研究的过程

### 工具

目前似乎只有2个选择：
- R：使用 Rmarkdown 
- Python：使用 Notebook

其他工具满足不了“可复制”的需求。


## “可复制研究”的意义

本质上，“可复制研究” 把数据分析从黑箱子变成了可以审阅的文本，发现分析错误变得相对便宜。

它让每个人的工作都变得 accountable。每一行代码、每一个分析决策，都被暴露在光天化日之下，东郭先生就无处藏身了。想象一下，写了你名字的报告上竟然有低级的数据清洗错误，这是件多么丢人的事情。

整体上，“可复制研究”会提高整个数据团队的分析质量。

我还没有机会在这样的团队工作，但过去的经历让我知道“普通”的数据分析团队可以糟糕到什么程度。看到某些分析师的报告，会让你怀疑他们是否上过统计学入门课，我一直为使用这些报告的决策者捏把汗。

可复制研究也对团队成员的能力有巨大的要求：
- 必须会 R 或 Python，会还不够，还必须能写出 readable code
- 表达能力要足够强
- 有一定的统计基础，能够做到引用文献

所谓“可复制研究”，除了是一种理念，还是一套工作流 workflow。就像 readable code 能整体上提高团队的开发质量，reproducible report 能整体上提高团队的数据分析质量。

使用可复制研究还有一个好处，“知识”变得可以沉淀，不再只是某些分析师脑子里的经验。后来者可以通过前人的报告学习分析思路、复用代码。
