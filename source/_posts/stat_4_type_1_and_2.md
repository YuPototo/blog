---
title: 1类错误和2类错误
date: 2018-07-15 19:45:10
categories:
  - 统计学
tags:
  - Statistics
---

{% asset_img plot_0.jpg "题图" %}

统计学里有两个基石一样的概念：一类错误和二类错误。这两个概念是统计检验的基础。

<!-- more -->

## 原假设

在定义这两类错误前，我们需要先复习假设检验。

在检验检验里，我们定义了**原假设** null hypothesis。原假设是我们希望通过数据去“证伪”的假设，习惯被表述为 H0。

一个典型的 H0 是：本市的人均收入大于或等于8000元。

原假设的对立面，是备择假设 alternative hypothesis，习惯被表述为 Ha。

上面的 H0 对应的 Ha 是：本市的人均收入小于8000元。

我们对本市市民做了一次问卷调查，搜集了10000份问卷，根据数据调查结果，我们会有两种结论：
- 拒绝 H0，即接受 Ha
- 无法拒绝 H0

在上面的表述里，我们没有说“接受 H0”。这是外行最常用错的表达。

## 为啥不能说“接受”原假设

在解答这个问题之前，我们需要先了解假设检验的方法论。

对每一个研究课题，我们的工作流程是：
1. 确定 H0 和 Ha
1. 根据过往的研究和理论，确定要使用的假设检验方法
1. 选好一个**显著性水平** significance level，比如 alhpa = 0.05
1. 搜集数据，并计算 p 值
1. 如果 p 值 < alpha，我们说**拒绝原假设**，否则，我们说**无法拒绝原假设**

是否拒绝原假设，受到我们选择的 significance level 影响。如果 p 值等于 0.06，而我们的 significance level 选了0.05，因为`0.06 > 0.1`，这个时候我们会“无法拒绝原假设”。而如果 significance level 选了0.1，因为`0.06 < 0.1`，我们就能够拒绝原假设。

当我们做假设检验时，我们其实是在研究这个问题：**给定原假设为真，我们有多大的可能观察到自己所搜集的数据，如果这个可能性很小，我们就拒绝原假设**。当我们 p 值 > alpha 时，本质上等价于：**我们所搜集到的数据并没有极端到能说明原假设是错的**。这里的极端程度标准，就是我们选好的 significance level。

以这个 H0 为例：本市的人均收入大于或等于8000元。 

我们发现调查1万人的平均收入是7990元，这个数字比8000小，看上去人均收入似乎低于8000了，但要注意，抽样调查存在随机性，这10元的差距很有可能是随机现象导致。换句话说，我们观察到的数据不足以证明原假设是错的。

但我们是否能接受“人均收入大于或等于8000”的说法呢？显然不能，7990 的调查结果当然支撑不了这个说法。

如果我们想要证明人均收入大于8000，我们的原假设就应该是：人均收入小于或等于8000（原假设里一定要有等号）。

## 第1类错误和第2类错误

理论部分搞清楚后，我们开始介绍第1类错误和第2类错误。

第1类错误：原假设为真，但我们拒绝了原假设。这类错误叫**去真**，英文文献里说 false positive。
第2类错误：原假设为假，但我们没有拒绝原假设。这类错误叫**存伪**，英文文献里叫 false negative。

如下图：
{% asset_img error.png "假设检验的错误" %}

犯第1类错误的概率，就是之前出现的 significance level。如果我们确定了 alpha = 0.05，那么在100次假设检验中，就会有5次出现第1类错误。

犯第2类错误的概率，被称为 beta，另一个常用统计学概念 power（统计功效）就等于 1 - beta。犯第2类错误的概率越低，建设检验的 power 就越高。

在这两类错误中，我们更关心哪一类呢？有的书籍会告诉我们第1类错误更重要，它们会用法庭审判的例子，说审判一个无罪的人有罪（第1类错误）是更大的风险，这其实只是个比喻，不是个好例子。下面我们看两个例子。

### 例子1

我们用统计学使用的最严格的场景做例子：药物效果的判断。新药上市前，要经过一系列严格的医学试验。

H0：药物 A 对治疗某疾病没有帮助
Ha：药物 A 对治疗某疾病有帮助

相关错误和结果如下：

第1类错误：药物 A 是没用的，但我们拒绝了原假设，给出了药物 A 是有用的的判断。药物上市，患者购买后没有帮助，患者因为使用 A 药物可能没有使用其他有效的药物，最后可能有负面结果（甚至死亡），进一步研究发现药物 A 是无效的，公司面料患者诉讼，甚至可能破产。

第2类错误：药物 A 是有用的，但我们没有拒绝原假设。结果：药物无法上市，公司损失了潜在的收入，病人失去了一种潜在的治疗方案。

两种结果，作为公司管理者的你，你会更希望减少哪一种错误的概率呢？显然是第1种。

### 例子2

我们做疾病筛查时，H0 和 Ha 分别是：

H0：病人没有疾病
Ha：病人有疾病

相关错误如下：

第1类错误：病人是没病的，被诊断为有病。病人痛哭流涕，但在之后的进一步检查中，病人发现自己并没有生病，虚惊一场，进而联想到生命的可贵，于是变成一个好父亲好丈夫。

第2类错误：病人是有病的，但原假设没有被拒绝，病人不知道自己生病了，于是逍遥快活，错过了治疗时机，然后症状变严重时，才进一步检查，发现得了病。

在这个例子里，你会更希望减少哪一类错误的概率呢？显然是第2种。在 HIV 检测中，false negative 确实比 false positve 更少见。

## 实践

在实际研究中，我们可以降低两类错误的概率：
1. 第1类错误：通过选择更低的 significant level
2. 第2类错误：通过扩大样本量或选择单侧检验

样本量足够大的时候，我们总是能把两类错误的概率降到足够低。但在实际应用中，更大的样本意味着成本：时间成本和金钱成本。

以 A/B 测试为例，只要样本量足够大，一定发现更好的方案。但产品有自己的迭代计划，每个产品有一系列的 A/B 测试需要做，不可能等每一个测试出现结果。这个时候就必须为了效率牺牲准确性。

有的领域，准确性非常非常重要，比如治疗癌症的药物研究，那是人命关天的事情，而且每天都在烧钱，真的是字面意义上的烧钱。

相比之下，互联网产品的容错率还是很高的。业界能够接受的标准一般是：
- 5%的犯一类错误的概率
- 20%的犯二类错误的概率

一般的产品按照这个标准去做研究，不会有大问题。