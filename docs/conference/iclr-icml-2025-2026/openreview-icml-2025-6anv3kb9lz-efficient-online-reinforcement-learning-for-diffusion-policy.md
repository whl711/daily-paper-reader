---
title: Efficient Online Reinforcement Learning for Diffusion Policy
title_zh: 面向扩散策略的高效在线强化学习
authors: "Haitong Ma, Tianyi Chen, Kai Wang, Na Li, Bo Dai"
date: 2025-05-01
pdf: "https://openreview.net/pdf?id=6Anv3KB9lz"
tags: ["query:diffusion-rl"]
score: 9.0
evidence: 重加权分数匹配实现扩散策略的高效在线RL
tldr: 针对扩散策略在线强化学习训练困难、计算成本高的问题，提出重加权分数匹配方法，通过对去噪分数匹配损失进行重加权，在保持最优解和低计算成本的同时，使得扩散策略可以在线训练，显著提升了样本效率和可扩展性。
source: ICML-2025-Accepted
selection_source: conference_retrieval
motivation: 扩散策略在线RL难以从最优策略采样，反向传播计算昂贵且不稳定。
method: 提出重加权分数匹配（RSM），通过加权去噪分数匹配损失实现高效训练。
result: 在多种任务上验证了RSM的样本效率和性能优势。
conclusion: RSM为扩散策略在线RL提供了一种高效且可扩展的范式。
---

## Abstract
Diffusion policies have achieved superior performance in imitation learning and offline reinforcement learning (RL) due to their rich expressiveness. However, the conventional diffusion training procedure requires samples from target distribution, which is impossible in online RL since we cannot sample from the optimal policy. Backpropagating policy gradient through the diffusion process incurs huge computational costs and instability, thus being expensive and not scalable. To enable efficient training of diffusion policies in online RL, we generalize the conventional denoising score matching by reweighting the loss function. The resulting Reweighted Score Matching (RSM) preserves the optimal solution and low computational cost of denoising score matching, while eliminating the need to sample from the target distribution and allowing learning to optimize value functions. We introduce two tractable reweighted loss functions to solve two commonly used policy optimization problems, policy mirror descent and max-entropy policy, resulting in two practical algorithms named Diffusion Policy Mirror Descent (DPMD) and Soft Diffusion Actor-Critic (SDAC). We conducted comprehensive comparisons on MuJoCo benchmarks. The empirical results show that the proposed algorithms outperform recent diffusion-policy online RLs on most tasks, and the DPMD improves more than 120% over soft actor-critic on Humanoid and Ant.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 核心问题与整体含义（研究动机和背景）
- **研究动机**：扩散策略因表达能力强，在模仿学习和离线强化学习中表现优异。但传统扩散训练需要从目标分布采样，而在线强化学习中无法从最优策略采样。通过扩散过程反向传播策略梯度计算成本高且不稳定，导致训练困难、可扩展性差。
- **整体含义**：旨在解决扩散策略在在线RL场景中高效训练的问题，降低计算负担，同时保持样本效率和性能。

## 2. 方法论：核心思想、关键技术细节
- **核心思想**：对传统去噪分数匹配（Denoising Score Matching）损失进行重加权，提出**重加权分数匹配（Reweighted Score Matching, RSM）**，使损失函数在保持最优解和低计算成本的同时，不需要从目标分布采样，且能学习优化值函数。
- **关键技术细节**：
  - RSM通过引入权重函数修改去噪分数匹配的损失形式，使得优化目标等价于策略梯度或最大熵目标。
  - 基于RSM推导出两种可计算的加权损失函数，分别对应两种常用策略优化问题：
    1. **策略镜像下降（Policy Mirror Descent）** → 提出 **Diffusion Policy Mirror Descent (DPMD)** 算法。
    2. **最大熵策略（Max-Entropy Policy）** → 提出 **Soft Diffusion Actor-Critic (SDAC)** 算法。
- **算法流程（文字说明）**：
  - DPMD：在在线RL中，使用RSM损失更新扩散策略参数，同时利用值函数估计策略梯度，实现策略镜像下降。
  - SDAC：结合软策略评估和RSM损失，更新actor和critic，最大化熵正则化回报。

## 3. 实验设计
- **数据集/场景**：MuJoCo基准测试环境（包括Humanoid、Ant等任务）。
- **Benchmark**：对比了多种基线方法，包括**软Actor-Critic（SAC）**以及近期的扩散策略在线RL方法。
- **对比方法**：具体名称未在摘要中列出，但提及“recent diffusion-policy online RLs”和SAC。

## 4. 资源与算力
- **未明确说明**：论文摘要和元数据未提及使用的GPU型号、数量、训练时长等算力信息。

## 5. 实验数量与充分性
- **实验数量**：在MuJoCo的多个任务（如Humanoid、Ant等）上进行了全面比较，但具体任务数量和消融实验未详细列出。
- **充分性与公平性**：摘要称“comprehensive comparisons”，且DPMD在Humanoid和Ant上比SAC提升超过120%，表明实验有一定覆盖。但缺乏更多细节（如超参数设置、随机种子次数等），无法完全判断是否足够充分和公平。

## 6. 主要结论与发现
- **主要结论**：提出的RSM方法及其两种算法DPMD和SDAC在大多数MuJoCo任务上优于近期扩散策略在线RL方法，并且相比SAC有显著提升（DPMD提升超过120%）。RSM为扩散策略在线RL提供了一种高效且可扩展的范式。

## 7. 优点
- **方法亮点**：
  - RSM保留了去噪分数匹配的低计算成本和最优解性质，同时移除对目标分布采样的依赖，解决了在线RL的核心障碍。
  - 推导出两种实用算法，直接适配经典策略优化框架。
- **实验亮点**：
  - 在连续控制基准上取得大幅提升，验证了方法的有效性。

## 8. 不足与局限
- **实验覆盖**：仅使用了MuJoCo环境，未在更复杂或高维任务（如机器人操作、视觉环境）上验证，泛化性存疑。
- **偏差风险**：未报告多次独立运行的标准差或置信区间，难以评估结果的稳定性。
- **应用限制**：RSM依赖于权重函数的设计，可能需要针对不同任务调整；未讨论与离线数据结合的情况，可能限制实际应用。
- **计算资源**：未说明训练成本，无法判断可扩展性是否真正实现。

（完）
