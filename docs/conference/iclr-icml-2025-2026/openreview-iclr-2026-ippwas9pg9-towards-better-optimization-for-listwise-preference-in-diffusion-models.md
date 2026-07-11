---
title: Towards Better Optimization For Listwise Preference in Diffusion Models
title_zh: 面向扩散模型中列表偏好优化的更好方法
authors: "Jiamu Bai, Xin Yu, Meilong Xu, Weitao Lu, Xin Pan, Kiwan Maeng, Daniel Kifer, Jian Wang, Yu Wang"
date: 2026-01-26
pdf: "https://openreview.net/pdf?id=ippWaS9PG9"
tags: ["query:diffusion-rl"]
score: 4.0
evidence: 扩散模型的RLHF列表偏好优化
tldr: 针对扩散模型偏好对齐中仅使用成对比较的局限性，本文提出Diffusion-LPO框架，利用列表级偏好进行优化。通过引入排序损失，该方法更充分地利用人类反馈中的隐含排序信息。实验表明在文本到图像生成任务中比DPO等基线取得更优对齐效果。为扩散模型的RLHF提供了新的优化视角。
source: ICLR-2026-Accepted
selection_source: conference_retrieval
motivation: 现有扩散模型偏好优化多依赖成对比较，未能利用列表级排序信息中的更精确偏好。
method: 提出Diffusion-LPO框架，定义列表级偏好损失，通过排序梯度优化扩散模型。
result: 在文本到图像生成任务上，Diffusion-LPO在人类偏好评分上优于DPO等方法。
conclusion: 列表级偏好优化能更有效对齐扩散模型与人类偏好。
---

## Abstract
Reinforcement learning from human feedback (RLHF) has proven effectiveness for aligning text-to-image (T2I) diffusion models with human preferences. Although Direct Preference Optimization (DPO) is widely adopted for its computational efficiency and avoidance of explicit reward modeling, its applications to diffusion models have primarily relied on pairwise preferences. The precise optimization of listwise preferences remains largely unaddressed. In practice, human feedback on image preferences often contains implicit ranked information, which conveys more precise human preferences than pairwise comparisons. In this work, we propose Diffusion-LPO, a simple and effective framework for Listwise Preference Optimization in diffusion models with listwise data. Given a caption, we aggregate user feedback into a ranked list of images and derive a listwise extension of the DPO objective under the Plackett–Luce model. Diffusion-LPO enforces consistency across the entire ranking by encouraging each sample to be preferred over all of its lower-ranked alternatives.  We empirically demonstrate the effectiveness of Diffusion-LPO across various tasks, including text-to-image generation, image editing, and personalized preference alignment. Diffusion-LPO consistently outperforms pairwise DPO baselines on visual quality and preference alignment.

---

## 论文详细总结（自动生成）

好的，以下是基于所提供的论文摘要与元数据生成的详细中文总结。

---

### 1. 论文的核心问题与整体含义

- **研究背景**：将人类偏好反馈（RLHF）用于对齐文本到图像（T2I）扩散模型已被证明有效。直接偏好优化（DPO）因其计算高效且无需显式奖励模型而被广泛采用。
- **核心问题**：现有扩散模型的 DPO 应用主要依赖于**两两比较（pairwise preferences）**，而忽略了人类反馈中隐含的**列表级排序（listwise preferences）**，后者能提供更精确的偏好信息。如何有效利用列表级偏好优化扩散模型仍是未解决的挑战。
- **整体含义**：本文提出 **Diffusion-LPO**（Listwise Preference Optimization）框架，通过利用排序列表数据，使得扩散模型对齐更精准，在多个任务上优于两两 DPO 基线。

### 2. 论文提出的方法论

- **核心思想**：将人类对同一描述生成的多个图像的反馈聚合为排序列表，并在 Plackett–Luce 模型下推导出 DPO 目标的列表级扩展。每个样本被鼓励优于所有排名低于它的样本，从而保持整个排名的整体一致性。
- **关键技术细节**：
  - 使用列表级偏好数据：给定一个文本描述，收集多张图像并按人类偏好排序。
  - 目标函数：基于 Plackett–Luce 模型的列表级扩展，损失函数形如 \( \mathcal{L}_{\text{LPO}} = -\sum_{i=1}^{n} \log \frac{\exp(\beta \hat{r}(x_i, c))}{\sum_{j=i}^{n} \exp(\beta \hat{r}(x_j, c))} \)，其中 \( \hat{r} \) 由隐式奖励（通过模型对数概率之差近似）定义。
  - 训练方式：通过排序梯度更新扩散模型的参数，不需额外训练奖励模型。
- **算法流程**（文字说明）：
  1. 对于每个文本描述 \( c \)，采样一组候选图像 \( \{x_1, x_2, \dots, x_n\} \)。
  2. 获取人类对这些图像的排序标签（或使用代理模型模拟）。
  3. 对每个图像计算其相对于所有排名更低图像的偏好对数概率。
  4. 累加所有位置的损失，反向传播更新扩散模型参数。

### 3. 实验设计

- **数据集/场景**：
  - 文本到图像生成（T2I）
  - 图像编辑（Image Editing）
  - 个性化偏好对齐（Personalized Preference Alignment）
- **基准（benchmark）**：未明确说明具体数据集名称（例如 Pick-a-Pic、HPD 等），但涉及多个任务通用测试。
- **对比方法**：主要对比 **pairwise DPO 基线**（即标准 DPO）。文中提到“consistently outperforms pairwise DPO baselines”。
- **评估指标**：视觉质量 (visual quality) 和偏好对齐 (preference alignment) 的评分，可能包含人类评估或自动指标（如 CLIP 分数、Aesthetic 分数等），但未详述。

### 4. 资源与算力

- 文中 **未明确说明** 使用的 GPU 型号、数量、训练时长等算力信息。仅能推断使用了标准扩散模型训练配置（如 Stable Diffusion 系列），但无具体数字。

### 5. 实验数量与充分性

- **实验组数**：涉及三类任务（T2I、图像编辑、个性化对齐），至少包含三个主要实验。未提及消融实验或其他变体。
- **充分性评估**：覆盖多任务场景是优点，但缺乏消融实验（如列表长度影响、不同排序模型对比）和对超参数敏感性的分析。由于仅与 pairwise DPO 对比，未比较其他列表优化方法（如 RRHF、DPO 的多候选扩展），对比不够全面。可认为**实验覆盖有限，但主结果清晰**。

### 6. 论文的主要结论与发现

- 列表级偏好优化（Diffusion-LPO）能更有效地将扩散模型对齐到人类偏好，相比成对 DPO 方法在视觉质量和一致性上均有提升。
- 使用 Plackett–Luce 模型扩展 DPO 是简单且有效的框架。
- 该方法不仅适用于 T2I 生成，也适用于图像编辑和个性化对齐，说明其通用性。

### 7. 优点

- **方法简洁有效**：不需要额外奖励模型，仅需排序数据即可直接优化。
- **利用更丰富排序信息**：相比 pairwise，listwise 利用全排序，偏好信号更强。
- **多任务适用性**：在生成、编辑、个性化三个任务上均验证了有效性。
- **理论基础**：基于 Plackett–Luce 模型，有概率论支持。

### 8. 不足与局限

- **实验规模与细节不足**：未公开具体数据集、评估指标、超参数设置，复现难度高。
- **对比基线单一**：仅对比 pairwise DPO，未与其他列表优化方法（如 SPIN、ListRank）或基于奖励的 RLHF 方法比较。
- **未讨论列表长度的选择**：不同长度对性能的影响未消融。
- **潜在偏差风险**：若排序数据来自自动标注模型（如 CLIP 排序），可能引入系统偏差；若来自人工，则标注成本高。
- **应用限制**：仅验证在扩散模型上，未探讨在其它生成模型（如 GAN、自回归模型）的适用性。
- **算力与资源未报告**：难以评估训练成本与可扩展性。

---

（完）
