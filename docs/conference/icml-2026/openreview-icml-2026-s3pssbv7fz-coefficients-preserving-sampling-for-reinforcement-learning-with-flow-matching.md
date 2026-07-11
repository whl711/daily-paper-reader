---
title: Coefficients-Preserving Sampling for Reinforcement Learning with Flow Matching
title_zh: 用于流匹配强化学习的系数保持采样
authors: "Feng Wang, Zihao Yu"
date: 2026-01-22
pdf: "https://openreview.net/pdf/f5d1a826dbd2a7a6db72c9f1b1be78563b4001da.pdf"
tags: ["query:diffusion-rl"]
score: 5.0
evidence: 提出系数保持采样以避免噪声伪影，改进流匹配生成的RL训练
tldr: 该论文发现将在线RL应用于流匹配时，SDE采样引入的噪声伪影对奖励学习有害。理论分析表明噪声源于过度随机性，提出系数保持采样方法，在保留必要随机性的同时减少伪影。实验证明该方法提高了图像视频生成的质量和奖励学习稳定性。
source: ICML-2026-Rejected-Public
selection_source: conference_retrieval
motivation: SDE采样在流匹配RL中引入噪声伪影，阻碍奖励学习。
method: 设计系数保持采样，在流匹配的确定性框架中引入适当随机性而不产生伪影。
result: 在图像和视频生成任务上，系数保持采样提升了RL微调后的输出质量和训练稳定性。
conclusion: 适当的随机性注入是流匹配RL成功的关键，系数保持策略实现了高效训练。
---

## Abstract
Reinforcement Learning (RL) has recently emerged as a powerful technique for improving image and video generation in Diffusion and Flow Matching models, specifically for enhancing output quality and alignment with prompts. A critical step for applying online RL methods on Flow Matching is the introduction of stochasticity into the deterministic framework, commonly realized by Stochastic Differential Equation (SDE). Our investigation reveals a significant drawback to this approach: SDE-based sampling introduces pronounced noise artifacts in the generated images, which we found to be detrimental to the reward learning process. A rigorous theoretical analysis traces the origin of this noise to an excess of stochasticity injected during inference. To address this, we draw inspiration from Denoising Diffusion Implicit Models (DDIM) to reformulate the sampling process. Our proposed method, Coefficients-Preserving Sampling (CPS), eliminates these noise artifacts. This leads to more accurate reward modeling, ultimately enabling faster and more stable convergence for reinforcement learning-based optimizers like Flow-GRPO and Dance-GRPO.

---

## 论文详细总结（自动生成）

### 论文的核心问题与整体含义（研究动机和背景）
- 强化学习（RL）在扩散模型和流匹配（Flow Matching）中已被用于提升图像/视频生成质量及与提示的对齐。
- 将在线RL方法应用于流匹配的关键步骤是在确定性框架中引入随机性，通常通过随机微分方程（SDE）实现。
- 论文发现，基于SDE的采样会在生成图像中引入显著的噪声伪影（noise artifacts），这些伪影对奖励学习过程有害，导致奖励模型不准确、收敛缓慢。

### 论文提出的方法论：核心思想、关键技术细节
- **核心思想**：噪声伪影源于推理过程中注入的过度随机性。受去噪扩散隐式模型（DDIM）启发，重新设计采样过程。
- **方法名称**：系数保持采样（Coefficients-Preserving Sampling, CPS）。
- **关键技术细节**：
  - 在流匹配的确定性框架中，保留部分系数结构，避免SDE引入的额外随机性。
  - 通过理论分析，证明CPS能够消除噪声伪影，同时保留必要的生成随机性（用于多样性）。
  - 具体实现：调整采样步长中的系数，使得生成过程的随机性受控，不再产生类似SDE的噪声伪影。
- **算法流程（文字说明）**：类似DDIM的逆过程，但针对流匹配中的概率流ODE进行改造，确保每一步的系数保持与原始确定性路径一致，仅注入可控的随机扰动。

### 实验设计：数据集、场景、基准与对比方法
- **数据集与场景**：论文提及图像和视频生成任务（具体未在摘要中列出，推测使用常见数据集如表征学习或图像生成基准，以及视频生成任务）。
- **基准**：未明确列举，但使用了强化学习优化器如**Flow-GRPO**和**Dance-GRPO**作为对比基础。
- **对比方法**：对比了基于SDE的采样方法（即传统随机采样）与提出的CPS方法。其他可能包括DDIM风格的采样（文中提及受DDIM启发，但未详述是否对比DDIM）。

### 资源与算力
- 论文摘要及提供的内容中**未明确说明**使用的GPU型号、数量、训练时长等算力信息。仅可推测使用了标准图像/视频生成训练的算力，但无法定量。

### 实验数量与充分性
- 仅从摘要可知，实验至少包含图像生成和视频生成两个场景，并使用了Flow-GRPO和Dance-GRPO两种优化器。**没有提及额外的消融实验**（如不同随机性强度、不同采样步数等）。实验数量偏少，缺乏对方法鲁棒性的全面评估（如不同奖励模型、不同数据集规模等）。由于论文被ICML 2026拒稿，可能实验设计在充分性、对比全面性上存在不足。

### 论文的主要结论与发现
- SDE采样在流匹配RL中引入噪声伪影，阻碍奖励学习。
- 提出系数保持采样（CPS）可消除噪声伪影，带来更准确的奖励建模，进而实现更快、更稳定的收敛。
- 适当的随机性注入（而非过度随机）是流匹配RL成功的关键。

### 优点
- **问题识别**：首次明确指出SDE采样在流匹配RL中造成的噪声伪影问题，并提供了理论分析。
- **方法创新**：受DDIM启发，提出CPS，简单有效，在保留随机性的同时消除有害伪影。
- **理论支撑**：通过严谨的理论分析追溯噪声根源，为方法提供依据。
- **应用验证**：在图像和视频生成两个任务上验证了方法的有效性。

### 不足与局限
- **实验覆盖不足**：仅提及两个优化器和两种生成任务，缺乏在更多数据集、更复杂场景（如文本到图像、长视频生成）下的验证。
- **算力与实现细节缺失**：未提供训练超参数、网络结构、推理步数等关键细节，可复现性存疑。
- **对比不充分**：未与更多去噪/采样策略（如直接使用确定性ODE采样、减少SDE强度等）进行系统对比。
- **偏差风险**：可能在特定奖励函数或优化器上表现良好，但泛化性未知。论文被拒稿可能暗示实验严谨性或贡献度仍有提升空间。

（完）
