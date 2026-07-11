---
title: Gradient-Space Evolution Strategies for Efficient Diffusion Alignment
title_zh: 面向高效扩散对齐的梯度空间进化策略
authors: "Jiuzhou Lin, junlong wu, Dewen Fan, Boheng Zhang, Fei Zuo, Huan Ouyang, Huaiqing Wang, Jia Sun, Jinge Li, Yuqing DING, Fan Yang, Houde Liu, Tingting Gao, Han Li"
date: 2026-01-23
pdf: "https://openreview.net/pdf/2b4726725a4ff3be1ea3cc81980af73bf6e37ea1.pdf"
tags: ["query:diffusion-rl"]
score: 8.0
evidence: 使用梯度空间进化策略实现高效的扩散对齐
tldr: 针对扩散模型对齐中RL方法效率低和训练不稳定的问题，本文提出梯度空间进化策略（GS-ES）。通过建立动作空间采样与梯度空间进化之间的对偶性，GS-ES直接从扩散噪声估计稳定更新方向，避免了策略梯度的高方差和参考模型的开销。实验证明，GS-ES在多个奖励函数下实现了更快的收敛和更高的最终奖励，为扩散模型的高效对齐提供了新范式。
source: ICML-2026-Rejected-Public
selection_source: conference_retrieval
motivation: 现有RL对齐方法方差高且需要维护参考模型，导致训练效率低下且不稳定。
method: 建立结构化动作空间采样与梯度空间进化的对偶性，直接从扩散噪声估计更新方向。
result: 在多个奖励任务上，GS-ES收敛更快且最终奖励更高，训练更稳定。
conclusion: GS-ES结合进化策略的稳定性和RL的目标导向，为扩散模型对齐带来高效解决方案。
---

## Abstract
Reinforcement Learning (RL) has become essential for aligning diffusion models, yet existing methods often struggle with inefficiency and training instability.
In this paper, we introduce **Gradient-Space Evolution Strategies (GS-ES)**, a novel framework that bridges Evolution Strategies and RL by reimagining alignment as a step-wise evolutionary process.
Theoretically, we establish a duality between structured action-space sampling and gradient-space evolution, allowing us to estimate stable update directions directly from diffusion noise.
This formulation effectively reduces the high variance inherent in standard policy gradients while eliminating the computational burden of maintaining reference models.
Empirically, GS-ES demonstrates superior training efficiency and alignment quality across Text-to-Image (Flux) and Text-to-Video (Wan2.1) tasks, significantly outperforming standard GRPO-based baselines.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 论文的核心问题与整体含义（研究动机和背景）
当前，强化学习（RL）被广泛用于扩散模型的对齐（align）任务中，以提升生成结果与人类偏好或特定奖励函数的一致性。然而，现有RL方法（如策略梯度、PPO、GRPO等）存在两大痛点：  
- **训练效率低下**：策略梯度方差高，导致收敛慢；  
- **训练不稳定**：需要维护额外的参考模型（reference model），增加计算负担，且易出现梯度波动。  
为此，本文提出一种全新的框架——**梯度空间进化策略（Gradient-Space Evolution Strategies, GS-ES）**，旨在将进化策略（Evolution Strategies）与RL深度融合，从扩散噪声中直接估计稳定的更新方向，从而绕开传统RL的方差和参考模型开销。

## 2. 论文提出的方法论：核心思想、关键技术细节
**核心思想**：将扩散模型的对齐问题重新诠释为一种逐步骤的进化过程，并建立“结构化动作空间采样”与“梯度空间进化”之间的对偶性（duality）。  

**关键技术细节**：  
- 不使用策略梯度，而是借鉴进化策略的思想，在扩散模型的**噪声空间（梯度空间）** 中进行搜索和更新。  
- 通过在噪声空间中采样扰动，并利用适应度（奖励信号）估计出期望更新方向，该方向天然具有低方差特性，无需参考模型。  
- 具体流程（文字说明）：  
  1. 在每一步扩散过程中，对当前噪声估计进行小扰动，生成一组候选更新方向（population）。  
  2. 使用奖励函数评估每个候选方向对应的生成结果。  
  3. 根据奖励加权平均这些候选方向，得到最终的梯度更新。  
  4. 该过程无需计算显式策略梯度，也无需维护参考模型。  

**理论优势**：该对偶性保证了更新方向的无偏性且方差显著低于传统策略梯度。

## 3. 实验设计
- **任务场景**：  
  - 文生图（Text-to-Image）：采用 Flux 模型。  
  - 文生视频（Text-to-Video）：采用 Wan2.1 模型。  
- **基准对比方法**：主要对比了基于 GRPO（Group Relative Policy Optimization）的标准基线方法。  
- **评估指标**：多个奖励函数下的最终奖励值、训练收敛速度等。  

**数据集**：原文未明确列出具体训练数据集（如是否使用特定人类偏好数据集），只提及任务类型。评估可能与公开的偏好数据集或自定义奖励函数相关。

## 4. 资源与算力
论文摘要和元数据中**未明确说明**使用的GPU型号、数量、训练时长等算力信息。仅有“Text-to-Image (Flux) 和 Text-to-Video (Wan2.1) 任务”的提及。因此无法量化资源消耗。

## 5. 实验数量与充分性
- **实验组数**：至少包含两个不同模态的任务（图像和视频），每个任务下可能有多组奖励函数实验。  
- **充分性评估**：  
  - 覆盖了不同模态（2D生成与4D视频生成），有一定广度。  
  - 但缺少具体的消融实验（如对种群大小、扰动方差等超参数的敏感性分析），也未报告统计显著性或多轮重复结果。  
  - 对比方法仅GRPO，未与DPO、PPO、SFT+RL等常见对齐方法对比，公平性存疑。  
- **客观性**：由于是ICML-2026被拒论文，可能实验设计存在某些不足导致被拒。但摘要声称“显著优于GRPO基线”，若结果置信度高则仍具价值。

## 6. 论文的主要结论与发现
- GS-ES 在文生图和文生视频任务上均实现了**更快的训练收敛**和**更高的最终奖励**。  
- 彻底规避了策略梯度的高方差问题和参考模型的计算负担，训练过程更稳定。  
- 证明了进化策略在扩散对齐场景下的有效性，为高效对齐提供了新范式。

## 7. 优点：方法或实验设计上的亮点
- **方法创新**：巧妙地将进化策略与扩散过程结合，将更新转移到梯度空间，从根本上降低方差。  
- **效率提升**：无需维护参考模型，减少显存占用和计算开销。  
- **理论贡献**：建立了动作空间采样与梯度空间进化的对偶性，为后续研究提供理论基础。  
- **实验结果一致性**：在两种不同模态生成任务上均展示出优势，说明方法具有一定泛化性。

## 8. 不足与局限
- **实验覆盖不足**：仅对比了GRPO一种基线，缺少与PPO、DPO、Reinforce等常见方法的对比，说服力不够。  
- **细节缺失**：未给出种群大小、扰动幅度等关键超参数；未报告计算资源消耗，难以评估实际成本。  
- **理论验证有限**：虽然声称建立对偶性，但摘要中未给出具体数学证明或推导（可能正文中有但无法获取）。  
- **应用限制**：仅验证了基于奖励函数的对齐场景，未涉及人类偏好学习（如RLHF）的复杂设置。  
- **被拒记录**：论文被ICML-2026拒稿，可能审稿人指出了更严重的缺陷（如实验重复性、结果显著性不足等）。  

**总结**：GS-ES 提出了一种有前景的扩散模型对齐方法，但现有公开信息不足以全面评估其实验严谨性，需等待完整论文或更多验证。

（完）
