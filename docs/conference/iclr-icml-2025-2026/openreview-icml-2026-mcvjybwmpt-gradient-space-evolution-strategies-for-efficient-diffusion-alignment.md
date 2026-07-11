---
title: Gradient-Space Evolution Strategies for Efficient Diffusion Alignment
title_zh: 用于高效扩散对齐的梯度空间进化策略
authors: "Jiuzhou Lin, junlong wu, Dewen Fan, Boheng Zhang, Fei Zuo, Huan Ouyang, Huaiqing Wang, Jia Sun, Jinge Li, Yuqing DING, Fan Yang, Houde Liu, Tingting Gao, Han Li"
date: 2026-01-23
pdf: "https://openreview.net/pdf/2b4726725a4ff3be1ea3cc81980af73bf6e37ea1.pdf"
tags: ["query:diffusion-rl"]
score: 8.0
evidence: 融合进化策略与强化学习以实现高效的扩散模型对齐
tldr: 现有强化学习对齐扩散模型的方法存在训练不稳定、效率低的问题。本文提出梯度空间进化策略（GS-ES），将对齐过程重新构想为逐步进化，建立了动作空间采样与梯度空间进化之间的对偶性。该方法降低了策略梯度的方差，消除了参考模型的计算开销，显著提升了训练稳定性和效率。
source: ICML-2026-Rejected-Public
selection_source: conference_retrieval
motivation: 强化学习对齐扩散模型时存在高方差和训练不稳定性。
method: 提出GS-ES，利用进化策略与RL的融合，从扩散噪声中直接估计稳定更新方向。
result: 在多个扩散模型对齐任务上实现了更优的训练效率和生成质量。
conclusion: 梯度空间进化策略有效解决了扩散模型RL对齐中的效率与稳定性问题。
---

## Abstract
Reinforcement Learning (RL) has become essential for aligning diffusion models, yet existing methods often struggle with inefficiency and training instability.
In this paper, we introduce **Gradient-Space Evolution Strategies (GS-ES)**, a novel framework that bridges Evolution Strategies and RL by reimagining alignment as a step-wise evolutionary process.
Theoretically, we establish a duality between structured action-space sampling and gradient-space evolution, allowing us to estimate stable update directions directly from diffusion noise.
This formulation effectively reduces the high variance inherent in standard policy gradients while eliminating the computational burden of maintaining reference models.
Empirically, GS-ES demonstrates superior training efficiency and alignment quality across Text-to-Image (Flux) and Text-to-Video (Wan2.1) tasks, significantly outperforming standard GRPO-based baselines.

---

## 论文详细总结（自动生成）

### 1. 论文的核心问题与整体含义（研究动机和背景）

- **核心问题**：现有强化学习（RL）方法在用于扩散模型对齐时，面临训练不稳定和效率低下的双重挑战。传统策略梯度方法方差高，且需要维护参考模型带来额外计算开销。
- **研究动机**：探索一种更稳定、高效的扩散模型对齐框架，结合进化策略（Evolution Strategies）与强化学习的优势，从根本上降低方差，消除参考模型负担。
- **整体含义**：本文提出梯度空间进化策略（GS-ES），将对齐过程重新构想为逐步进化过程，建立了动作空间采样与梯度空间进化之间的对偶性，从而实现对扩散模型的高效、稳定对齐。

### 2. 论文提出的方法论：核心思想、关键技术细节

- **核心思想**：将扩散模型的对齐视为一个逐步进化过程，而不依赖于传统RL的策略梯度更新。通过建立**结构化动作空间采样**与**梯度空间进化**之间的对偶性，直接从扩散噪声中估计稳定的更新方向。
- **关键技术细节**：
  - 利用进化策略（ES）替代标准的策略梯度，有效降低方差。
  - 消除对参考模型（如KL散度约束中的参考模型）的依赖，简化计算图，提高训练速度。
  - 算法流程（文字描述）：在每一步进化中，对噪声扰动后的扩散模型输出进行采样，计算奖励；然后利用进化策略的梯度估计方法（如有限差分或自然进化策略）在梯度空间中进行参数更新，而非在动作空间进行策略梯度更新。
- **公式/算法**（原文未给出具体公式，此处根据方法描述推测）：使用`g = E[ R(θ+σ·ε) · ε ]` 形式的梯度估计（其中`ε`为噪声，`σ`为扰动尺度，`R`为奖励函数），这等价于在参数梯度空间进行进化搜索。

### 3. 实验设计：数据集、基准、对比方法

- **任务场景**：
  - 文生图（Text-to-Image）任务：使用 Flux 模型。
  - 文生视频（Text-to-Video）任务：使用 Wan2.1 模型。
- **基准（Benchmark）**：未提及具体基准数据集名称（可能指标准评估指标，如FID、CLIP score等，但论文摘要未列举）。推断采用常用的扩散模型对齐评估指标（如人类偏好、奖励分数等）。
- **对比方法**：主要对比基于标准 GRPO（Group Relative Policy Optimization）的基线方法。GRPO是一种流行的RLHF方法，用于扩散模型对齐。
- **实验结果**：GS-ES 在训练效率和生成质量上均显著优于 GRPO 基线。

### 4. 资源与算力

- 论文摘要和元数据未明确说明使用的 GPU 型号、数量、训练时长等算力信息。
- **注意**：缺少具体资源细节，可能是由于论文篇幅限制或拒绝稿信息不足。读者如需复现需自行估算或参考未来公开发布的代码。

### 5. 实验数量与充分性

- **实验数量**：从摘要看仅提到两个任务（文生图和文生视频），并未明确列出消融实验、超参数敏感性分析或其他变体。但作为方法论文，通常至少包含与主基线对比、以及可能的消融研究（如去除参考模型后的效果、噪声尺度影响等）。不过现有信息无法断言实验完整程度。
- **充分性**：由于原文只有摘要，我们只能认为实验覆盖了两个代表性任务，对比了GRPO这一强基线。如果论文包含更多实验（如不同奖励函数、模型规模、训练步数对比），则充分性会更高。目前结论基于有限信息，需谨慎评估。

### 6. 论文的主要结论与发现

- GS-ES 框架能有效降低标准策略梯度的高方差，同时消除参考模型计算开销，显著提升训练稳定性和效率。
- 在文生图和文生视频任务上，GS-ES 相较于 GRPO 基线，实现了更优的训练效率和生成质量。
- 理论建立了动作空间采样与梯度空间进化的对偶性，为扩散模型对齐提供了新视角。

### 7. 优点

- **方法创新**：巧妙融合进化策略与强化学习，利用进化策略的低方差特性解决了RL对齐中的经典问题。
- **计算高效**：无需维护参考模型，大幅降低内存和计算需求，适合大规模扩散模型。
- **理论保证**：建立了对偶性，使从噪声中直接估计稳定更新方向有理论支撑。
- **实证效果**：在两个不同模态任务（图像与视频）上均超越强基线GRPO，说明方法具有跨模态泛化能力。

### 8. 不足与局限

- **实验覆盖有限**：仅测试了两个任务（Flux和Wan2.1），未在更多主流扩散模型（如Stable Diffusion、DALL·E等）上验证，也未与更多RL对齐方法（如PPO、DDPO、DPO等）进行全面对比。
- **资源细节缺失**：未报告训练所需GPU型号、数量、总训练时长，难以评估实际计算成本。
- **消融分析不明确**：未公开关键超参数（如进化策略的扰动尺度、种群大小）的影响，以及是否对稳定性有额外依赖。
- **潜在偏差风险**：GRPO作为基线可能并非最优配置，GS-ES的优势可能部分来自不同的超参数调整或实现细节。
- **应用限制**：进化策略通常对奖励函数的平滑性有要求，在稀疏奖励或高维参数空间下可能效率下降，论文未讨论这些边界条件。

（完）
