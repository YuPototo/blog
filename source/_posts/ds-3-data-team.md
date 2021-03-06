---
title: 数据团队的三个角色：数据科学家、数据分析师、数据工程师
date: 2019-01-07 02:39:41
categories:
  - 数据科学
---

{% asset_img data.png "三者的区别" %}

一般来说，一个完整的数据团队应该由3个角色构成：
- 数据分析师 data analyst
- 数据工程师 data engineer
- 数据科学家 data scientist

这篇文章总结三个角色的区别，并说明何种情况下团队里应该有这些角色。

<!-- more -->

## 三个角色

三个角色的工作有时会有一定程度的交集，这里只讨论三个角色的最重要差异。

- 数据分析师： 需要了解业务，使用**常规的数据分析方法**为日常商业决策提供依据
- 数据工程师：创造和维护数据基础设施
- 数据科学家：掌握机器学习算法，擅长数学、统计学和编程，可以处理大数据分析需求


### 数据分析师

数据分析师是最常见的角色，已经存在了很多年。早期的数据分析师只需要掌握 Excel 的使用，生成日常数据报表，做一定的数据可视化。一般情况下，数据分析师还需要懂业务，并通过数据对商业决策提供建议。

稍微进阶的数据分析师，还会懂一些 inferential statistics，使用线性回归等方法做归因和预测。

随着大家的学习能力越来越强，数据分析已经变成了一个基本技能。在这个环境下，数据分析师的核心价值是什么呢？我认为是“懂业务”。

数据分析师需要非常了解自己业务的情况，拿游戏来举例，数据分析师应该了解自己所分析的游戏、了解游戏行业的数据特点。SLG 游戏和 ARPG 游戏就是两个完全不同的游戏类型，如果某个核心指标相同（比如7日ROI），数据分析师需要针对两种游戏做不同的判断。同时，数据分析师应该深度体验自己的游戏，并能结合游戏体验提出值得研究的问题。

因为“懂业务”同时也“懂数据”，数据分析师应该是最能影响项目决策的人。

数据分析师的常见学科背景：商科、经济学、统计学。

### 数据工程师

数据工程师其实是“需要做数据相关开发工作”的程序员，比如数据存储、数据计算、数据后台的搭建等等。

在小一些的公司，数据工程师一般由后端开发人员兼任。大型的公司，会需要专门的数据工程师。

数据工程师的常见学科背景：计算机。

### 数据科学家

数据科学家是最近几年又硅谷带动起来的新兴岗位。数据科学家最核心的特点是：能用**科学的**方法解决企业的数据分析问题。

为什么这里提到“科学的”方法呢？有两个原因。

第1，很多数据分析师的分析工作其实充满了主观判断，毫无科学性可言。举个例子，我在某上市游戏公司的数据部门上班时，发现他们的数据分析报告**从来不会**使用假设检验，重要决策只通过画图寻找相关性后“主观判断”。数据分析其实是个有着严格科学基础的工作，专业的数据科学家会让团队的数据决策更加准确。

第2，大数据的兴起，让数据分析工作从传统的 excel 就能解决变成必须依赖高强度的编程能力和数学能力。

数据科学家有如下特点：
- 高学历
- 强大的数学和统计学能力
- 了解机器学习、NLP 等新兴数据分析领域的情况
- 强大的（数学）编程水平

相比于数据分析师，数据科学家可能不是那么懂业务。
相比于数据工程师，数据科学家又不是那么懂数据库、API。


## 三个角色的需求

数据分析师：大中小型团队都需要的角色
数据工程师：大型团队需要
数据科学家：大型团队需要

我了解的美国某著名游戏公司的情况：
- 每一个项目里都有数据分析师，专门负责这个项目的分析工作和决策支持
- 数据工程师和数据科学家，不跟随项目走，在项目需要的时候提供工程支持和理论支持

工作场景举例：
- 数据分析师：日常分析需求、数据分析报告
- 数据工程师：分析师想要增加10个埋点，工程师满足这个需求
- 数据科学家：审查数据分析师的分析报告是否符合科学规范

对最顶级的公司，竞争的核心点其实是数据科学家。一个强大的数据科学家团队，可以让数据决策的精准度得到巨大提高。因为，数据科学家可以把结论用概率的形式科学的表示出来。（也许某些数据分析师也可以，但我不敢信他们啊）。

如果没有数据科学家，很有可能出现两种情况：
- 数据分析师给出质量无法保证的数据分析结果
- 团队依靠“数据可视化”的图像做出“猜测”

什么叫“团队依靠图像做出猜测”呢？举个让我印象深刻的例子吧。在游戏公司上班时，项目负责人拉数据之后画两个折线图，发现两者都有向上的趋势，于是推测出两者正相关，进而得出了一个行动依据。在懂数据分析的人看来，这大概跟“夜观天象”的靠谱程度差不多吧。一个数据科学家会在这个问题上给出严谨的数学公式、编程计算和结果报告，这就是数据科学家的价值。

但是，只有在尊重科学的公司，数据科学家的声音才会被重视。这可能也是硅谷才有这么多数据科学家岗位的原因吧啊。
