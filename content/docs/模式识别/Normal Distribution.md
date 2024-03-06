---
date: '2024-03-01'
title: 'Normal Distribution'
weight: 3
math: true
url: '/docs/pr/3'
---

笔记作者：BrickLoo

## 单变量的正态分布

对于变量 $x$，
- Probability:
$$p(x)=\frac{1}{(2\pi)^{1/2}|\sigma^2|^{1/2}} \exp \left[-\frac{1}{2}(x-\mu)(\sigma^2)^{-1}(x-\mu)\right]$$

其中，
- Mean:
$$\mu=\int xp(x)\mathop{dx}$$
- Variance:
$$\sigma^2=\int(x-\mu)^2p(x)\mathop{dx}$$

## 多变量的正态分布

对于 $d$ 维变量 $x = (x_1,x_2,\dots,x_d)^\intercal$，
- Probability: 
$$p(x)=\frac{1}{(2\pi)^{d/2}|\Sigma|^{1/2}} \exp \left[-\frac{1}{2}(x-\mu)^\intercal(\Sigma)^{-1}(x-\mu)\right]$$

其中，
- Mean:
$$\mu=(\mu_1,\mu_2,\dots,\mu_d)^\intercal$$
- Variance:
$$\Sigma=\int(x-\mu)(x-\mu)^\intercal p(x)\mathop{dx}$$

## 正态分布下的判别函数

回顾 Bayes 章节，我们可以知道一种常见的判别函数是
$$g_i(x)=p(x|\omega_i)p(\omega_i)$$
它将观测到的变量值 $x$ 作为条件输入，评估变量 $\omega$ 的类别为 $i$ 的可能性。

某些应用场景中，在变量 $\omega$ 的类别确定的情况下，它的其他可观测变量 $x_i$ 的均服从某种正态分布，即 $p(x|\omega_i) \sim N(\mu_i,\Sigma_i)$，则我们可以将其代入概率公式得到
$$g_i(x) = \frac{1}{(2\pi)^{d/2}|\Sigma_i|^{1/2}} \exp \left[-\frac{1}{2}(x-\mu_i)^\intercal(\Sigma_i)^{-1}(x-\mu_i)\right]p(\omega_i)$$
对于这个判别函数我们可以取自然对数来消除式子中的各种指数，变成
$$g_i(x) = -\frac{d}{2}\ln(2\pi)-\frac{1}{2}\ln(|\Sigma_i|)-\frac{1}{2}(x-\mu_i)^\intercal\Sigma_i^{-1}(x-\mu_i)+\ln\big(p(\omega_i)\big)$$
对于不同类别 $i$ 之间的比较，由于输入的 $x$ 是固定的，含有维度的项 $-\frac{d}{2}\ln(2\pi)$ 是个常量，因此在判别函数中我们可以将其省略。另外，把含有自然对数的常数项放在一起看起来更优雅一点，我们再调整一下，得到
$$g_i(x) = -\frac{1}{2}(x-\mu_i)^\intercal\Sigma_i^{-1}(x-\mu_i)-\frac{1}{2}\ln(|\Sigma_i|)+\ln\big(p(\omega_i)\big)$$
这个公式就是最普适的判别函数。

其实到这里公式已经相对简单，可以直接用了。不过注意到前面的项 $(x-\mu_i)^\intercal\Sigma_i^{-1}(x-\mu_i)$ 类似于差的平方，因此我们还可以继续将 $g_i(x)$ 展开成形如 $a_ix^2+b_ix+c_i$ 的形式。我们先看局部

$$\begin{align*}
(x-\mu_i)^\intercal\Sigma_i^{-1}(x-\mu_i)&=(x^\intercal\Sigma_i^{-1}-\mu_i^\intercal\Sigma_i^{-1})(x-\mu_i)\newline
&=x^\intercal\Sigma_i^{-1}x-x^\intercal\Sigma_i^{-1}\mu_i-\mu_i^\intercal\Sigma_i^{-1}x+\mu_i^\intercal\Sigma_i^{-1}\mu_i
\end{align*}$$

对于二次项，$x^\intercal W_i x$ 就是比较常见的形式，不动。

对于一次项，我们希望整理成 $w_i^\intercal x$ 的形式，上式第二项调整一下位置刚好有 $x^\intercal\Sigma_i^{-1}\mu_i=(\Sigma_i^{-1}\mu_i)^\intercal x$。而至于第三项是否可以合并，我们可以把括号再拆开看看：$(\Sigma_i^{-1}\mu_i)^\intercal x=\mu^\intercal(\Sigma_i^{-1})^\intercal x$，发现刚好差了一个转置。不过这里实际上还隐含了一个条件，即协方差矩阵恰好是对称的，满足 $\Sigma^\intercal=\Sigma$，因为两个变量之间的协方差不存在先后关系；同时对称矩阵的逆矩阵依旧是对称矩阵（不会推导的话可以参考这条[知乎问答](https://www.zhihu.com/question/396786869)），因此一次项最终可以统一合并为 $(\Sigma_i^{-1}\mu_i)^\intercal x$ 的形态。

将结果代入回前面的 $g_i(x)$，可以得到
$$g_i(x)=x^\intercal W_i x+w_i^\intercal x + b_{i}$$
其中
$$\begin{align*}
W_i&=-\frac{1}{2}\Sigma_i^{-1}\newline
w_i&=\Sigma_i^{-1}\mu_i\newline
b_i&=-\frac{1}{2}\mu_i^\intercal\Sigma_i^{-1}\mu_i-\frac{1}{2}\ln(|\Sigma_i|)+\ln\big(p(\omega_i)\big)
\end{align*}$$

## 分布参数的最大似然估计

由于这个部分的篇幅有点长，所以这里断成小的章节来叙述，但是内容是连续而并非并列的。

### 为何需要正态分布参数

我们在前面 Bayes 的章节讲到，对于难以“观测”和估计的变量 $\omega$，我们在优先确定其取值 $\omega_i$ 的情况下，可以统计其它方便“观测”的变量 $x$ 的取值比例情况 $p(x|\omega_i)$，并利用这些统计信息通过 Bayes 公式反向解决问题。

然而在许多应用场景中，这个变量 $x$ 的取值比例情况服从正态分布 $N(\mu_i,\Sigma_i)$，也就是说要使用 $p(x|\omega_i)$ 的数学表达式来计算就必须知道它所对应的正态分布参数 $\theta_i=(\mu_i,\Sigma_i)$。

但是，我们采样得到的对应类别的数据集 $D_i$ 总归是离散的，采样也有可能是不充分的，正态分布的参数也不止一种可能。因此我们需要考虑我们目前拥有的数据集来估计出最有可能的参数 $\theta_i$，也就是找到最大的 $p(\theta_i|D_i)$。

### 使用 Bayes 公式来转换

又回到了熟悉的格式。不过从分布中获得采样容易，从采样中估出分布难，这里显而易见是需要继续使用 Bayes 公式来反向解决问题的——不过考虑到一直带着类别下标 $i$ 挺影响体验的，而且对于无论那个类别 $i$ 我们都希望计算出这个正态分布参数 $\theta_i=(\mu_i,\Sigma_i)$，所以我们不妨就此忽略掉它：
$$p(\theta|D)=\frac{p(D|\theta)p(\theta)}{p(D)}$$

回顾这个公式的本质，它在这里所表示的是：在拥有了数据的情况下，假设这些数据是从标准正态分布中采样的，那么这个分布的参数刚好为这个数值的概率，其实是等同于我先在所有可能的标准正态分布参数中选到了这个参数数值，再在满足这个参数的正态分布里刚好采样到这些样本数据的概率。

注意，这里我们是从纯数值的角度来考虑这个问题，来尝试为这些数据赋予一个相对最合理的正态分布参数，而不考虑样本 $x$ 是否有什么取值上的概率限制（例如人的身高一般不超过 $2m$ 等）——不然就要变成条件概率了。因此在这个情况下，样本 $x$ 出现的概率在数值域中应当是平均的，参数 $\theta$ 的选择在所有可能性中也是平均的，所以在对比中，我们可以忽略这个常量 $p(\theta)$。

另外，无论对于哪一个类别，我们都是在固定类别的数据集 $D$ 下寻找可能性最大的参数值 $\hat\theta$，因此 $p(D)$ 是一个常量，在不同参数值的对比中也可以忽略。

所以，我们只需要找到一个 $\theta=\hat\theta$ 使似然项 $p(D|\theta)$ 最大，即可将这个 $\hat\theta$ 值作为这些数据所服从的正态分布的参数。这便是最大似然估计。

### 对数似然与求导

到这一步，再求最大值就是常规的数学思路了——尝试代入正态分布的数学表达式，然后求导。我们先处理一下这个式子，将整个数据集 $D$ 拆成独立的样本 $x_j$ 来估计
$$p(D|\theta)=\prod^n_{j=1}p(x_j|\theta)$$
这样就可以把正态分布的概率公式代入了。

然而要求导的话，连乘处理起来会比较麻烦，不妨再取个对数将连乘转换成连加
$$\ln\big(p(D|\theta)\big)=\sum^n_{j=1}\ln\big(p(x_j|\theta)\big)$$

这样子再求导就方便多了,还能顺便把正态分布中的指数给消掉。人们通常把这种用对数处理的似然称为对数似然（Log-likelihood）。

接下来尝试解这个求导后等于零的等式就行
$$\nabla_\theta\ln\big(p(D|\theta)\big)=\sum^n_{j=1}\nabla_\theta\ln\big(p(x_j|\theta)\big)=0$$

### 简单案例

相对来说比较简单的一种情况，是样本 $x$ 中的各种变量相互独立，即此时 $\Sigma=\sigma^2I$（即协方差矩阵中只有对角线上有值）

我们先代入正态分布的概率公式
$$\sum^n_{j=1}\ln\big(p(x_j|\mu,\sigma)\big)=\sum^n_{j=1}\big(-\frac{1}{2}(x_j-\mu)^\intercal\Sigma^{-1}(x_j-\mu)-\frac{d}{2}\ln(2\pi)-\frac{1}{2}\ln(|\Sigma_i|)\big)$$
然后将 $\Sigma$ 替换掉
$$\begin{align*}
\sum^n_{j=1}\ln\big(p(x_j|\mu,\sigma)\big)&=\sum^n_{j=1}\big(-\frac{1}{2\sigma^2}(x_j-\mu)^\intercal I(x_j-\mu)-\frac{d}{2}\ln(2\pi)-\frac{1}{2}\ln(\sigma^2)\big) \newline
&=\sum^n_{j=1}\big(-\frac{1}{2\sigma^2}(x_j^2-2\mu\cdot x_j+\mu^2)-\frac{d}{2}\ln(2\pi)-\ln(\sigma)\big)
\end{align*}$$
就很容易求导并列出使之为 $0$ 的等式了
$$\begin{align*}
\nabla_\mu\sum^n_{j=1}\ln\big(p(x_j|\mu,\sigma)\big)&=\sum^n_{j=1}\frac{1}{\sigma^2}(x_j-\mu)=0 \newline
\nabla_\sigma\sum^n_{j=1}\ln\big(p(x_j|\mu,\sigma)\big)&=\sum^n_{j=1}(-\frac{1}{\sigma}+\frac{(x_j-\mu)^2}{\sigma^3})=0
\end{align*}$$
最后可以解得最优值分别为
$$\begin{align*}
\hat\mu&=\frac{1}{n}\sum^n_{j=1}x_j \newline
\hat\sigma^2&=\frac{1}{n}\sum^n_{j=1}(x_j-\mu)^2
\end{align*}$$
这里方差中的 $\mu$ 显然是用最优的 $\hat\mu$ 来代入计算，即 $\hat\sigma^2=1/n\sum^n_{j=1}(x_j-\hat\mu)^2$。

直到这里我们其实可以发现，实际中在应用正态分布的最大似然参数估计时，我们可以直接用数据的统计学指标，而不需要再根据这个最大似然估计的原理来慢慢推导。这个结果还是非常符合直觉的：既然我直接用数据能统计出这些统计学指标，那么他们满足的分布正是这个参数的概率应该是相对最大的。

不过需要注意一下前提条件，这里变量之间满足相互独立，问题难度才能变成中小学统计题；但，如果不满足变量相互独立，在求导过程中就会比较麻烦，也不确定最终得到的结论是否一致。关于后者，笔者暂时还没有学习和验证，先留下一篇看到的[知乎文章](https://zhuanlan.zhihu.com/p/305171795)，或许可以作为参考。