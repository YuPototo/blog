---
title: Association Rules：定义和指标
date: 2019-08-31 13:34:37
categories:
  - 数据科学
tags:
  - 机器学习
---

Association Rules 解决的问题是：哪些商品经常被一起购买。

典型例子如下图，这是 Udemy 某课程页面里的搭售信息：

{% asset_img udemy.png "udemy" %}

Udemy 的推荐系统基于 association rule 判断出这两个商品经常被一起购买，所以打包推荐了这两个商品。

这篇文章主要说明 association rule 里的几个定义和指标

<!-- more -->

## Itemset

### Antecedent 和 Consequent

关联规则的表达形式是：*如果 A，那么 B*。表示 A 和 B 两组商品会大概率一起出现。

在上面的规则里，A 被称为 antecedent，是一组商品；B 被称为 consequent，是另一组商品。

每一个 antecedent 和 consequent 的组合，被称为一个 **itemset**。

例子：购买了*面包*和*鸡蛋*的用户，也会购买*牛奶*。用 antecedent、consequent 和 itemset 表示规则如下：

- antecedent：{面包, 鸡蛋}
- consequent: {牛奶}
- itemset: {面包, 鸡蛋, 牛奶}

在文献里用 {} 表示 itemset，在 python 里，{} 刚好也是 set。

### 根据订单计算 itemsets

一个包含“面包，鸡蛋，牛奶”的订单，有几个 itemset 呢？

所有 itemset 如下：

- 面包
- 鸡蛋
- 牛奶
- 面包，鸡蛋
- 面包，牛奶
- 鸡蛋，牛奶
- 面包，鸡蛋，牛奶

共7个itemset。

1个商品，也算一个 itemset。

根据交易历史，可以得到一个商店的 itemsets。以下面的交易记录为例（1表示购买，0表示未购买）：

| 订单编号 | 鸡蛋 | 牛奶 | 咖啡 |
|:--------:|:----:|:----:|:----:|
|     1    |   1  |   1  |   0  |
|     2    |   1  |   1  |   1  |
|     3    |   0  |   1  |   1  |

- {鸡蛋} x 2
- {牛奶} x 3
- {咖啡} x 2
- {鸡蛋，牛奶} x 2
- {鸡蛋，咖啡} x 1
- {牛奶，咖啡} x 2
- {鸡蛋，咖啡，牛奶} x 1

上面的交易记录里医用有 13 个itemsets。

## 指标

Associaition rule 的核心问题是：如何判断哪些 rule 是有价值的。

几个比较重要的指标是：

- support
- confidence
- lift

### Support

support 的定义是：itemset 的占比。

以上面的3个订单为例子，各个 itemset 的 suport 依次是：

- {鸡蛋} = 2/13 = 15.4%
- {牛奶} = 3/13 = 23%
- {咖啡} = 2/13 = 15.4%
- {鸡蛋，牛奶} = 2/13 = 15.4%
- {鸡蛋，咖啡} = 1/13 = 7.7%
- {牛奶，咖啡} = 2/13 = 15.4%
- {鸡蛋，咖啡，牛奶}  = 1/13 = 7.7%

Support 可以用数学公式描述为：Support = P(antecedent AND consequent)

### Confidence

confidence 的定义是：给定 antecedent，consequent 发生的概率有多大？

confidence 的数学公式是：Confidence = P(antecedent AND consequent) / P(antecedent) = P(consequent | antecedent)

还是用刚才的例子：

“if 牛奶，then 鸡蛋”的 confidence 是：P(牛奶 and 鸡蛋) / P(牛奶) = 15.4% / 23% = 67%

“if 咖啡，then 牛奶+鸡蛋” 的 confidence 是：P(咖啡 and 牛奶 and 鸡蛋) / P(咖啡) = 7.7% / 15.4% = 50%

这个指标有一定的欺骗性。如果 antecedent 和 consequent 都有很高的 support，那么相应的 confidence 必然很高，即使两者相互独立。

所以我们需要第三个指标：Lift Ratio。

### Lift Ratio

假定 antecedent 和 consequent 相互独立，我们可以计算出一个理论的 confidence 值。这个 confidence 被命名为 benchmark confidence 值。

假定两者相互独立，可以有：

P(antecedent AND consequent) = P(antecedent) * P(consequent)

然后可以计算 benchmark confidence：

Benchmark Confidence = P(antecedent) * P(consequent) / P(antecedent) = P(consequent)

然后可以计算 **lift ratio**：

lift ratio = confidence / benchmark confidence

以下面几个规则为例

“if 牛奶，then 鸡蛋”：

- benchmark confidence = P(consequent) = P(鸡蛋) = 23%
- confidence = 67%
- lift = 67% / 23% = 2.7

if 咖啡，then 牛奶+鸡蛋”:

- benchmark confidence = P(牛奶 AND 鸡蛋) = 15.4%
- confidence = 50%
- lift = 50% / 15.4% = 3.25

## 小结

Association rules 的算法都基于上面几个概念和指标。理解了这些指标后，如果有必要，可以去看算法，如果对算法细节不在意，基本就可以直接去跑数据了。
