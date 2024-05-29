---
date: '2024-05-16'
lastmod: '2024-05-29'
title: 'Support Vector Machines'
weight: 7
math: true
url: '/docs/pr/6'
---

笔记作者：BrickLoo  
笔者注：康奈尔大学提供了比较优质的[机器学习课程资源](https://kuleshov-group.github.io/aml-book/intro.html)，其中涉及了[支持向量机部分](https://kuleshov-group.github.io/aml-book/contents/lecture13-svm-dual.html)，适合作为本章内容的补充。

## Concepts

> Support Vector Machine is a system for efficiently training **linear learning machines** in **kernel-induced feature spaces**, while respecting the insights of generalization theory and exploiting optimization
theory.

还记得我们在前面的 [Linear Classification](https://brickloo.github.io/docs/pr/3/) 中讨论过线性模型中比较简单的[参数选取方法](https://brickloo.github.io/docs/pr/3/#least-squares-classification)，也讨论了线性模型中的[几何特性](https://brickloo.github.io/docs/pr/3/#geometry-of-linear-discriminant-functions)。在 [Practical Techniques](https://brickloo.github.io/docs/pr/5/) 中，我们也谈到模型除了要对训练数据进行拟合，同时也应该拥有尽可能强大的泛化能力。对于此前的线性模型参数求解方法，我们只是找到了一组合适的参数，使得不同类别的样本输出值具有不同的正负性；但合适的参数是多种多样的，哪一种更好，哪一种具有更强的泛化能力？支持向量机 SVM 旨在解决这一问题。

从数形结合的角度考虑问题，线性二分类任务可以视为使用一个线性的决策边界划分空间中散落的样本点，而各种合适的参数则代表了这个决策边界的绘制方式。直观上来说，我们希望两个类别的样本被尽可能地分得更开，因此，两个类别到决策边界的最短距离需要尽可能大。此时，两个类别中间空出来不确定类别的地带，即两个类别到决策边界的距离之和，被称为 **margin**。也就是说，挑选最优的决策边界问题可以被转化成 margin 的最大化问题。

根据前面前面 [Linear Classification](https://brickloo.github.io/docs/pr/3/) 中讨论过的线性模型 [几何特性](https://brickloo.github.io/docs/pr/3/#geometry-of-linear-discriminant-functions)，我们可以将样本点到决策边界的距离表示为
$$d_x=\frac{g(x)}{\lVert\vec{w}\rVert}$$
此时式子的正负号表示样本点相对于决策边界的方向与决策边界法向量方向的异同关系。不妨将同向的样本点赋予类别标签值 $+1$，异向的样本点赋予类别标签值 $-1$，那么此时距离的数值大小就可以表示为
$$r=y\frac{g(x)}{\lVert\vec{w}\rVert}$$

对于最大化 margin 问题，我们只需考虑距离决策边界最近的样本点即可，这些样本点被称为**支持向量 support vectors**。虽然我们不知道它们是哪些样本，但是我们可以确定的是，假设我们已经求出了决策边界 $g(x)$ 的表达式，那么由于它们到达决策边界距离相等，一定处于 $g(x)=\pm c$ 两条直线上，那么，此时 margin 的表达式就可以写成
$$2y\frac{g(x)}{\lVert\vec{w}\rVert}=\frac{2c}{\lVert\vec{w}\rVert}$$

所以，margin 的最大化问题，实际上是分母 $\lVert\vec{w}\rVert$ 的最小化问题。考虑到绝对值的计算和求导等方面问题，我们可以略作调整，把优化目标最终描述为：
$$\text{minimize } \frac{1}{2}\lVert w \rVert^2, \newline \text{such that } y_i(w^\intercal x_i+w_0) \ge c$$

要注意别忘记这里的条件，因为对于线性可分问题的边界优化首先需要保证分类正确。

这里保留数值大小不确定的常量 $c$ 并不是这么好，因为我们可以先将式子两边同时除以这个正值 $c$，变为 $y_i(w^\intercal x_i/c+w_0/c) \ge 1$，然后再忽略掉这个常数 $c$，即
$$\text{minimize } \frac{1}{2}\lVert w \rVert^2, \newline \text{such that } y_i(w^\intercal x_i+w_0) \ge 1$$
因为反正 $w$ 和 $w_0$ 都是要训练的变量，直接将参数训练到与这个对应的值即可，因此我们就不需要再明确地把常数 $c$ 给写出来了。这样，不但式子中的未知量就直接减少了一个，而且对于支持向量 $g(x)$ 计算出来的值直接就是类别标签值，方便后续计算。

## The Karush-Kuhn-Tucker Conditions

约束和优化目标通常是互相抗衡的，而约束之间也可能会存在抗衡，否则约束并无意义。要解决带约束的优化问题，我们可以从所求变量参与抗衡关系入手，因为当它们互相制衡时，问题能够取到最优值。当我们找到了它们互相制衡的平衡点时，我们就可以利用此时的相等关系求出问题的解。这就是“KKT 条件”的思想。

在这个问题中，变量 $\lVert\vec{w}\rVert$ 的抗衡表现在损失和约束之间，损失中它越小越好，但是小到一定程度的时候有可能导致不满足约束 $y_i(w^\intercal x_i+w_0) \ge 1$；而变量 $w_0$ 的抗衡存在于多个约束之间，$w_0$ 的移动可能在导致部分约束更容易满足的同时，部分约束更难满足。

如果将不满足的约束表现为损失，将抗衡关系表现为损失之间的权衡，我们就可以将目标和约束加权地合并到同一个损失表达式中，并通过总损失对所求变量满足梯度值为 $0$ 的等式表达抗衡关系的平衡点。即，对于总共 $n$ 个样本，我们可以将总损失写为
$$L(w,w_0,\alpha)=\frac{1}{2}\lVert w \rVert^2+\sum_{i=1}^n\alpha_i\big(1-y_i(w^\intercal x_i+w_0)\big)$$
而变量 $w$ 和 $w_0$ 的取值平衡点 $w^* $ 和 $w_0^* $ 符合
$$\frac{\partial L}{\partial w}=w-\sum_{i=1}^n\alpha_iy_ix_i=0$$
$$\frac{\partial L}{\partial w_0}=\sum_{i=1}^n\alpha_iy_i=0$$

对于 $w_0$，当损失函数取到最小值，也就是抗衡关系达到平衡点时，我们可以利用第二条等式进一步化简损失函数的公式，得到
$$L(w,w_0^*,\alpha)=\frac{1}{2}\lVert w \rVert^2+\sum_{i=1}^n\alpha_i(1-y_iw^\intercal x_i)$$

其中，权重 $\alpha$ 虽然还不好确定，但是我们可以知道它存在约束
$$\alpha_i \ge 0 \text{ for all } i$$
因为对于正值即损失的设定和另一个约束条件 $1-y_i(w^\intercal x_i+w_0) \le 0$ 来说， $\alpha < 0$ 是不符合损失公式的含义的，且满足约束条件的时候不应该存在损失。

## Lagrange Duality

不过，我们希望的是约束能够被完全满足，而不仅仅是造成损失，这个应该如何表示呢？尝试用自然语言描述就是，无论我的权重 $\alpha$ 如何取值，如何将约束条件带来的损失放大，我都希望能够取到这个最小值。那么，数学语言的对应描述就是

$$\text{minimize }\max_{\alpha \ge 0}L(w,w_0,\alpha)$$

从前文我们可以知道，当 $w_0$ 取到最优值 $w_0^* $ 时，损失函数能够被进一步化简为仅含两个变量的 $L(w,w_0^* ,\alpha)$。因此，此处进一步要求我们求出其余变量的最优解时，我们首先需要先利用前文中描述平衡点的等式，使用 $w$ 表达出 $\alpha^* $，得到 $\max_{\alpha \ge 0}L(w,w_0^* ,\alpha)=L(w,w_0^* ,\alpha^* )$ 的表达式，再求出其关于变量 $w$ 的最小值。

然而，使用 $w$ 表达 $\alpha^* $ 十分困难，使用 $\alpha$ 表达 $w^* $ 则十分简单。因此，我们可以利用拉格朗日对偶，将最小值和最大值的求解顺序调换，先利用前文中描述平衡点的等式，使用 $\alpha$ 表达出 $w^* $，得到 $\min_w L(w,w_0^* ,\alpha)=L(w^* ,w_0^* ,\alpha)$ 的表达式，再求出其关于变量 $\alpha$ 的最大值，即

$$\text{maximize }\min_w L(w,w_0^*,\alpha)$$

不过对于表达式 $w=\sum_{i=1}^n\alpha_iy_ix_i$，我们可以先将其下标 $i$ 替换成下标 $j$ 以避免歧义，然后再代入损失函数，得到

$$\begin{align*}
\min_w L(\alpha)&=\frac{1}{2}\lVert\sum_{j=1}^ny_jx_j\alpha_j\rVert^2+\sum_{i=1}^n\alpha_i\big(1-y_i(\sum_{j=1}^n\alpha_jy_jx_j)^\intercal x_i\big) \newline
&=\sum_{i=1}^na_i-\frac{1}{2}\sum_{i=1}^n\sum_{j=1}^n y_i y_j x_i^\intercal x_j \alpha_i \alpha_j
\end{align*}$$

至此，我们重新整理一下我们的目标和约束——对于寻找线性模型中最优决策边界的问题，我们已经将其转化成了寻找最优的参数 $\alpha$，即
$$\text{maximize }\sum_{i=1}^n a_i-\frac{1}{2}\sum_{i=1}^n\sum_{j=1}^n y_i y_j x_i^\intercal x_j \alpha_i \alpha_j$$
同时伴有约束：  
$$\sum_{i=1}^n\alpha_iy_i=0$$
$$a_i \ge 0 \text{ for all } a_i$$
$$1-y_i\big((\sum_{j=1}^n\alpha_jy_jx_j)^\intercal x_i+w_0\big) \le 0$$

## Linear SVM

求出了最优的参数 $\alpha$，再在前面的过程中找回对应的公式进行代入，我们就能确定我们的线性模型了。

对于线性的判别函数 $g(x)=w^\intercal x+w_0$，我们可以：
1. 首先搬出前文中梯度值为 $0$ 时得到的等式，我们有 $$w=\sum_{i=1}^n\alpha_iy_ix_i$$
2. 接下来有两种思路求解 $w_0$
   - 我们可以根据 $\alpha_i$ 是否为 $0$ 来判断样本 $x_i$ 是否为支持向量，然后根据支持向量样本集来求解 $w_0$。回顾损失中描述正确分类的项 $\alpha_i\big(1-y_i(w^\intercal x_i+w_0)\big)$ 以及约束 $\alpha_i \ge 0$ 和 $1-y_i(w^\intercal x_i+w_0) \le 0$，我们会发现这个项不可能大于零。因此，为了使得 $\alpha$ 取值最大，两者中有且仅有一方为 $0$，且当 $\alpha$ 不为 $0$ 时，$x_i$ 是支持向量。知道了支持向量以后，自然也就能求出它们中间最适合的 $w_0$。
   - 我们还可以通过遍历两个类别的样本点各自的 $w^\intercal x$。对于 $y_i=+1$ 的样本点，当 $w^\intercal x$ 取到最小值时，对应的样本就是标签为 $+1$ 的支持向量 $x_s$，此时有 $w_0 = 1-w^\intercal x_s$；而对于 $y_i=-1$ 的样本点，当 $w^\intercal x$ 取到最大值时，对应的样本就是标签为 $-1$ 的支持向量 $x_s$，此时有 $w_0 = -1-w^\intercal x_s$。因此，我们可以求出 $$w_0=-(\max_{s:y_s=-1} w^\intercal x_s + \min_{s:y_s=+1} w^\intercal x_s)/2$$

## Soft Margin Classification

对于存在噪声的数据，即使是线性可分任务，支持向量机也可能会因为噪声而变得束手无策。因此，我们可以尝试为支持向量机赋予容错能力：  
1. 将容纳的错误引入优化目标中，既能从约束变成容错，也能促使模型在训练过程中尽可能减少错误。我们用分类错误的样本点 $x_i$ 距离决策边界的距离 $\epsilon_i$，或者按照通常的叫法称为**松弛变量 slack variables**，作为该样本的容错距离，则此时要优化的目标可以表示为 $$\text{minimize } \frac{1}{2}\lVert w \rVert^2+\lambda\sum_{i=1}^n\epsilon_i$$ 其中，对于分类正确的样本，$\epsilon_i$ 取 $0$ 即可；式中还引入了变量 $\lambda$，使得支持向量机对错误样本的敏感程度可控；
2. 同时，我们对分类正确性的约束也需要加入松弛变量，使其相应地更新为 $$y_i(w^\intercal x_i+w_0) \ge 1-\epsilon_i$$
3. 另外，根据 $\epsilon$ 的含义，我们还能得出约束 $$\epsilon_i \ge 0 \text{ for all } i$$

接下来，我们还是借助 KKT 条件，列出
$$L(w,w_0,\epsilon,\alpha,\beta)=\frac{1}{2}\lVert w \rVert^2+\lambda\sum\epsilon_i+\sum_{i=1}^n\alpha_i\big(1-\epsilon_i-y_i(w^\intercal x_i+w_0)\big)+\sum_{i=1}^n\beta_i(-\epsilon_i)$$

其中变量 $\alpha$ 和 $\beta$ 均具有大于 $0$ 的约束。

从这步可以看出，损失对于变量 $w$，$w_0$ 的梯度值都没有改变，而对于新引入的松弛变量 $\epsilon_i$，损失梯度值为 $0$ 的等式是
$$\frac{\partial L}{\partial \epsilon_i}=\lambda-\alpha_i-\beta_i=0$$

不妨借助这个等式将原式子中的 $\beta$ 消掉，减少一个变量，得到
$$\begin{align*}
L(w,w_0,\epsilon,\alpha)&=\frac{1}{2}\lVert w \rVert^2+\lambda\sum\epsilon_i+\sum_{i=1}^n\alpha_i\big(1-\epsilon_i-y_i(w^\intercal x_i+w_0)\big)+\sum_{i=1}^n(\lambda-\alpha_i)(-\epsilon_i) \newline
&=\frac{1}{2}\lVert w \rVert^2+\sum_{i=1}^n\alpha_i\big(1-y_i(w^\intercal x_i+w_0)\big)
\end{align*}$$

可以发现，当式子中的变量变量 $\epsilon$ 取到最优值的时候，损失函数与原本没引入松弛变量时是一样的，唯一的不同之处在于多了一个 $\beta$ 变量大于 $0$ 的约束。根据等式我们可以将其合并进 $\alpha$ 的约束中得到
$$0 \le a_i \le \lambda \text{ for all } i$$

再接下来就是求解 $\alpha$，继续参考前文的方法将损失最小化转化为对偶问题即可，除了 $\alpha$ 的约束略有不同以外，其余并无不同。得到 $\alpha$ 后，参数 $w$ 依然是相同的求解公式，但 $w_0$ 的计算方式就不一样了。引入了松弛变量后，由于类似前面的解法我们需要求损失函数 $L$ 对于变量 $\alpha$ 的最大值，因此有
$$\alpha_i\big(1-\epsilon_i-y_i(w^\intercal x_i+w_0)\big)=0$$

其中对于有可能是支持向量的样本点，满足
$$1-\epsilon_i-y_i(w^\intercal x_i+w_0)=0$$

利用 $y_i^2=1$ 的隐藏条件，我们可以进一步移项得到
$$w_0=y_i(1-\epsilon_i)-w^\intercal x_i$$

然而这里还有一个不确定的变量 $\epsilon_i$，且只有当它为 $0$ 时对应的样本才是支持向量。再一次回顾损失函数，用类比的方法观察最后一项 $\beta_i(-\epsilon_i)$，不难想到当 $\beta_i=\lambda-\alpha_i$ 非 $0$ 时，取到最大值的 $L$ 中也有 $\epsilon_i=0$，即当我们的 $\alpha_i$ 满足 $0<\alpha_i<\lambda$ 时，对应的样本 $x_i$ 为支持向量。接下来，我们就可以对每一个支持向量求 $w_0$，然后通过求平均值等方式求出决策边界的 $w_0$ 了。

在一开始时，我们设置 $\lambda$ 来描述模型对错误样本的敏感程度，而这里对 $\alpha$ 的约束也展现了这个参数是如何影响模型的——相比于原本对所有 $\alpha_i>0$ 的样本感兴趣，$\lambda$ 作为上限迫使模型放弃一些错误程度较高的样本，而这个上限越低，模型关心的错误样本也就越少，也就越不敏感。

## Nonlinear SVM

支持向量机从提高泛化能力的角度来考虑和改进线性模型，显然是一个非常有价值的工作。但我们应该怎么使它的泛化能力惠及非线性可分的任务呢？我们只需要想办法把输入给线性支持向量机的样本变为线性可分的就好，而这个需要让输入预先经过一些非线性的操作。

回顾前面的内容，无论是否存在软间隔，我们都可以将模型的输入 $x$ 分离出来，记为
$$g(x)=w^\intercal x+w_0=\sum_{i=1}^n \alpha_iy_ix_i^\intercal x+w_0$$

当我们需要使用非线性处理 $\phi$ 将线性不可分的输入数据 $x$ 处理成 $\phi(x)$，那么，支持向量机的判别函数就变成了
$$g(x)=\sum_{i=1}^n \alpha_iy_i\phi(x_i)^\intercal \phi(x)+w_0$$

由于在前面的式子中，形如 $x_i^\intercal x_j$ 的输入组合经常出现，而且随着非线性的需求共同变化，因此我们可以单独将其分离出来，称为**核函数 kernel function**。其数学表示是
$$K(x_i,x_j)=\phi(x_i)^\intercal\phi(x_j)$$

那么支持向量机的判别函数也就可以写成
$$g(x)=\sum_{i=1}^n \alpha_iy_iK(x_i,x)+w_0$$

不过，对于形如 $\phi(x_i)^\intercal\phi(x)$ 的核函数，它其实可以理解为通过判断向量的方向相似度来评估输入样本点与所有样本点的相似程度，并加权求和以判断输入样本所属类别；但，除了用方向相似度，我们还可以参考 [Neural Network](https://brickloo.github.io/docs/pr/4/) 章节中的[ RBF 网络](https://brickloo.github.io/docs/pr/4/#radial-basis-function-rbf-network)，使用距离相近度来判断输入样本点与其他样本点的近似程度，这也就是支持向量机处理非线性可分问题时最常见的一种核函数——高斯核函数。它的数学表达式为
$$K(x_i,x_j)=\exp(-\frac{\lVert x_i-x_j\rVert^2}{2\sigma^2})$$