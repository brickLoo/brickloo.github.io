---
date: '2024-02-25'
title: 'DiffPure'
weight: 2
math: true
prev: '/docs/论文阅读笔记/AutoAttack'
url: '/docs/papers-notes/DiffPure'
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

## 简介

提到对抗性训练的弊端：针对性防御，复杂度高
提到对抗性净化的弊端：性能相比对抗性训练的结果较差（通常归因于纯化模型的特点）

创新点：将扩散模型用于图像纯化
要点：噪声的量需要控制在一定范围内，使得对抗性攻击能够被去除，且不破坏标签语义；使用伴随方法节约逆向复原过程的内存成本
