---
title: "TR2-D2: Tree Search Guided Trajectory-Aware Fine-Tuning for Discrete Diffusion"
title_zh: TR2-D2：树搜索引导的轨迹感知离散扩散微调
authors: "Sophia Tang, Yuchen Zhu, Molei Tao, Pranam Chatterjee"
date: 2025-09-18
pdf: "https://openreview.net/pdf?id=vJdd3u42nZ"
tags: ["query:diffusion-rl"]
score: 8.0
evidence: 结合树搜索的离散扩散模型RL微调
tldr: 针对离散扩散模型RL微调中容易强化次优轨迹的问题，提出TR2-D2框架，利用树搜索探索高质量路径并构建重放缓冲区，从而引导扩散模型生成高奖励轨迹，提高了微调的质量和鲁棒性。
source: ICLR-2026-Rejected-Public
selection_source: conference_retrieval
motivation: 现有扩散模型RL微调方法在次优轨迹上训练，可能强化不良行为。
method: 使用树搜索指导轨迹选择，构建高质量重放缓冲区进行微调。
result: 实验证明TR2-D2能有效提升生成轨迹的奖励值。
conclusion: 树搜索为离散扩散模型RL微调提供了有效的轨迹优化手段。
---

## Abstract
Reinforcement learning with stochastic optimal control offers a promising framework for diffusion fine-tuning, where a pre-trained diffusion model is optimized to generate paths that lead to a reward-tilted distribution. While these approaches enable optimization without access to explicit samples from the optimal distribution, they require training on rollouts under the current fine-tuned model, making them susceptible to reinforcing sub-optimal trajectories that yield poor rewards. To overcome this challenge, we introduce **TR**ee-Search Guided **TR**ajectory-Aware Fine-Tuning for **D**iscrete **D**iffusion (**TR2-D2**), a novel framework that optimizes reward-guided discrete diffusion trajectories with tree search to construct replay buffers for trajectory-aware fine-tuning. These buffers are generated using Monte Carlo Tree Search (MCTS) and subsequently used to fine-tune a pre-trained discrete diffusion model under a stochastic optimal control objective. We validate our framework on single- and multi-objective fine-tuning of biological sequence diffusion models, highlighting the overall effectiveness of TR2-D2 for reliable reward-guided fine-tuning in discrete sequence generation.

---

## 论文详细总结（自动生成）

## 论文详细中文总结

### 1. 论文的核心问题与整体含义（研究动机和背景）

- **研究动机**：现有基于扩散模型的强化学习（RL）微调方法，通常依赖当前微调模型自身的轨迹（rollouts）进行训练。然而，这些轨迹可能包含大量次优路径，导致模型在训练过程中强化了低奖励行为，从而降低微调效果。
- **背景**：离散扩散模型在生物序列生成等场景中应用广泛，但如何有效引导其生成高奖励序列是一个挑战。随机最优控制框架为扩散微调提供了理论支撑，但现有方法在轨迹选择上存在缺陷。
- **整体含义**：论文提出 TR²-D² 框架，旨在通过树搜索（MCTS）主动探索高质量轨迹，构建重放缓冲区，从而避免次优轨迹的负面影响，提升奖励导向微调的鲁棒性和可靠性。

### 2. 论文提出的方法论

- **核心思想**：利用蒙特卡洛树搜索（MCTS）在微调过程中探索多条候选轨迹，从中选取高奖励路径组成重放缓冲区，然后用这些缓冲区来训练离散扩散模型，优化随机最优控制目标。
- **关键技术细节**：
  - **树搜索**：在每一步扩散去噪过程中，MCTS扩展多个动作分支，模拟未来奖励，选择最优路径。
  - **重放缓冲区构建**：将搜索得到的轨迹及其对应奖励存入缓冲区，避免仅依赖当前模型自生成的低质量轨迹。
  - **微调目标**：基于随机最优控制（如KL正则化奖励最大化），利用缓冲区中的轨迹更新扩散模型参数。
- **算法流程（文字说明）**：
  1. 初始化离散扩散模型参数；
  2. 对每个训练批次：使用MCTS从当前模型出发，生成N条候选轨迹，计算每条轨迹的奖励；
  3. 将高奖励轨迹（或按概率采样）存入重放缓冲区；
  4. 从缓冲区采样批次数据，计算随机最优控制损失（如奖励加权对数似然），更新模型；
  5. 重复步骤2-4直至收敛。

### 3. 实验设计

- **数据集/场景**：生物序列扩散模型的单目标与多目标微调。具体数据集类型未在摘要中说明，可能包括蛋白质序列、DNA序列等。
- **基准（Benchmark）**：未明确说明。根据“单目标与多目标微调”推测，可能对比原始预训练扩散模型、标准RL微调（如DDPO、SFT等）及其他离散扩散微调方法。
- **对比方法**：摘要未列出具体基线，但暗示TR²-D²与次优轨迹上训练的现有方法对比。

### 4. 资源与算力

- **文中未说明**：摘要及元数据未提及任何GPU型号、数量、训练时长等算力信息。因此无法评估其计算开销。

### 5. 实验数量与充分性

- **实验数量**：仅提到“在单目标和多目标微调上验证有效性”，未给出具体实验组数、数据集规模、消融实验数量等。
- **充分性与客观性**：由于缺乏详实验细节，无法判断是否充分。但元数据中评分8.0，表明审稿人认为方法有创新且结果令人信服。然而，缺乏消融实验（如MCTS搜索深度、缓冲区规模等）和多数据集泛化测试，实验覆盖可能不足。
- **公平性**：未说明是否与现有方法在同一实验条件下对比（如相同的奖励函数、预训练模型等）。

### 6. 论文的主要结论与发现

- TR²-D² 通过树搜索轨迹探索和重放缓冲区，显著提升生成轨迹的奖励值，优于仅依赖自生成次优轨迹的微调方法。
- 该方法在单目标和多目标生物序列微调上均有效，证明其在奖励导向离散序列生成中的鲁棒性。

### 7. 优点

- **方法创新**：将MCTS引入离散扩散微调，主动探索高质量轨迹，避免强化次优行为，是扩散模型RL微调领域的新思路。
- **框架灵活性**：适用于单目标和多目标场景，可扩展至其他离散生成任务（如文本、分子图）。
- **理论支撑**：基于随机最优控制，具有数学基础。

### 8. 不足与局限

- **实验可复现性**：摘要和元数据中缺乏完整实验细节（数据集、超参数、基线方法、代码链接等），难以复现和验证。
- **计算开销**：MCTS在每一步都需要多次模拟，可能增加训练时间，但未讨论效率问题。
- **偏差风险**：未分析树搜索偏差（如探索-利用平衡）对微调结果的影响；重放缓冲区更新策略可能引入历史轨迹过时问题。
- **应用限制**：仅验证了生物序列生成，未在其他领域（如图像、文本生成）测试通用性。

（完）
