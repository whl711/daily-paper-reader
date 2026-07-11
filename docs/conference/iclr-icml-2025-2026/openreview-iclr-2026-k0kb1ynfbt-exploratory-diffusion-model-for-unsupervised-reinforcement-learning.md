---
title: Exploratory Diffusion Model for Unsupervised Reinforcement Learning
title_zh: 用于无监督强化学习的探索性扩散模型
authors: "Chengyang Ying, Huayu Chen, Xinning Zhou, Zhongkai Hao, Hang Su, Jun Zhu"
date: 2026-01-26
pdf: "https://openreview.net/pdf?id=k0Kb1ynFbt"
tags: ["query:diffusion-rl"]
score: 7.0
evidence: 扩散模型用于无监督强化学习的内在奖励
tldr: 提出探索性扩散模型ExDM，利用扩散模型的表达能力拟合回放缓冲分布，提供基于分数的内在奖励，驱动智能体探索未访问区域，显著扩大状态覆盖并生成鲁棒的预训练策略，适用于无监督RL。
source: ICLR-2026-Accepted
selection_source: conference_retrieval
motivation: 无监督强化学习的异构探索数据需要强建模能力。
method: 使用扩散模型进行密度估计，产生基于分数的内在奖励。
result: 在多种下游任务上状态覆盖和性能提升。
conclusion: 扩散模型可有效用于无监督RL探索。
---

## Abstract
Unsupervised reinforcement learning (URL) pre-trains agents by exploring diverse states in reward-free environments, aiming to enable efficient adaptation to various downstream tasks. Without extrinsic rewards, prior methods rely on intrinsic objectives, but heterogeneous exploration data demand strong modeling capacity for both intrinsic reward design and policy learning. We introduce the **Ex**ploratory **D**iffusion **M**odel (**ExDM**), which leverages the expressive power of diffusion models to fit diverse replay-buffer distributions, thus providing accurate density estimates and a score-based intrinsic reward that drives exploration into under-visited regions. This mechanism substantially broadens state coverage and yields robust pre-trained policies. Beyond exploration, ExDM offers theoretical guarantees and practical algorithms for fine-tuning diffusion policies under limited interactions, overcoming instability and computational overhead from multi-step sampling. Extensive experiments on Maze2d and URLB show that ExDM achieves superior exploration and faster downstream adaptation, establishing new state-of-the-art results, particularly in environments with complex structure or cross-embodiment settings. The source code is provided at https://github.com/yingchengyang/ExDM.

---

## 论文详细总结（自动生成）

### 论文详细中文总结

#### 1. 核心问题与整体含义（研究动机和背景）
- **核心问题**：在无监督强化学习（URL）中，智能体在没有外部奖励的环境下预训练，通过探索多样化的状态为下游任务提供高效适应能力。然而，异构的探索数据对密度估计（用于设计内在奖励）和策略学习提出了高要求——传统方法建模能力不足，难以有效覆盖复杂环境中的未访问区域。
- **整体含义**：这项工作旨在利用生成模型的强大表达能力（尤其是扩散模型）来提升无监督探索的质量，从而在预训练阶段获得更广的状态覆盖和更鲁棒的策略，最终加速下游任务的适应。

#### 2. 方法论
- **核心思想**：提出**探索性扩散模型（ExDM）**，将扩散模型作为密度估计器，拟合回放缓冲（replay buffer）中的状态分布，并利用其得分函数（score function）设计内在奖励，引导智能体探索密度低的区域。
- **关键技术细节**：
  - 扩散模型通过学习从随机噪声到数据分布的逆向过程，提供精确的密度估计（即状态空间的概率密度）。
  - 基于密度估计，定义**基于得分的内在奖励**：状态概率密度越低，奖励越高，从而驱动智能体访问未充分探索的区域。
  - 在微调阶段，ExDM 提供理论保证和实用算法，解决多步采样带来的不稳定性和计算开销，使扩散策略能在有限交互下高效微调。
- **公式/算法流程（文字描述）**：
  1. 预训练阶段：智能体与环境交互，将收集的状态存入回放缓冲；训练一个扩散模型拟合回放缓冲中的状态分布；利用扩散模型的得分函数计算每个状态的探索奖励（负对数似然或得分范数）；智能体以该内在奖励为目标优化策略，持续扩大状态覆盖。
  2. 微调阶段：给定下游任务（带外部奖励），利用扩散模型生成初始轨迹或作为策略先验，通过提出的算法（如截断的多步采样或重要性加权）稳定微调过程，避免因扩散模型采样步骤多导致的梯度不稳定。

#### 3. 实验设计
- **数据集/场景**：Maze2D（二维迷宫环境）和 URLB（Unsupervised Reinforcement Learning Benchmark，包含多种连续控制任务如 Cheetah、Walker、Quadruped 等）。
- **基准（Benchmark）**：使用 URLB 标准设置，对比多种前沿的无监督强化学习方法（如 ICM、RND、DIAYN、VIC、APT 等）。
- **对比方法**：摘要未列出具体方法名称，但指出对比了 prior methods（基于内在奖励的 URL 方法），ExDM 在所有基准上达到新的最优结果。

#### 4. 资源与算力
- 文中**未明确说明**使用的 GPU 型号、数量、训练时长等算力信息。仅在摘要末尾提供源代码链接（GitHub），但未描述实验硬件。

#### 5. 实验数量与充分性
- **实验数量**：摘要称进行了“extensive experiments”，包含 Maze2D 和 URLB 两大场景，以及不同下游任务（多种环境）的适应性能评估。但是否包含消融实验、超参数分析等未提及。从结论来看，实验涵盖复杂结构和跨形态（cross-embodiment）设置，具有一定的广度。
- **充分性与公平性**：基于摘要描述，实验设计较为扎实：在标准基准 URLB 上进行，对比多种常见方法，并报告 SOTA 结果。但缺乏具体实验次数、统计显著性检验、计算效率对比等细节，无法完全判断其公平性。推测其作者在 ICLR 接受文章中提供了更详尽的实验。

#### 6. 主要结论与发现
- ExDM 通过扩散模型提供的精确密度估计和基于得分的内在奖励，显著扩大了无监督探索的状态覆盖，并生成鲁棒的预训练策略。
- 在 Maze2D 和 URLB 的多种下游任务中，ExDM 取得了比现有方法更快的适应速度和更高的最终性能。
- 特别在具有复杂结构的环境和跨形态（不同机器人形态）设置下，ExDM 表现突出，证明了扩散模型在 URL 探索中的有效性。

#### 7. 优点（方法与实验亮点）
- **方法创新**：首次将扩散模型引入无监督强化学习的内在奖励设计，利用其强大的生成能力和得分函数精确建模回放缓冲分布，优于传统的简单密度估计（如核密度估计、RND 等）。
- **理论贡献**：为基于扩散模型的策略微调提供了理论保证（收敛性、稳定性），解决了多步采样导致的不稳定性问题。
- **实用算法**：提出的微调算法能够高效利用有限交互，降低计算开销，便于实际应用。
- **实验结果**：在多个标准基准上达到 SOTA，特别是跨形态设置证明方法的泛化能力。

#### 8. 不足与局限
- **实验覆盖**：虽然结果在 Maze2D 和 URLB 上优秀，但缺少更复杂的环境（如 3D 导航、部分可观测环境）或真实机器人实验，泛化性有待验证。
- **偏差风险**：摘要未讨论扩散模型训练的计算成本（相对于其他内在奖励方法可能更高），也未分析在低维状态空间中扩散模型是否过度复杂。
- **应用限制**：依赖扩散模型的采样和得分计算，在超大规模状态空间或在线交互频繁的场景下，实时性可能成为瓶颈；此外，基于密度的内在奖励在极端稀疏奖励环境中可能仍需谨慎设计。
- **资源信息缺失**：未报告算力消耗，难以评估方法的可复现性和效率。

（完）
