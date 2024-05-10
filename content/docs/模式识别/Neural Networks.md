---
date: '2024-05-10'
title: 'Neural Networks'
weight: 5
math: true
url: '/docs/pr/5'
---

笔记作者：BrickLoo

## Artificial Neural Network (ANN)

回顾前一章节 Linear Classification 的末尾，我们提到了线性分类器在几何学上的非凹特性。也就是说，对于决策区域的形状较为复杂，例如出现凹陷或者是需要使用异或逻辑的时候，线性分类器就显得比较无能为力了。对于这个问题，改进的思路自然是引入非线性函数。但接下来的两个难点是，如何引入非线性函数，引入什么非线性函数。即使是一些存在异或逻辑的决策区域，或者有一些凹陷的决策区域，只要找到合适的非线性函数其实都是有可能拟合的，但是这并不优雅——因为通常来说并不知道哪种非线性函数拟合得比较好，即使我们找一个完备的基函数集合使其能表示出任何一种非线性函数，也有太多的参数要估计，我们难以训练。

不妨让生物科学的发展为我们打开视野。观察人的神经元，它从其他神经元那里接受输入，并在满足条件的情况下将激活信号传递给下一个神经元。这与我们前面所讨论的线性模型其实是很相似的：接受输入，按权重向加，然后判断类别。但它的不同点在于，它会将自己的类别判断传递给下一个“单元”。那前面所介绍的线性分类模型是否也可以作为一个个“单元”来组成类似的结构，以弥补线性模型主要的局限性？答案是肯定的。仔细思考一下，除了加权求和过程中的线性处理，在判定类别时，我们其实是经过了一个非线性的处理。转换为数学语言来描述，这个非线性的处理可以描述为
$$f(x)=\begin{cases}
   +1 & x \gt 0 \newline
   -1 & x \lt 0
\end{cases}$$
虽然这样的非线性处理看着非常简单，但是实际上只要我们能将非线性的信息传递下去，使得整个分类器不完全是线性的，我们就能使用这些“单元”拼凑出更强大的分类器来解决各种非线性问题。

### XOR Example

以异或问题为例，如果我们希望能够将 $(0,1)$ 和 $(1,0)$ 的情况与 $(0,0)$ 和 $(1,1)$ 分离开，我们不妨：
- 先用一个“单元”将 $(0,0)$ 和其他分离开，如 $(0,0)$ 点输出 $1$，其他输出 $0$；
- 再用一个“单元”将 $(1,1)$ 和其他分离开，如 $(1,1)$ 点输出 $1$，其他输出 $0$。
- 这时，我们将这些非线性的分类信息传递给下一个“单元”，下一个“单元”只需要判断是否有被任意一个分离开即可，如判断两者之和为 $0$ 还是为 $1$。

这样，异或问题就通过线性函数和简单的非线性函数组成的“单元”解决了。

仔细回想这个过程，通过多个“单元”并列，我们实现了多个线性决策边界的联动，这有点化直为曲的意思在；同时，通过将“单元”的非线性信息逐级传递，层层叠加，我们还能实现复杂的逻辑。即便这背后的数学原理和参数形式依旧比较简单，使用这种“单元”组成的模型来解决复杂的问题已经“理论可行”了。线性模型当初的局限早已不在话下，我们只需要训练模型中的参数来使其具有合适的逻辑关系即可。不得不说，这种解决方案是非常优雅的。我们不妨为其起一个漂亮的名字——**神经网络**。每一个“单元”不妨就叫做 **“神经元”**，其中非线性的“分类函数”不妨就类比神经元的激活，称为 **“激活函数”**。

### Activation Function

这个解决方案看似完整，实际上我们还需要详细探讨如何训练的问题。

在前一章节里，我们简单提及了广义线性模型的 LMS 训练方法，虽然看起来也有非线性的成分，但是这里还是略微有些区别——在这里，我们每一个神经元应当作为完整的单元考虑，激活函数也需要考虑上。如果将激活函数用 $f(x)$ 表示，那么，一个神经元所对应的函数应当是
$$g(x)=f(\sum^d_{i=1} w_i\phi_i(x))$$
如果我们希望使用 LMS 方法来训练，则需要求出误差的梯度
$$\begin{align*}
\nabla L(w_i)&=2\big(g(x)-c\big)\cdot\frac{\partial g}{\partial w_i}\newline
&=2\big(g(x)-c\big)\cdot\frac{\partial g}{\partial f}\frac{\partial f}{\partial w_i}
\end{align*}$$
这可不好办，因为前面所举例的激活函数即使能求导也是得到数值为 $0$ 的结果，导致我们无法使用 LMS 方法进行梯度下降。所以，我们不妨换一个激活函数，让它既能通过求导来进行模型参数的训练，形态也类似于原本案例中的激活函数，能够将正值和负值分为两类。

对于满足这两个要求的非线性函数，其中一种比较有名的构造叫做 Sigmoid 函数，它的数学表达是
$$f(x)=\frac{1}{1+e^{-x}}$$
它尝试将正值和负值分别分类为 $1$ 和 $0$，但在临界点 $x=0$ 的附近进行了平滑的过渡，有种“是又不完全是”的感觉，使得模型参数能够通过梯度下降的方法进行训练。

## Perceptron

ANN 的意义在于将模式识别模型视为结合了线性和非线性处理的单元的组合。在这个基础上，模式识别模型蓬勃发展，诞生出前馈神经网络、反馈神经网络和图网络等网络结构。同时前馈神经网络中又有感知机和卷积神经网络。这里，我们进一步地详细探讨一种最基础的模型——感知机。

正如前文描述激活函数时提及的那样，感知机算法可以用以下数学公式表示：
$$g(x)=f(w^\intercal\phi(x))$$
其中 $f$ 为非线性的激活函数，$\phi(x)$ 则是来自其他神经元的输入，这个输入相对于 $x$ 是非线性的。

### Criterion

在感知机中，我们可以延续线性分类模型中用符号来进行分类的方法。用数学语言描述，我们希望的训练目标是下式对所有样本成立：
$$g(x)y>0$$
回顾前一篇中提到的 LMS 方法，当我们进行参数更新时，我们尝试将分类器的输出值接近人为指定的数值 $c$，因此我们作差并求平方。受到训练目标的数学语言描述的启发，我们也可以尝试一下将准则函数中的作差改为相乘，即
$$L(w)=-\sum_{x\in M}{g(x)y}$$
其中，$M$ 表示训练集中被错误分类的样本子集，这是因为相比起 LMS 方法，这种相乘的准则函数不具有结果恒为正的特性，因此可能会由于正负抵消而起不到训练效果。当然，我们也可以每传入一次样本就判断一次正误然后进行一次更新，这时的准则函数就是
$$L(w)=-g(x)y$$
但是我们依旧要进行分类正误的判断。这种判断实际上可以被理解为一种正则化的手段，因为如果我们继续使用已经分类正确的样本进行训练和参数更新，模型就会进一步放大参数的数值，使得模型更加容易放大一些非重点信息的影响，因此可能对于噪声更加敏感，影响模型的泛化能力。

> 注：
> 要最小化或最大化的函数被称为目标函数（objective function）或准则（criterion）。当对其进行最小化时，我们也可以称其为代价函数（cost function）、损失函数（loss function）或误差函数（error function）。

明确了准则函数，剩下的就是根据准则函数进行梯度下降了。使用相乘的准则函数的算法伪代码如下：

1. Choose $w_0$ randomly
2. Choose $\eta$
3. $t = 0$
4. Repeat
5. &emsp;$M=\emptyset$
6. &emsp;For $i = 1$ to $N$
7. &emsp;&emsp;If $g(x_i) y_i \leq 0$, then $M = M \cup \{x_i\}$
8. &emsp;End
9. &emsp;$w_{t + 1} = w_t + \eta \sum_{x\in M}{g(x)y}$
10. &emsp;$t=t+1$
11. Until $M=\emptyset$

相乘的优势在于梯度计算公式看起来更加简洁，因为差的平方在求导的时候需要进行复合函数求导，而相乘则是简单函数的求导。然而，这种方法需要对模型的分类正误情况进行判断，逻辑处理上不如作差的方法简单直接，因此神经网络中通常还是采用作差的方法，即采用均方误差作为准则函数。

### Back-Propagation (BP)

神经网络模型通过信息在神经元中的传递实现强大的功能，而传递经过的层数越多，数据受到的处理也越复杂，因此也能实现相对更难的任务。对于多层神经元的神经网络模型，我们可以通过反向传播算法实现整个模型的参数更新。反向传播的伪代码如下：

1. Initialize network topology, $w$, criterion $\theta$, $\eta$, $epoch \leftarrow 0$
2. Repeat
3. &emsp;$epoch \leftarrow epoch + 1$; $m \leftarrow 0$; $\Delta w_{kj} \leftarrow 0$; $\Delta w_{kj} \leftarrow 0$
4. &emsp;Repeat
5. &emsp;&emsp;$m \leftarrow m+1$
6. &emsp;&emsp;$x \leftarrow select\ pattern$
7. &emsp;&emsp;$\Delta w_{kj} \leftarrow \Delta w_{kj} - \eta\frac{\partial L}{\partial w_{kj}}$; $\Delta w_{ji} \leftarrow \Delta w_{ji} - \eta\frac{\partial L}{\partial w_{ji}}$; ...
8. &emsp;until $m = n$
9. &emsp;$w_{kj} \leftarrow w_{kj}+\Delta w_{kj}$; $w_{ji} \leftarrow w_{ji}+\Delta w_{ji}$; ...
10. until $\nabla J(w) < \theta$

其中，我们用 $w_{kj}$ 表示第 $j$ 层与第 $k$ 层神经元之间的线性组合权重，$w_{ji}$ 同理表示第 $i$ 层与第 $j$ 层神经元之间的线性组合权重；省略号表示在第 $i$ 层前或许还有若干层神经元，它们都可以参考 $wji$ 的更新进行相同思路的处理。也就是说，在这里，第 $k$ 层是模型的最后一层。这里我们将层号倒过来写，是为了强调这个算法需要从后往前计算——因为在计算更前的参数时，我们可以用到后面的参数更新时的部分计算结果，节省计算量。这也是为什么算法名叫“反向传播”的原因。

先看 $\frac{\partial L}{\partial w_{kj}}$ 的计算方法。对于最后一层，当我们以均方误差 $L=(target-output)^2/2$ 为准则函数时
$$\frac{\partial L}{\partial w_{kj}}=\frac{\partial L}{\partial output_k}\frac{\partial output_k}{\partial net_k}\frac{\partial net_k}{\partial w_{kj}}$$
其中，$net_k = \sum^n_{t=1}{w_{kt}y_t}$，$output_k=f(net_k)$，分别表示神经网络模型单元中的线性组合和非线性处理部分，因此，
$$\begin{align*}
\frac{\partial L}{\partial output_k}&=-(target_k-output_k) \newline
\frac{\partial output_k}{\partial net_k}&=f\prime(net_k) \newline
\frac{\partial net_k}{\partial w_{kj}}&=output_j
\end{align*}$$

继续看倒数第二层用到的 $\frac{\partial L}{\partial w_{ji}}$，它的公式同理为
$$\frac{\partial L}{\partial w_{kj}}=\frac{\partial L}{\partial output_k}\frac{\partial output_k}{\partial net_k}\frac{\partial net_k}{\partial w_{kj}}$$
每一项的取值分别是
$$\begin{align*}
\frac{\partial L}{\partial output_j}&=\sum\frac{\partial L}{\partial net_k}\frac{\partial net_k}{\partial output_j}=\sum\frac{\partial L}{\partial net_k}w_{kj} \newline
\frac{\partial output_j}{\partial net_j}&=f\prime(net_j) \newline
\frac{\partial net_j}{\partial w_{ji}}&=output_i
\end{align*}$$
值得注意的是，在求解 $\frac{\partial L}{\partial output_j}$ 这一项时，我们应该记得进行求和操作，因为第 $j$ 层神经元输出的值会送到第 $k$ 层中多个不同的神经元进行计算，而对于第 $k$ 层中不同神经元的输出 $output_k$，我们也有不同的期望值 $target_k$。在衡量模型整体的误差时，我们通常需要叠加地考虑同一层中不同神经元的误差，因此我们在更新参数时也需要充分地考虑对于所有路径的影响。

同时我们不难发现，求解这一项时，大部分要计算的东西已经在更新后一层参数的计算中求过了，可以直接拿过来用。这也就是为什么我们从后往前更新模型参数的原因。