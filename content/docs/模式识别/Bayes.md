---
date: '2024-03-01'
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
  - $p \left( \omega | x \right)$ 被称为 posterior probability
- 等式右边的
  - $p\left(x|\omega\right)$ 被称为 conditional probability 或 likelihood
  - $p\left(\omega\right)$ 被称为 prior probability
  - $p\left(x\right)$ 被称为 evidence

这个公式的意义在于，
- 不同的变量，“观测”其取值的难度不同
  - 例如一个人要“观测”他有没有生病（生病状态的取值），不准，只能用 $p\left(\omega\right)$ 来估计（但是往往大多数是健康的）
  - 但是如果要“观测”他的某项体检指标（体检指标的取值），简单且准
- 但是我们可以简单地通过统计的方式得到变量各自的取值比例 $p\left(\omega\right)$ 和 $p\left(x\right)$
  - 例如可以统计生病和健康的人在人群中的比例
  - 还可以统计体检指标在人群中的取值分布
- 而且对于难以“观测”的变量 $\omega$，我们在优先确定其取值的情况下，可以统计其它方便“观测”的变量 $x$ 取值的分布情况 $p\left(x|\omega\right)$ 
  - 例如在确定了生病人群和健康人群后，我们可以分别统计他们体检指标取值的分布
- 在有了先前统计信息的基础后，我们可以把原本的难以“观测”的变量用更容易“观测”的变量来表达，用统计的比例来估算概率
  - 从“只能用 $p\left(\omega\right)$ 来估计”，到“用额外引入的指标 $x$ 计算 $p \left( \omega | x \right)$ 来估计”，既降低了观测难度，也提高了观测的准确度

简单来说，这是一种通过“转移问题到引入的新指标上”来“降低难度并提高准确度”的策略。

## Rules

### Bayes Decision Rule

根据前面的 Bayes 公式，在估计难以观测的变量 $\omega$ 时，我们可以引入其它指标 $x$，根据 $p \left( \omega | x \right)$ 来估计其取值概率，以降低难度提高准确度。

概率越大顾名思义就是更应该作为估计结果的那个，因此 $\omega$ 的取值问题就变成了等式右边分子 $p\left(x|\omega\right)p\left(\omega\right)$ 每种取值情况下 $p\left(x|\omega_i\right)p\left(\omega_i\right)$ 的大小比较问题。**根据 $p\left(x|\omega_i\right)p\left(\omega_i\right)$ 的大小做出决策的这种规则就叫 Bayes Decision Rule。**

这里可以忽略分母的原因是它并不影响概率大小的比较，因为我们是先观测确定 $x$ 的取值，再判断 $\omega$ 的取值，无论最后 $\omega$ 选择了哪个值，$x$ 的取值都是相对固定的，对应的 $p\left(x\right)$ 也是相对固定的，所以在不同取值的比较中数值大小是相等的。

### Maximum Likelihood (ML) Rule

ML Rule 是 Bayes Decision Rule 的一种特殊情况，在 $\omega$ 所有取值的概率都相等的情况下，我们比较时也不用考虑 $p\left(\omega_i\right)$，于是最后就只是 $p\left(x|\omega_i\right)$ 之间的大小比较。$p\left(x|\omega\right)$ 在公式中被称为 likelihood，而我们的决策规则仅参考它的取值，所以这个决策规则被命名为 ML Rule 的原因也显而易见了。

### Minimum Risk Decision Rule

风险是在做出了决策采取了行动的基础上考虑的。我们估计一个难以“观测”的变量变量值，最终的目的是辅助我们做出下一步行动，而不同的行动也存在不同程度的后果。（对于更加严峻的后果，我们往往需要做出更保守的决策。）

将行动记为 $\alpha$，对于我们的估计的变量 $\omega$，不同的实际值 $\omega_j$ 下采取不同的行动 $\alpha_i$ 所产生的**损失**我们可以记为 $\lambda_{ij} = \lambda\left(\alpha_i|\omega_j\right)$，因此我们可以定义 conditional risk 来评估我们行动的合理程度：
$$R\left(\alpha_i|x\right)=\sum^c_{j=1}\lambda_{ij}p\left(\omega_j|x\right)$$

最合理的行动，就是使得 conditional risk 最小的 $\alpha_i$，这种决策规则就称为 Minimum Risk Decision Rule。

## Discrimination

### discriminant function

前面的几个 rules 中，我们都是通过数值大小比较来进行决策的，而它们的共性是输入已知的观测值 $x$，得到选择 $i$ 下的评估数值（例如概率值）。因此我们可以将他们统称为判别函数 $g_i\left(x\right)$。

判别函数的概念比较泛。通常我们可以将它们之间进行作差或相除处理，通过观察它们与 $0$ 或 $1$ 的关系实现相互之间的比较，这种调整后的函数也可以叫做判别函数；判别函数还能通过指数对数等数学处理方法进行处理让它长得更加“优雅”，只要在仍然保持判别功能的情况下（因为这种处理具有单调性），都算是判别函数。

### decision region & decision boundary

对于决策函数，不同的输入（或者说“特征空间”中的不同位置）会有不同的最优取值，因此他们根据取值被划分成不同的决策区域；决策区域的边界就是决策边界。

## Error

### for a single decision

性能评估是性能改进的关键步骤，因此我们需要分析这种决策方式的错误率以评估性能。对于 Bayes Rule，决策的错误率为

$$p(error|x)=1-\mathop{max}\big[p(\omega_1|x),p(\omega_2|x),\dots,p(\omega_n|x)\big]$$

因为我们会选使得 $p\left(\omega_i|x\right)$ 最大的那个 $\omega_i$，且这样选择的正确率就是 $p\left(\omega_i|x\right)$，所以错误率就是用 $1$ 减去它。

### for the whole problem

**Bayes Error 考虑的是整个问题在选定指标下的错误率。** 它的计算方法是将指标 $x$ 的所有可能取值的错误率进行累加，求它能取到的最小值。对于不同的取值 $x$，
- 如果不同类别的概率 $p$ 差别很大，根据公式减去的 $p\left(\omega_i|x\right)$ 相对更大，累加的错误率便相对更小；
- 如果不同类别的概率 $p$ 差别很小，根据公式减去的 $p\left(\omega_i|x\right)$ 相对更小，累加的错误率便相对更大。

可见 Bayes Error 可以用于评估问题下 $x$ 的选取合理性。

对于实际问题，在靠近决策边界的区域，我们可能比较难做出最优决策；因此对于实际的 Error，如果做出的决策不是理论最优决策，那么根据公式减去的 $p\left(\omega_i|x\right)$ 就是相对更小的值，因此实际计算出的 Error 会相对更大。数值上多出来的这部分被称为 Added Error。即
$$Error = Bayes\ Error + Added\ Error$$