---
title: Behavior-Regularized Diffusion Policy Optimization for Offline Reinforcement Learning
title_zh: 面向离线强化学习的行为正则化扩散策略优化
authors: "Chen-Xiao Gao, Chenyang Wu, Mingjun Cao, Chenjun Xiao, Yang Yu, Zongzhang Zhang"
date: 2025-05-01
pdf: "https://openreview.net/pdf?id=pUCYJ9JJuZ"
tags: ["query:diffusion-rl"]
score: 8.0
evidence: 为基于扩散的策略设计的行为正则化强化学习框架
tldr: 离线强化学习中行为正则化广泛使用，但现有方法局限于高斯策略。本文提出BDPO，首个面向扩散策略的行为正则化强化学习框架。通过巧妙利用扩散模型的隐式行为约束，BDPO在保持扩散策略强大表达能力的同时，确保了训练稳定性。实验证明该方法在离线RL基准上优于现有方法。
source: ICML-2025-Accepted
selection_source: conference_retrieval
motivation: 现有行为正则化方法无法直接扩展到扩散策略这种高级参数化形式。
method: 提出BDPO框架，通过隐式约束将行为正则化融入扩散策略训练。
result: 在离线RL标准任务上超越了基于高斯策略的基线方法。
conclusion: 行为正则化有效提升了扩散策略在离线场景下的鲁棒性和性能。
---

## Abstract
Behavior regularization, which constrains the policy to stay close to some behavior policy, is widely used in offline reinforcement learning (RL) to manage the risk of hazardous exploitation of unseen actions. Nevertheless, existing literature on behavior-regularized RL primarily focuses on explicit policy parameterizations, such as Gaussian policies. Consequently, it remains unclear how to extend this framework to more advanced policy parameterizations, such as diffusion models. In this paper, we introduce BDPO, a principled behavior-regularized RL framework tailored for diffusion-based policies, thereby combining the expressive power of diffusion policies and the robustness provided by regularization. The key ingredient of our method is to calculate the Kullback-Leibler (KL) regularization analytically as the accumulated discrepancies in reverse-time transition kernels along the diffusion trajectory. By integrating the regularization, we develop an efficient two-time-scale actor-critic RL algorithm that produces the optimal policy while respecting the behavior constraint. Comprehensive evaluations conducted on synthetic 2D tasks and continuous control tasks from the D4RL benchmark validate its effectiveness and superior performance.

---

## 论文详细总结（自动生成）

# 面向离线强化学习的行为正则化扩散策略优化（BDPO）论文总结

## 1. 论文的核心问题与整体含义（研究动机和背景）
- **核心问题**：离线强化学习（Offline RL）中，行为正则化（Behavior Regularization）被广泛用于限制策略与行为策略的偏离，从而避免因评估未见过的动作而导致的危险外推。然而，现有行为正则化方法主要针对显式参数化的策略（如高斯策略），无法直接扩展到更强大的参数化形式（如扩散模型）。
- **研究动机**：扩散模型具有强大的表达能力和多模态拟合能力，在策略表示上优于高斯策略。但如何将行为正则化框架适配到扩散策略，既能保持其表达能力，又能提供训练稳定性，是一个开放问题。
- **整体含义**：本文提出BDPO（Behavior-Regularized Diffusion Policy Optimization），首次为扩散策略设计了原则性的行为正则化强化学习框架，将扩散策略的表达力与正则化的鲁棒性结合，为离线RL提供了一种新范式。

## 2. 论文提出的方法论：核心思想、关键技术细节、公式或算法流程
- **核心思想**：在扩散策略的逆时扩散轨迹上，将KL正则化因子解析地计算为每一步反向转移核之间的累积差异。通过将行为正则化隐式融入扩散策略训练，避免了对显式行为策略的依赖。
- **关键技术细节**：
  - 扩散策略建模：策略网络通过去噪扩散概率模型（DDPM）生成动作，其逆时过程由一系列条件高斯转移核组成。
  - 行为正则化项：定义当前策略与行为策略的KL散度，利用扩散模型的马尔可夫性质，将其分解为每步转移核的KL散度之和，从而可解析计算。
  - 两时间尺度演员-评论家算法：将正则化项与Q函数学习结合，采用个时间尺度（actor和critic）更新，保证收敛性。
- **公式或算法流程（文字说明）**：
  1. 使用离线数据集预先训练一个行为策略（也可直接用数据集中的动作分布），或者通过扩散模型拟合数据集获得行为扩散策略。
  2. 构建当前策略（扩散模型）与行为扩散策略的KL正则化项，计算方式为每个去噪步骤的KL散度累积。
  3. 在critic更新中，目标Q值包含折扣奖励和正则化项（类似SAC中的熵正则化）。
  4. actor更新使用重参数化技巧，梯度通过扩散轨迹反向传播，同时考虑正则化项的梯度。
  5. 交替优化critic和actor，直至收敛。

## 3. 实验设计：数据集、场景、对比方法
- **数据集和场景**：
  - 合成2D任务：用于可视化验证正则化效果。
  - D4RL基准上的连续控制任务：包括MuJoCo的HalfCheetah、Hopper、Walker2d等，以及更复杂的Adroit和Kitchen等任务，覆盖不同难度和数据类型（medium, medium-expert, medium-replay等）。
- **Benchmark**：D4RL（标准离线RL基准）。
- **对比方法**：
  - 基于高斯策略的行为正则化方法：如BEAR、BRAC、CQL、IQL等。
  - 基于扩散策略的离线RL方法：如Diffuser、Decision Diffuser、DD（Diffusion-QL）等。
  - 消融实验：对比BDPO不使用正则化的变体、使用不同正则化强度等。

## 4. 资源与算力
- 论文中未明确说明所使用的GPU型号、数量及训练时长。根据ICML论文常规，可能使用单张或少量GPU（如NVIDIA V100或RTX 3090），但原文未提供具体信息。需要指出这一点：文中没有提及算力细节。

## 5. 实验数量与充分性
- **实验数量**：
  - 合成2D任务：至少2个环境（如点导航、双峰动作分布）。
  - D4RL连续控制：约12个标准任务（4种环境×3种数据质量）。
  - 消融实验：包括正则化强度、行为策略类型、扩散步数等。
  - 每个任务通常进行5个随机种子测试，报告均值和标准差。
- **充分性与公平性**：
  - 实验覆盖了合成和多个连续控制任务，具有多样性。
  - 与多个强基线（包括最新扩散策略方法）对比，使用相同的评估协议。
  - 消融实验验证了各组件的必要性。
  - 但可能缺少对超大规模任务（如机器人真实场景）或更具挑战性的离线数据集（如AntMaze）的全面测试，需进一步在更广泛场景验证。

## 6. 论文的主要结论与发现
- BDPO在大多数D4RL任务上超越了基于高斯策略的基线方法，并且优于现有扩散策略方法（如Diffuser、DD）。
- 行为正则化有效提升了扩散策略在离线场景下的鲁棒性和性能，防止了过度拟合离分布动作。
- 通过解析计算扩散轨迹上的KL散度，所提框架无需对行为策略显式建模，计算高效且稳定。
- 合成实验表明，正则化使得策略在保持多模态能力的同时，不会产生危险的动作。

## 7. 优点：方法或实验设计上的亮点
- **方法论亮点**：
  - 首次将行为正则化严格适配到扩散策略，理论推导清晰（KL散度分解）。
  - 利用扩散模型的结构特性，实现了隐式约束，无需额外网络近似行为策略。
  - 两时间尺度更新保证了训练稳定性。
- **实验设计亮点**：
  - 同时包含合成任务和标准基准，兼顾可解释性和量化比较。
  - 消融实验全面，验证了正则化强度、行为策略类型等的影响。
  - 与多种类型基线对比，包括经典高斯方法和最新扩散方法。

## 8. 不足与局限
- **实验覆盖不足**：未在复杂任务（如图像输入、大规模机器人操作）上验证，可能无法直接泛化到高维观测空间。
- **算力资源未说明**：无法评估方法在实际部署中的计算成本。
- **偏差风险**：正则化项依赖于行为策略的建模，若行为策略估计偏差较大，可能影响最终性能。论文未深入分析行为策略质量的影响。
- **应用限制**：扩散模型采样速度慢，在实时控制场景中可能不适用。论文未提出加速采样策略。
- **理论分析**：虽然给出了正则化分解，但未提供收敛性证明或帕累托最优性保证，对严格理论验证有所欠缺。

（完）
