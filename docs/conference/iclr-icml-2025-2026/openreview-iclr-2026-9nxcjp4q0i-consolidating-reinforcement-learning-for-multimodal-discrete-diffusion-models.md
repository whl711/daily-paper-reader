---
title: Consolidating Reinforcement Learning for Multimodal Discrete Diffusion Models
title_zh: 面向多模态离散扩散模型的强化学习整合
authors: "Tianren Ma, Mu Zhang, Yibing Wang, Qixiang Ye"
date: 2026-01-26
pdf: "https://openreview.net/pdf?id=9nxCJP4q0i"
tags: ["query:diffusion-rl"]
score: 9.0
evidence: 将强化学习应用于离散扩散模型的优化，使用策略梯度方法
tldr: 离散扩散模型（DDM）的奖励优化面临重要性采样困难、轨迹复杂等挑战。本文提出MaskGRPO，首个可扩展的离散扩散强化学习方法，通过理论分析建立重要性估计器，并针对视觉序列定制展开策略。该方法在图像和文本模态上均取得优异性能，弥合了强化学习与离散扩散模型之间的鸿沟。
source: ICLR-2026-Accepted
selection_source: conference_retrieval
motivation: 现有强化学习方法在离散扩散模型上因重要性采样和轨迹问题难以应用。
method: 提出MaskGRPO，通过理论推导建立重要性估计器，并设计模态特定的展开方法。
result: 在图像和文本生成任务上均显著提升奖励优化效果。
conclusion: MaskGRPO使得强化学习在离散扩散模型上首次实现可扩展应用。
---

## Abstract
Optimizing discrete diffusion model (DDM) with rewards remains a challenge—the non-autoregressive paradigm makes importance sampling intractable and rollout complex, puzzling reinforcement learning methods such as Group Relative Policy
Optimization (GRPO). In this study, we introduce **MaskGRPO**, the first viable approach to enable scalable multimodal reinforcement learning in discrete diffusion with effective importance sampling and modality-specific adaptations. To this end,
we first clarify the theoretical foundation for DDMs, which facilitates building an importance estimator that captures valuable token fluctuation for gradient updates. We then delicately tailored the rollout method for visual sequences, which yields diverse completions and reliable optimization gradients. Across math reasoning, coding, and visual generation benchmarks, MaskGRPO brings more stable and efficient updates, **doubling** reinforcement learning gains while speeding up training by up to **30%**. This study establishes MaskGRPO as a systematic policy optimization approach and the first practical way for discretized visual diffusion. The code is available at https://github.com/martian422/MaskGRPO.

---

## 论文详细总结（自动生成）

# 论文中文总结

## 1. 核心问题与整体含义（研究动机和背景）
- **问题**：离散扩散模型（DDM）的非自回归特性导致重要性采样难以进行，且轨迹（rollout）复杂，使得现有强化学习方法（如 GRPO）难以直接应用于 DDM 的奖励优化。
- **动机**：将强化学习（RL）与离散扩散模型结合，以利用奖励信号提升生成质量，但面临理论和方法上的鸿沟。
- **整体含义**：本文旨在首次实现可扩展的多模态离散扩散强化学习，打通 RL 与 DDM 之间的障碍。

## 2. 方法论：核心思想、关键技术细节与流程
- **核心思想**：提出 **MaskGRPO**，通过理论分析建立有效的重要性估计器，并针对视觉序列定制 rollout 策略，实现稳定的策略梯度更新。
- **关键技术细节**：
  - **重要性估计器**：从理论上推导出能捕获 token 波动的估计器，用于梯度更新（解决非自回归下的重要性采样困难）。
  - **模态特定展开**：为视觉序列设计专门的 rollout 方法，生成多样化的完整序列，提供可靠的优化梯度。
- **算法流程**（文字说明）：
  1. 从离散扩散模型中采样部分完成的序列；
  2. 利用重要性估计器对 token 的波动进行加权；
  3. 通过组相对策略优化（GRPO）框架，结合估计器计算梯度；
  4. 针对图像/视觉模态，定制展开策略以生成完整候选；
  5. 更新策略参数以最大化奖励。

## 3. 实验设计
- **数据集/场景**：数学推理、代码生成、视觉生成三个 benchmark。
- **Benchmark**：未具体列出数据集名称（如可能涉及 MATH、HumanEval、图像生成数据集等）。
- **对比方法**：未直接列出，但隐含对比基线包括标准 GRPO 及其他 RL 方法。MaskGRPO 相比它们带来更稳定高效的更新。

## 4. 资源与算力
- **文中未明确说明**：未提及使用的 GPU 型号、数量、训练时长等具体算力信息。仅提到训练速度提升最高 30%，但未给出绝对数值。

## 5. 实验数量与充分性
- **实验数量**：涵盖三个不同模态（数学推理、代码、视觉生成）的 benchmark，每个 benchmark 应有若干子实验。
- **充分性**：实验覆盖多模态，且包含奖励优化效果和训练效率对比，较为全面。但缺少详细的消融实验和超参数敏感性分析。客观性较好，但未披露随机种子等信息。

## 6. 主要结论与发现
- MaskGRPO 是首个可扩展的离散扩散模型强化学习方法，在多模态任务上均有效。
- 相比现有方法，MaskGRPO 使强化学习收益**翻倍**（double RL gains），同时训练速度提升最高 **30%**。
- 证明了离散扩散模型与策略梯度方法的兼容性，弥合了 RL 与 DDM 之间的鸿沟。

## 7. 优点
- **理论贡献**：首次从理论上解决 DDM 中重要性采样的难题，提供扎实的估计器推导。
- **实用性**：提出模态特定的 rollout 策略，适应视觉序列的特殊性，扩展性强。
- **性能提升**：显著提高奖励优化效果和训练效率，指标突出（翻倍增益、30% 加速）。
- **开源代码**：提供 GitHub 仓库，可复现。

## 8. 不足与局限
- **算力资源未公开**：缺乏具体硬件配置，影响可复现性评估。
- **消融实验不足**：未详细展示各组件（如重要性估计器、视觉 rollout）的独立贡献。
- **应用限制**：方法可能仅适用于离散 token 的生成模型，对连续扩散或混合模态尚需验证。
- **偏差风险**：仅报告正面结果，未讨论失败案例或 hyperparameter 敏感性。

（完）
