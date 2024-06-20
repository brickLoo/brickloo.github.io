---
date: '2024-04-17'
title: 'VQVAE'
weight: 2
math: true
prev: '/docs/论文阅读笔记/VQVAE'
url: '/docs/papers/DiffPure'
draft: true
---

> 笔记作者：BrickLoo
>   
> **论文标题**  
> 作者
> *会议*, PMLR 信息, 年份.  

[Publisher]() &nbsp; [GitHub]() &nbsp; [arXiv]() &nbsp; [Semantic Scholar]()

{{< callout type="warning" >}}
此页面为未完成的页面。
{{< /callout >}}

## 表征学习
无监督学习的场景下，如何让模型更好地学到有效表示数据的方式？称为表征（representation）

## VQVAE

使用离散的表征方式，提出向量离散化的变分自编码器

区别于 VAE，采用离散编码（语言模型中的词向量也是离散的）；同时 VAE 假设了先验概率满足静态的标准高斯分布，而 VQVAE 不局限于一种分布，而让模型去学习。

使用 VQ 的策略可以解决后验坍塌问题（posterior collapse）

（细化计划：VAE，后验坍塌）

VAE 参考：
- 苏剑林科学空间搜索，还没看
- [b 站讲解](https://www.bilibili.com/video/BV1Ns4y1J7tK/?spm_id_from=333.337.search-card.all.click&vd_source=55a96244deebfec823c98e520b55f7a4)

先参考[问答](https://datascience.stackexchange.com/questions/48962/what-is-posterior-collapse-phenomenon)再看相关[中文帖子](https://blog.csdn.net/wr1997/article/details/115255712)的进一步解释。[这篇](https://zhuanlan.zhihu.com/p/389295612)好像也可以作为参考但是还没仔细阅读。
> When posterior is not collapsed, $z_d$ (d-th dimension of latent variable $z$) is sampled from $q_{\phi}(z_d|x)=\mathcal{N}(\mu_d, \sigma^2_d)$, where $\mu_d$ and $\sigma_d$ are stable functions of input $x$. In other words, encoder distills useful information from $x$ into $\mu_d$ and $\sigma_d$.
>
> We say a posterior is collapsing, when signal from input $x$ to posterior parameters is either too weak or too noisy, and as a result, decoder starts ignoring $z$ samples drawn from the posterior $q_{\phi}(z|x)$.
> 
> The too noisy signal means $\mu_d$ and $\sigma_d$ are unstable and thus sampled $z$'s are also unstable, which forces the decoder to ignore them. By "ignore" I mean: output of decoder $\hat{x}$ becomes almost independent of $z$, which in practice translates to producing some generic outputs $\hat{x}$ that are crude representatives of all seen $x$'s.
> 
> The too weak signal translates to 
> $$q_{\phi}(z|x)\simeq q_{\phi}(z)=\mathcal{N}(a,b)$$
> which means $\mu$ and $\sigma$ of posterior become almost disconnected from input $x$. In other words, $\mu$ and $\sigma$ collapse to constant values $a$, and $b$ channeling a weak (constant) signal from different inputs to decoder. As a result, decoder tries to reconstruct $x$ by ignoring useless $z$'s which are sampled from $\mathcal{N}(a,b)$.