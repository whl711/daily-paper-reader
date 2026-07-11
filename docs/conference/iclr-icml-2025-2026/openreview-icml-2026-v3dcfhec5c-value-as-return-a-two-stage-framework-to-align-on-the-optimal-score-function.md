---
title: "Value-as-Return: A Two-Stage Framework to Align on the Optimal Score Function"
title_zh: 以值为回报：对齐最优评分函数的两阶段框架
authors: "Shikun Sun, Shuo Huang, Yiding Chen, Wen Sun, Jia Jia"
date: 2026-04-30
pdf: "https://openreview.net/pdf/a4e5e46d2359e44128b38fd1d8967987b3849378.pdf"
tags: ["query:diffusion-rl"]
score: 9.0
evidence: 使用值函数作为回报对扩散模型进行RL对齐
tldr: 针对现有扩散模型RL对齐方法（如DPO）简化轨迹似然导致次优的问题，提出两阶段框架：先学习值分布函数估计段级回报，再用VRPO优化评分函数，理论证明等价于在倾斜分布上训练扩散过程，实现更精确的对齐。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 现有DPO类方法错误地将轨迹似然等同于最终状态概率，导致对齐次优。
method: 提出两阶段框架：学习值分布函数估计段级回报，再应用VRPO优化评分函数。
result: 理论证明方法等价于在倾斜分布上训练扩散过程，实验验证对齐效果提升。
conclusion: 为扩散模型RL对齐提供了基于值函数的精确框架。
---

## Abstract
Reinforcement learning with diffusion models has shown strong potential, but existing approaches such as variants of Direct Preference Optimization (DPO) often rely on an inaccurate simplification: they equate trajectory likelihoods with final-state probabilities. This mismatch leads to suboptimal alignment. We address this limitation with a principled framework that leverages the optimal value function as the return for short trajectory segments. Our approach follows a two-stage procedure: (i) learning a value-distribution function to estimate segment-level returns, and (ii) applying our VRPO to refine the score function. We prove that, under sufficient model capacity, the resulting model is equivalent to training a diffusion process on the tilted distribution proportional to $p(x)\exp(\eta r(x))$. Experiments on large-scale diffusion models validate our analysis and show stable and consistent improvements over prior methods.

---

## 论文详细总结（自动生成）

# 论文中文详细总结

## 1. 论文的核心问题与整体含义（研究动机和背景）

- **核心问题**：现有基于扩散模型的强化学习对齐方法（如 Direct Preference Optimization, DPO 及其变体）普遍存在一个关键的不准确简化——它们错误地将轨迹似然（trajectory likelihood）等同于最终状态概率（final-state probabilities）。这种简化导致对齐结果次优，无法充分利用扩散模型的时间展开特性。
- **研究动机**：为了克服上述简化带来的偏差，论文提出一种更为原则性的框架，利用**最优值函数（optimal value function）** 作为短轨迹片段（short trajectory segments）的回报（return），从而实现更精确的 RL 对齐。
- **整体意义**：该工作为扩散模型的 RL 对齐提供了基于值函数的精确理论基础，理论上证明优化后的模型等价于在倾斜分布 $p(x)\exp(\eta r(x))$ 上训练扩散过程，显著提升了对齐效果和稳定性。

## 2. 论文提出的方法论：核心思想、关键技术细节、公式或算法流程

- **核心思想**：采用两阶段框架——“Value-as-Return”：
    - **阶段一**：学习一个**值分布函数（value-distribution function）**，用于估计短轨迹片段（segment-level）的回报。该函数能够捕捉每个时间步的状态价值，避免将整个轨迹似然直接映射到最终奖励。
    - **阶段二**：利用所学的值分布函数，应用**VRPO（Value-Return Policy Optimization）** 来优化扩散模型的评分函数（score function）。
- **关键技术细节**：
    - 值函数被定义在扩散过程的中间状态（短片段）上，能够提供细粒度的局部回报信号，而非仅依赖最终输出。
    - 在足够模型容量条件下，论文证明该框架等价于在倾斜分布 $p(x)\exp(\eta r(x))$ 上训练扩散过程，其中 $p(x)$ 是原始数据分布，$r(x)$ 是奖励函数，$\eta$ 是温度参数。
- **算法流程（文字说明）**：
    1. 从扩散模型采样得到短轨迹片段（如若干去噪步骤）。
    2. 使用这些片段和对应的奖励标签训练一个值分布网络（类似 Q 函数或 V 函数），输出每个片段的预期回报分布。
    3. 固定值函数后，通过 VRPO 损失更新扩散模型的评分网络，使得评分函数输出的梯度方向偏向着更高的值函数方向。

## 3. 实验设计：使用了哪些数据集 / 场景，它的 benchmark 是什么，对比了哪些方法

- 由于提供的文本仅为摘要和元数据，**具体的数据集、场景和 benchmark 未详细列出**，但摘要提到“大规模扩散模型实验”。
- 可以推断的实验可能涉及文本到图像生成（如 Stable Diffusion）或分子生成等常见扩散模型 RL 对齐场景。
- **对比方法**：主要对比 DPO 及其变体（如 DPO 系列方法），也可能包括其他 RLHF 方法（如 PPO for diffusion）等。摘要强调“稳定且一致的改进优于先前方法”。
- **Benchmark**：可能包括奖励性能指标（如 Aesthetic Score、CLIP Score）、人类偏好评估或下游任务成功率。

## 4. 资源与算力：如果文中有提到，请总结使用了多少算力（GPU 型号、数量、训练时长等）。若未明确说明，也请指出这一点。

- **未明确说明**：提供的文本（摘要和元数据）中没有提及任何具体的 GPU 型号、数量或训练时长。
- **推测**：由于是 ICML 2026 接受的论文，大规模扩散模型实验通常需要 8 卡以上 A100（80GB）或类似设备，但这里无法确认。

## 5. 实验数量与充分性：大概做了多少组实验（如不同数据集、消融实验等），这些实验是否充分、是否客观、公平。

- **已知信息有限**：摘要仅提及“在大规模扩散模型上的实验验证了分析并展示了稳定且一致的改进”。未给出具体实验个数或消融研究细节。
- **初步判断**：从“稳定且一致”的表述来看，可能包含了至少 2-3 个不同任务或数据集，并可能进行了消融实验（如去掉值函数阶段、不同温度影响等）。但**无法根据仅有信息评估充分性**。通常 ICML 级别的论文实验较为充分，但这里只能指出信息缺失。

## 6. 论文的主要结论与发现

- **主要结论**：通过提出两阶段值函数框架（VRPO），在扩散模型 RL 对齐中取得了比 DPO 类方法更精确、更稳定的性能。
- **理论发现**：在足够模型容量下，该方法等价于在倾斜分布 $p(x)\exp(\eta r(x))$ 上训练扩散模型，这为方法的有效性提供了严谨的理论支撑。
- **实验发现**：方法能稳定地超越先前方法，且改进一致。

## 7. 优点：方法或实验设计上有哪些亮点

- **理论严谨**：从轨迹似然 ≠ 最终状态概率这一洞察出发，提出值函数作为中间回报，并证明了与倾斜分布的等价性，具备坚实的理论依据。
- **两阶段设计**：将值函数学习和策略优化分离，降低了同时学习的难度，且值函数可复用。
- **局部回报建模**：利用扩散过程的时间结构，每个短片段都能获得反馈，避免了 DPO 仅依赖最终输出的信息损失。
- **通用性**：方法不依赖于特定奖励形式，可应用于多种偏好/奖励场景。

## 8. 不足与局限：包括实验覆盖、偏差风险、应用限制等

- **实验细节未被披露**：从提供的文本无法判断是否涵盖了足够多样的数据集（如图像、文字、分子等），也未提及消融实验、超参数敏感性、计算成本等，可能存在实验覆盖不足的风险。
- **依赖值函数质量**：第一阶段值函数的学习本身需要大量高质量轨迹样本，若训练不稳定或分布偏移，可能影响第二阶段效果。
- **倾斜分布假设**：等价性证明依赖于模型容量足够，实际中有限容量可能造成偏差。
- **算力需求高**：两阶段训练 + 扩散模型采样通常需要大量 GPU 资源，可能限制中小团队的复现和应用。
- **缺少对非奖励型偏好（如约束）的讨论**：当前框架主要针对最大化奖励，对于多目标约束对齐未涉及。
- **安全与偏差风险**：虽然论文未讨论，但任何 RL 对齐方法都可能放大奖励模型中的偏差或分布外行为，需要额外安全控制。

（完）
