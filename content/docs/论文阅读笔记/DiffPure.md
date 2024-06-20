---
date: '2024-02-25'
title: 'DiffPure'
weight: 2
math: true
prev: '/docs/论文阅读笔记/AutoAttack'
url: '/docs/papers/DiffPure'
draft: true
---

> 笔记作者：BrickLoo
>   
> **Diffusion Models for Adversarial Purification**  
> Weili Nie, Brandon Guo, Yujia Huang, Chaowei Xiao, Arash Vahdat, Animashree Anandkumar  
> *Proceedings of the 39th International Conference on Machine Learning*, PMLR 162:16805-16827, 2022.  

[Publisher](https://proceedings.mlr.press/v162/nie22a.html) &nbsp; [GitHub](https://github.com/NVlabs/DiffPure) &nbsp; [arXiv](https://arxiv.org/abs/2205.07460) &nbsp; [Semantic Scholar](https://www.semanticscholar.org/paper/Diffusion-Models-for-Adversarial-Purification-Nie-Guo/9b41d745fe3a76443bd0420bc5f2df28be2bd65f)

{{< callout type="warning" >}}
此页面为未完成的页面。
{{< /callout >}}

## 背景知识

### SDE

对于离散的扩散模型，加噪过程的数学表示为
$$x_t=\alpha_tx_{t-1}+\beta_t\epsilon_t$$
而 SDE 追求连续时间中的数学表达，其表示为
$$\mathop{dx}=f(x,t)\mathop{dt}+g(t)\mathop{dw}$$
先解释上面那条离散的式子。对于离散的情况，后一次的变量是前一次变量和噪声的加权和。对于连续时间的情况，式子中关注的不再是加入噪声后样本的样子，而是对于一个极其微小的时间 $dt$，样本的变化量 $dx$。