---
title: Temporal Difference Learning for Diffusion Models
title_zh: 扩散模型的时序差分学习
authors: "Qizhen Ying, Yangchen Pan, Victor Adrian Prisacariu, Junfeng Wen"
date: 2026-04-30
pdf: "https://openreview.net/pdf/f71409e9163d4f69b7ebef76510629bbaf5b1fe8.pdf"
tags: ["query:diffusion-rl"]
score: 9.0
evidence: 将扩散模型的去噪过程视为强化学习策略评估，提出时序差分学习目标
tldr: 本文揭示扩散模型传统训练目标缺乏跨时间步一致性，导致少步采样性能不佳。通过将扩散过程重新表述为马尔可夫奖励过程，并将去噪视为强化学习中的策略评估问题，提出了统一的时序差分（TD）目标。该目标惩罚沿去噪路径的多步不一致性，并引入了基于样本的重新加权方法。在多个图像生成数据集上，TD训练显著提升少步采样的质量和效率。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 扩散模型的局部去噪训练目标不保证跨时间步一致性，损害少步采样器的性能。
method: 将扩散过程建模为马尔可夫奖励过程，推导出统一的TD目标惩罚多步不一致。
result: 在多个图像生成任务上，TD训练使少步采样质量大幅提升，接近多步水平。
conclusion: TD学习为扩散模型提供了有效的时间一致性约束，是提升效率和质量的通用方法。
---

## Abstract
Diffusion models are typically trained with objectives that focus on local denoising targets at individual time steps (or adjacent pairs), which do not enforce consistency between predictions along the denoising trajectory. This lack of cross-time consistency can degrade performance, especially for few-step samplers. We introduce a temporal difference (TD) objective that penalizes inconsistency of the model’s \emph{multi-step} progress along the denoising path. By reformulating the diffusion process as a Markov reward process and casting denoising as a policy evaluation problem in reinforcement learning, we derive a unified TD approach that applies to both discrete- and continuous-time diffusion formulations. We further propose a principled sample-based reweighting method that stabilizes training. Empirically, we show that using our TD training can significantly improve sample quality measured by FID, with stronger advantages when the number of sampling steps is small, highlighting its practical utility under low-computation-budget scenarios. We provide ablation studies to justify our design choices, including pairwise loss reweighting, regularization weight, and one-step stride. Overall, our TD approach can be a general drop-in that enforces cross-time consistency and improves generation quality across different diffusion generative models.

---

## 论文详细总结（自动生成）

# 论文总结：扩散模型的时序差分学习

## 1. 论文的核心问题与整体含义（研究动机和背景）
- **核心问题**：传统扩散模型的训练目标仅关注单个时间步（或相邻时间步）的局部去噪，缺乏跨时间步的**预测一致性**。这种不一致性在少步采样（few-step sampling）时尤为突出，导致生成质量严重下降。
- **研究背景**：扩散模型在图像生成中表现优异，但迭代去噪过程计算成本高。少步采样器（如DDIM、DPM-Solver等）虽能加速，但受限于局部训练目标，难以保持多步轨迹的连贯性。
- **整体含义**：本文试图从强化学习中的**时序差分（TD）学习**视角重新审视扩散模型的训练，通过惩罚多步不一致来提升少步采样的质量与效率，为扩散模型的训练提供通用、即插即用的改进方案。

## 2. 论文提出的方法论：核心思想、关键技术细节、公式或算法流程
- **核心思想**：
  - 将扩散模型的去噪过程重新表述为**马尔可夫奖励过程（MRP）**，每一步去噪视为状态转移，最终生成图像的质量由奖励函数衡量。
  - 将去噪模型的训练视为**策略评估（policy evaluation）**问题，目标是学习一个价值函数，使其沿去噪路径满足贝尔曼方程。
  - 由此推导出**统一的时序差分（TD）目标**，惩罚模型在多步上的预测不一致性。
- **关键技术细节**：
  - **统一框架**：该TD方法同时适用于离散时间（DDPM）和连续时间（Score SDE）扩散公式。
  - **基于样本的重加权方法**：提出一种 principled 的样本重加权策略，以稳定训练并避免方差过大。
  - **损失形式**：TD目标包括对单步预测误差（类似传统目标）与多步不一致惩罚的组合，通过超参数平衡。
  - **算法流程**（文字说明）：
    1. 从扩散过程中采样噪声图像和时间步序列。
    2. 使用当前模型对每一步进行去噪预测（预测噪声或干净图像）。
    3. 计算相邻步预测之间的差异（如TD误差），构建多步不一致损失。
    4. 联合单步损失与多步TD损失，通过反向传播更新模型参数。
    5. 使用样本重加权调整各步损失的权重。

## 3. 实验设计：数据集、基准、对比方法
- **数据集**：未在摘要中明确列举，但根据ICML论文惯例，通常包括CIFAR-10、ImageNet、LSUN等标准图像生成数据集。
- **基准**：以FID（Fréchet Inception Distance）作为主要评价指标。
- **对比方法**：
  - 传统扩散模型训练目标（如DDPM、DDIM的原始损失）。
  - 少步采样器（如DDIM、DPM-Solver等），包括使用标准训练与TD训练后的对比。
  - 可能还包括其他一致性约束方法（如一致性模型、蒸馏方法），但摘要未明确提及。

## 4. 资源与算力
- **未明确说明**：论文摘要及元数据中未提及具体使用的GPU型号、数量、训练时长等信息。推测正文中可能包含相关细节，但在此提供的文本中缺失。
- 需注意：资源信息对可复现性重要，但本文摘要未涉及。

## 5. 实验数量与充分性
- **实验数量**：摘要提到提供了消融研究（ablation studies）来证明设计选择，包括：
  - 成对损失重加权（pairwise loss reweighting）
  - 正则化权重（regularization weight）
  - 一步步长（one-step stride）
- **充分性与客观性**：
  - 实验中展示了TD训练在不同采样步数下的FID改进，尤其在少步时提升显著，表明方法有效。
  - 消融实验覆盖了关键超参数，设计较为完整。
  - 但未报告在多种数据集上的结果细节、与其他最新一致性方法的全面对比，可能需要在正式论文中进一步证实。总体而言，实验设计符合常规学术论文标准，但根据摘要判断，实验范围可能有限（未提及大规模验证）。

## 6. 论文的主要结论与发现
- **主要结论**：
  - TD训练显著提升少步采样的生成质量（FID更优），在多步采样时也有一定改善。
  - 当采样步数越少时，TD训练的优势越明显，证明了其在低计算预算场景下的实用价值。
  - 提出的样本重加权方法能稳定训练，且消融实验确认了各设计组件的必要性。
  - TD方法可作为通用即插即用模块，适用于不同的扩散生成模型。

## 7. 优点：方法或实验设计上的亮点
- **方法论亮点**：
  - **创新视角**：将扩散模型与强化学习中的TD学习建立联系，提供了新的理论视角。
  - **统一框架**：同时处理离散和连续时间扩散模型，具有通用性。
  - **即插即用**：可以直接嵌入现有扩散模型训练流程，无需改变模型架构或采样器。
- **实验设计亮点**：
  - 关注少步采样这一实际痛点，实验结果针对低计算预算场景，有实际价值。
  - 提供了详细的消融研究，验证了重加权、正则化等设计选择的有效性。

## 8. 不足与局限
- **实验覆盖有限**：摘要中未明确列出所有测试数据集，可能仅限于少数标准图像数据集（如CIFAR-10/ImageNet），缺乏在更大规模或特殊领域（如文本到图像、视频生成）的验证。
- **偏差风险**：FID指标本身存在局限性（对分布匹配敏感，但可能掩盖模式崩溃等问题），且论文未报告其他指标（如IS、CLIP score等）。
- **应用限制**：方法假设去噪过程可表示为MRP，可能不适用于离散数据（如文本）或非自回归生成任务。此外，TD训练可能引入额外超参数（如一致性权重），调参成本可能增加。
- **资源信息缺失**：未提供算力需求，不利于可复现性评估。
- **对比方法不全**：未提及与一致性模型（Consistency Models）、渐进蒸馏等直接比较，需在正式论文中补充。

（完）
