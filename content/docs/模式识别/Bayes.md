---
date: '2024-03-01'
lastmod: '2024-04-22'
title: 'Bayes'
weight: 2
math: true
url: '/docs/pr/2'
---

笔记作者：BrickLoo

## Bayes Formula

本质上是描述“考虑多种变量/条件叠加的概率时，考虑条件先后顺序不影响结果”的等式。(只需要移下项)

$$p \left( \omega | x \right) = \frac{p\left(x|\omega\right)p\left(\omega\right)}{p\left(x\right)}$$

其中，
- 等式左边的
  - $p \left( \omega | x \right)$ 被称为**后验概率 posterior probability**，可以理解为事情发生“后”，从结果反推得到的成因条件情况的可能性。
- 等式右边的
  - $p\left(x|\omega\right)$ 被称为**条件概率 conditional probability** 或**似然 likelihood**，可以理解为在一定“条件”的成因下，产生这种结果的“可能性”；
  - $p\left(\omega\right)$ 被称为**先验概率 prior probability**，可以理解为成因条件出现的可能性，但是这通常需要我们从客观世界中通过实践发现或者做出近似假设，来作为使用这个公式的“前提”知识和“先决”条件；
  - $p\left(x\right)$ 被称为**证据 evidence**，因为这些样本是实打实地从整体中得到的样本，为我们推测整体情况提供了“证据”。

这个公式的意义在于，
- 不同的变量，“观测”其取值的准确度不同
  - 例如要直接判断一个人有没有生病（生病状态的取值）而不借助其他特征，即只用 $p\left(\omega\right)$ 来估计（但是往往大多数是健康的），不准；
  - 但是如果要测量对比他的某项体检指标（体检指标的取值），通常简单且准。
- 然而，我们可以简单地通过统计的方式得到变量各自的数值分布 $p\left(\omega\right)$ 和 $p\left(x\right)$
  - 例如可以统计生病和健康的人在人群中的比例，
  - 还可以统计体检指标在人群中的数值分布。
- 而且，对于难以“观测”的变量 $\omega$，如果已经确定了其取值，再进一步统计其它“观测”的变量 $x$ 取值的分布情况 $p\left(x|\omega\right)$ 是相对简单的
  - 例如在确定了生病人群和健康人群后，我们可以分别统计他们体检指标取值的分布
- 那么，在有了先前统计信息的基础后，我们可以把原本的难以“观测”的变量用更容易“观测”的变量来表达，用统计得到的概率分布来估计观测目标的取值概率
  - 从“只能用 $p\left(\omega\right)$ 来估计”，到“用额外引入的指标 $x$ 计算 $p \left( \omega | x \right)$ 来估计”，提高了观测的准确度

简单来说，这是一种通过“转移问题到新的观测指标”来“提高观测准确度”的策略。同时，通过简单的方法来提高准确度也算是“降低了观测难度”。

## Rules

### Bayes Decision Rule

根据前面的 Bayes 公式，在估计难以观测的变量 $\omega$ 时，我们可以引入其它指标 $x$，根据 $p \left( \omega | x \right)$ 来估计其取值概率，以降低难度并提高准确度。

概率越大顾名思义就是更应该作为估计结果的那个，因此 $\omega$ 的取值问题就变成了等式右边分子 $p\left(x|\omega\right)p\left(\omega\right)$ 每种取值情况下 $p\left(x|\omega_i\right)p\left(\omega_i\right)$ 的大小比较问题。**根据 $p\left(x|\omega_i\right)p\left(\omega_i\right)$ 的大小做出决策的这种规则就叫 Bayes Decision Rule。**

这里可以忽略分母的原因是它并不影响概率大小的比较，因为我们是先观测确定 $x$ 的取值，再判断 $\omega$ 的取值，无论最后 $\omega$ 选择了哪个值，$x$ 的取值都是相对固定的，对应的 $p\left(x\right)$ 也是相对固定的，所以在不同取值的比较中数值大小是相等的。

### Maximum Likelihood (ML) Rule

ML Rule 是 Bayes Decision Rule 的一种特殊情况，在 $\omega$ 所有取值的概率都相等的情况下，我们比较时也不用考虑 $p\left(\omega_i\right)$，于是最后就只是 $p\left(x|\omega_i\right)$ 之间的大小比较。$p\left(x|\omega\right)$ 在公式中被称为 likelihood，而我们的决策规则仅参考它的取值，所以这个决策规则被命名为 ML Rule 的原因也显而易见了。

### Minimum Risk Decision Rule

我们尝试“观测”一个变量值，通常是因为它的取值会影响我们的下一步行动。前面两种规则是根据概率来评估取值的可能性，而这个规则则是进一步通过变量取值的可能性来评估我们采取行动的决策合理性。对基于不确定的变量采取的行动，我们通常使用“风险”这一概念来评估其合理性。

风险，指意外事件（如不符合实际取值的行动）发生的可能性与其产生后果的组合，如果意外发生的可能性越大，后果越严重，行动的风险就越大。将采取的行动记为 $\alpha$，对于我们的估计的变量 $\omega$，不同的行动 $\alpha_i$ 在不同的实际值 $\omega_j$ 下所产生的后果的严重程度我们记为 $\lambda_{ij} = \lambda\left(\alpha_i|\omega_j\right)$，那么我们可以这样定义 conditional risk：
$$R\left(\alpha_i|x\right)=\sum^c_{j=1}\lambda_{ij}p\left(\omega_j|x\right)$$

最合理的行动，就是使得 conditional risk 最小的 $\alpha_i$，这种决策规则就称为 Minimum Risk Decision Rule。

## Discrimination

### discriminant function

前面的几个 rules 中，我们都是通过数值大小比较来进行决策的，而它们的共性是输入已知的观测值 $x$，得到选择 $i$ 下的评估数值（例如概率值）。因此我们可以将他们统称为判别函数 $g_i\left(x\right)$。

判别函数的概念比较泛。通常我们可以将两个判别函数通过作差或相除来进行合并，并通过观察它们与 $0$ 或 $1$ 的关系实现相互之间的比较，这种合并后的函数也可以叫做判别函数；判别函数还能通过指数对数等数学处理方法进行处理让它长得更加“优雅”（因为这种处理保留单调性），只要仍然保持判别功能，它们都算是判别函数。

### decision region & decision boundary

对于判别函数，不同的输入（或者说“特征空间”中的不同位置）会有不同的最优选择，因此他们根据不同的选择被划分成不同的决策区域；决策区域的边界就是决策边界。

## Error

### for a single decision

性能评估是性能改进的关键步骤，因此我们需要分析这种决策方式的错误率以评估性能。对于 Bayes Rule，决策的错误率为

$$p(error|x)=1-\mathop{max}\big[p(\omega_1|x),p(\omega_2|x),\dots,p(\omega_n|x)\big]$$

因为我们会选使得 $p\left(\omega_i|x\right)$ 最大的那个 $\omega_i$，且这样选择的正确率就是 $p\left(\omega_i|x\right)$，所以错误率就是用 $1$ 减去它。

### for the whole problem

**Bayes Error 考虑的是整个问题在选定指标下的错误率**，它的计算方法是将指标 $x$ 的所有可能取值的决策错误率 $p(error|x)$ 进行累加。对于一个固定的 $x$，
- 如果不同类别 $\omega_i$ 中，最大的概率值 $p\left(\omega_i|x\right)$ 与其他差别较大，根据公式减去的 $\mathop{max}\big[p(\omega_1|x),p(\omega_2|x),\dots,p(\omega_n|x)\big]$ 相对更大，累加的错误率便相对更小；
- 如果不同类别 $\omega_i$ 中，最大的概率值 $p\left(\omega_i|x\right)$ 与其他差别较小，根据公式减去的 $\mathop{max}\big[p(\omega_1|x),p(\omega_2|x),\dots,p(\omega_n|x)\big]$ 相对更小，累加的错误率便相对更大；

可见 Bayes Error 可以反映指标 $x$ 的选取合理性。好的指标更能够区分开不同类别的概率，因此有更小的 Bayes Error。

对于实际问题，在靠近决策边界的区域，我们可能比较难做出最优决策；因此对于实际的 Error，如果做出的决策不是理论最优决策，那么根据公式减去的 $p\left(\omega_i|x\right)$ 就不是最大值 $\mathop{max}\big[p(\omega_1|x),p(\omega_2|x),\dots,p(\omega_n|x)\big]$，而是相对更小的值，因此实际计算出的 Error 会相对更大。数值上多出来的这部分被称为 **Added Error**。即
$$Error = Bayes\ Error + Added\ Error$$