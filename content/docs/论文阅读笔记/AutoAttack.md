---
date: '2024-01-16'
title: 'AutoAttack'
math: true
prev: '/docs'
url: '/docs/papers-note/AutoAttack'
---

> 笔记作者：BrickLoo
>   
> Francesco Croce and Matthias Hein. 2020. Reliable evaluation of adversarial robustness with an ensemble of diverse parameter-free attacks. In Proceedings of the 37th International Conference on Machine Learning (ICML'20), Vol. 119. JMLR.org, Article 206, 2206–2216.  
> https://arxiv.org/abs/2003.01690  
> **Published in:** [ICML'20: Proceedings of the 37th International Conference on Machine Learning](https://dl.acm.org/doi/proceedings/10.5555/3524938)  
> **Official Code:** https://github.com/fra31/auto-attack

## 简介

### 价值

为模型鲁棒性提供更规范化的评估方式。`// 另一方面来说是改进了 PGD 攻击效果`

### 背景

最普遍的评估方法是使用 PGD 攻击。然而——
- 一方面，PGD 有较多超参数需要调整；`// 比较不公平`
- 另一方面，PGD 本身也不一定能保证成功率（作者认为可能的原因在于 PGD 的固定步长，以及损失函数的选择问题），导致性能高估；`// 固定的步长如果选择不当可能不收敛并影响性能`
- PGD 的迭代次数具有误导性，攻击的损失值在数次迭代后趋于稳定，攻击效果不会随着攻击的预算（即循环次数）的增加而显著增加；`// 也是固定步长的选择问题`
- 此外，作者认为评估过程应该提高攻击方式的多样性；

### 改进思路

提出两种 PGD 变体来分别解决问题（其中”迭代次数“是唯一的超参数），并结合两个同期工作中提出的攻击方法（白盒 FAB 攻击和黑盒 Square 攻击），综合评估模型鲁棒性。`// 引入的同期攻击方法不依赖梯度，可以突破梯度掩蔽防御`

### 实验思路

选取 35 篇论文中的 50 个鲁棒模型进行评估。

## APGD (Auto-PGD)

### 改进思路

- 步长以先大后小的规律进行调整
	- 大步长时为了避免方向受到局部梯度信息影响而剧烈变化，加入动量机制；
- 根据优化的趋势决定步长的调整时机，当优化速度放缓时尝试减小步长让其更好收敛
	- 每过一段间隔设置检查点，作为调整步长的时机并对最佳对抗样本进行保存
		- 按照比例而非迭代次数来设置检查点；`// 主要是为了照顾迭代次数比较少的攻击`
		- 作者认为越迭代收敛应该越快，间隔应该要逐步减小；`// 在局部搜索最优解相对更简单`
	- 检查点之间，如果成功优化的次数少于一定比例，则减小步长
		- 成功优化的比例可以反映优化速度；
		- 考虑极端情况，算法可能会让对抗样本在某一个位置徘徊，陷入循环；
	- 如果与上一次检查点相比，步长没有改变，最佳对抗样本也没有改变，则减小步长
		- 这个条件是用来完善另一个条件中的循环问题；

### 超参数选择

- 初始步长：$2\epsilon$
- 步长减小的比例：$0.5$
- 动量参数：本轮影响力 $0.75$，上一轮影响力 $0.25$
- 检查点选取方式：第 $\lceil p_j N_{iter} \rceil$  个循环为检查点，其中 $p_0 = 0$， $p_1 = 0.22$， $p_{j+1} = p_j + max \lbrace p_j-p_{j-1}-0.03,0.06 \rbrace$ 
- 成功优化比例的阈值：$0.75$

### 步骤

![APGD 算法伪代码](https://raw.githubusercontent.com/brickloo/myfigs/main/APGD.png)

### 实验

![实验对比 APGD 与运用了动量的 PGD](https://raw.githubusercontent.com/brickloo/myfigs/main/APGD-fig1.png)  
图中虚线为应用动量机制的 PGD，实线为作者提出的 APGD；  
图中比较值得注意的点是，
- 应用动量的 PGD 并非步长越小越好，超参数的选取会影响攻击效果；
- 不同场景下的最优步长可能不同；
- 迭代次数较多时 APGD 会搜索得更慢，但是最终效果更好；
- 迭代次数较少时 APGD 在不用选择参数的情况下攻击效果也还算不错；

## DLR (Difference of Logits Ratio)

### 背景

PGD 攻击方法的迭代中需要用到损失函数的梯度信息来搜索对抗样本。前面提到作者认为损失函数的选择可能是 PGD 无法保证攻击成功率的原因之一。

原因在于，常用的损失函数是 CE（交叉熵）损失，这种方法具有平移不变性，但是不具有缩放不变性。

作者通过对 CE 求导，论证在不合适的缩放下，交叉熵损失的梯度值可以接近 0，使得 PGD 迭代时几乎无法改进，从而影响攻击性能，造成模型高估。这种现象在 C&W 攻击方法的论文中被发现。（<mark> 数学原理没太懂，论文里没细讲 </mark>）

作者同时做了实验进行验证：  
![CE 损失的梯度消失实验](https://raw.githubusercontent.com/brickloo/myfigs/main/APGD-fig2.png)  
图中横轴表示缩放操作中，分母选取的大小；（<mark> 不太明白是怎么对模型应用缩放的 </mark>）  
图中比较值得注意的点是，
- 当不缩放时梯度信息几乎全部消失；
- 进行缩放后攻击效果显现，模型鲁棒性变差，说明实验中如果没有考虑这个因素并进行处理可能会导致鲁棒性高估；

### 改进

作者基于 C&W 中选取的损失函数进行改进，提出名为 DLR 的替代损失函数，旨在维持缩放不变性，保证其自由度与分类器的决策相等
$$DLR(x,y)=-\frac{z_y-\mathop{max}\limits_{i \neq y}\{z_i\}}{z_{\pi_1}-z_{\pi_3}}$$
作者同时为针对性攻击的场景设计了替代损失函数 Targeted-DLR
$$Targeted\text{-}DLR(x,y)=-\frac{z_y-z_t}{z_{\pi_1}-(z_{\pi_3}+z_{\pi_4})/2}$$

### 实验

作者进一步将不同损失函数的 APGD 与 PGD / 带动量机制的 PGD 进行对比，并尝试了包括 $L_\infty$ 和 $L_2$ 范数限制的攻击场景；  
得出的结论是，
- APGD 的攻击性能优于 PGD / 带动量机制的 PGD；
- DLR 损失相比 CE 损失和 CW 的损失效果相对更好，攻击失败的情况更少；