---
title: "Value-as-Return: A Two-Stage Framework to Align on the Optimal Score Function"
title_zh: 价值即回报：对齐最优评分函数的两阶段框架
authors: "Shikun Sun, Shuo Huang, Yiding Chen, Wen Sun, Jia Jia"
date: 2026-04-30
pdf: "https://openreview.net/pdf/a4e5e46d2359e44128b38fd1d8967987b3849378.pdf"
tags: ["query:diffusion-rl"]
score: 6.0
evidence: 利用最优价值函数作为短轨迹段的回报来对齐扩散模型
tldr: 该论文针对现有偏好优化方法将轨迹似然等同于最终状态概率的简化问题，提出价值即回报（VRPO）框架。第一阶段学习价值分布函数估计段级回报，第二阶段使用VRPO优化评分函数。理论上证明了在足够容量下等价于在倾斜分布上训练扩散过程。实验表明VRPO在图像和文本生成的对齐任务上优于DPO变体。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 现有DPO变体忽视了轨迹似然与最终状态概率的差异，导致对齐次优。
method: 两阶段框架：先学习段级价值分布，再基于VRPO优化扩散过程的评分函数。
result: 在多个对齐基准上，VRPO优于直接DPO方法，尤其在分布外场景下。
conclusion: 价值函数作为回报能更准确地引导扩散模型对齐，优于似然简化方法。
---

## Abstract
Reinforcement learning with diffusion models has shown strong potential, but existing approaches such as variants of Direct Preference Optimization (DPO) often rely on an inaccurate simplification: they equate trajectory likelihoods with final-state probabilities. This mismatch leads to suboptimal alignment. We address this limitation with a principled framework that leverages the optimal value function as the return for short trajectory segments. Our approach follows a two-stage procedure: (i) learning a value-distribution function to estimate segment-level returns, and (ii) applying our VRPO to refine the score function. We prove that, under sufficient model capacity, the resulting model is equivalent to training a diffusion process on the tilted distribution proportional to $p(x)\exp(\eta r(x))$. Experiments on large-scale diffusion models validate our analysis and show stable and consistent improvements over prior methods.

---

## 论文详细总结（自动生成）

# 论文总结：Value-as-Return: A Two-Stage Framework to Align on the Optimal Score Function

## 1. 核心问题与整体含义
- **研究动机**：现有基于扩散模型的强化学习对齐方法（如 Direct Preference Optimization (DPO) 变体）存在一个不准确的简化——将轨迹似然（trajectory likelihood）等同于最终状态概率（final-state probability）。这种简化导致优化目标与实际偏好对齐之间存在偏差，造成次优的对齐效果。
- **核心问题**：如何更精确地利用扩散过程的中间步骤信息，避免轨迹似然与最终状态概率的混淆，从而实现更优的偏好对齐。
- **整体含义**：提出价值即回报（Value-as-Return, VRPO）框架，通过引入最优价值函数作为短轨迹段的回报，纠正上述简化错误，使扩散模型的对齐更符合理论最优分布（倾斜分布）。

## 2. 方法论：核心思想、关键技术、算法流程
- **核心思想**：将强化学习中的最优价值函数（value function）作为扩散模型中短轨迹段的回报信号，而非直接使用最终状态的对数概率。通过两阶段训练实现对齐。
- **两阶段框架**：
  - **第一阶段：学习价值-分布函数（Value-Distribution Function）**  
    训练一个神经网络来估计给定短轨迹段（若干连续去噪步）的累计回报，即该段对应的价值分布。此阶段不改变扩散模型的评分函数。
  - **第二阶段：基于价值回报的偏好优化（VRPO）**  
    利用第一阶段学到的价值函数作为回报，通过类似策略梯度的方式优化扩散过程的评分函数（score function）。具体优化目标是使生成的样本分布向倾斜分布 \( p(x) \exp(\eta r(x)) \) 靠拢，其中 \( r(x) \) 是学习到的价值回报。
- **理论保证**：文章证明，在模型容量足够大的条件下，VRPO 等价于在倾斜分布（tilted distribution）上训练扩散过程，即直接最小化与目标分布的 KL 散度，避免了似然简化导致的偏差。
- **关键公式**：未给出具体公式，但核心是使用价值函数替代对数似然作为优化目标中的回报项。

## 3. 实验设计
- **数据集/场景**：未在元数据中明确列出具体数据集，但实验涵盖**图像生成**和**文本生成**两大领域的对齐任务，验证了方法的通用性。
- **基准（Benchmark）**：对比了多种 DPO 变体方法（如标准 DPO、DPO 在扩散模型上的适配版本等）。
- **对比方法**：主要包括 DPO 及其变体，以及可能的其他偏好优化方法（如 RLHF 类方法）。
- **评估指标**：未在元数据中详细说明，但提到了“对齐基准”（alignment benchmarks），通常包括人类偏好评分、自动评测指标（如 FID、CLIP score、BLEU/ROUGE 用于文本）等。

## 4. 资源与算力
- **未明确说明**：元数据中未提及使用的 GPU 型号、数量、训练时长等具体算力信息。需指出文中未提供资源详情，无法评估计算成本。

## 5. 实验数量与充分性
- **实验数量**：元数据提到在“多个对齐基准”上验证，暗示至少涵盖 2~3 个不同任务（图像生成、文本生成）及对应的数据集。可能包含消融实验（如是否使用价值函数、不同超参数设置）。
- **充分性与公平性**：实验对比了 DPO 变体，且特别强调在**分布外（out-of-distribution）场景**下 VRPO 表现更优，表明实验覆盖了泛化能力测试。但缺少具体的实验数量统计和统计显著性说明，总体上实验设计较为合理但不够详尽。

## 6. 主要结论与发现
- **VRPO 优于直接 DPO 方法**：在多个对齐基准上，VRPO 均取得稳定且一致的优势，尤其在分布外场景下提升明显。
- **价值函数作为回报更准确**：相比不准确的似然简化，使用最优价值函数能更精确地引导扩散模型对齐到目标偏好分布。
- **理论等价性成立**：实验验证了在足够容量下 VRPO 确实逼近倾斜分布训练的效果。

## 7. 优点
- **理论贡献**：明确指出了现有 DPO 变体的简化缺陷，并提供了严格的理论证明（等价于倾斜分布训练），提升了方法可解释性。
- **方法创新**：将强化学习中的价值函数概念引入扩散模型对齐，两阶段框架分离了价值估计与策略优化，降低训练耦合。
- **实验验证全面**：覆盖图像和文本两个主流生成领域，并特别测试了分布外泛化，体现了方法的鲁棒性。
- **稳定性**：相比 DPO 变体，VRPO 表现出更稳定的训练行为（元数据提到“stable and consistent improvements”）。

## 8. 不足与局限
- **算力信息缺失**：未报告训练资源，难以判断实际部署成本。
- **实验细节不充分**：元数据仅给出概要，具体数据集名称、超参数设置、基线方法的具体实现版本、消融实验的数量等均未提供，降低了可复现性。
- **可能存在的偏差风险**：价值函数的训练过程依赖额外网络和阶段，可能引入估计误差；若价值函数不准确，VRPO 的性能可能下降。
- **应用限制**：当前仅验证了图像和文本生成，对于更复杂的序列决策（如机器人控制）或长程依赖任务尚未测试；且需要短轨迹段的定义（步数选择），可能不同任务需调参。
- **理论假设**：等价性证明依赖于模型容量足够大，实际中有限容量下可能仍有偏差。

（完）
