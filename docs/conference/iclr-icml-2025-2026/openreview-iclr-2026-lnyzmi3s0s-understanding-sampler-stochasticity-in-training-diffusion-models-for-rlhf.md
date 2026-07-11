---
title: Understanding Sampler Stochasticity in Training Diffusion Models for RLHF
title_zh: 理解RLHF训练扩散模型中的采样随机性
authors: "Jiayuan Sheng, Hanyang Zhao, Haoxian Chen, Wenpin Tang, David Yao"
date: 2025-09-19
pdf: "https://openreview.net/pdf?id=LnYzMi3s0s"
tags: ["query:diffusion-rl"]
score: 8.0
evidence: 扩散模型RLHF中采样随机性的理论分析
tldr: 针对RLHF训练扩散模型中的采样随机性不一致问题，建立了偏好-奖励差距的非平凡界，并提出利用随机gDDIM方案在保持数据边缘分布的同时实现任意高随机性，为理解扩散模型RLHF的采样设计提供理论指导。
source: ICLR-2026-Rejected-Public
selection_source: conference_retrieval
motivation: 训练与推理时采样随机性差异导致偏好-奖励差距，需要理论分析。
method: 建立非平凡界，利用随机gDDIM方案实现可控随机性并保持数据边缘分布。
result: 给出了针对通用扩散模型以及VE/VP模型的界，并通过实验验证。
conclusion: 为扩散模型RLHF的采样设计提供了理论依据和实用方案。
---

## Abstract
Reinforcement Learning from Human Feedback (RLHF) improves pretrained generative models, and its sampling design is important for training reliable, high-quality models. In practice, stochastic SDE samplers promote exploration during training, while deterministic ODE samplers enable fast, stable inference; this creates a discrepancy in sampling stochasticity that induces a preference-reward gap. In this paper, we establish a non-vacuous bound on this gap for general diffusion models and a sharper bound for Variance Exploding (VE) and Variance Preserving (VP) models with (mixture) Gaussian data. Methodologically, we leverage the stochastic gDDIM scheme to attain arbitrarily high stochasticity while preserving data marginals, and we evaluate, under multiple preference rewards, the performance of RL algorithms (e.g., log-likelihood and group-relative policy variants). Our numerical experiments validate that reward gaps consistently narrow over training, and ODE sampling quality improves when models are updated using higher-stochasticity SDE training.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 核心问题与整体含义（研究动机和背景）
- **研究动机**：在扩散模型的强化学习人类反馈（RLHF）训练中，训练阶段通常采用随机SDE采样器以促进探索，而推理阶段则使用确定性ODE采样器以实现快速稳定的采样。这种训练与推理之间采样随机性的不一致会导致“偏好-奖励差距”（preference-reward gap），即模型在训练时学到的偏好与最终推理时实际奖励之间的偏差。
- **整体含义**：该差距的存在会降低RLHF训练的有效性和模型的可靠性，因此需要从理论上理解并控制该差距，从而为扩散模型RLHF的采样设计提供理论指导。

## 2. 论文提出的方法论
- **核心思想**：建立偏好-奖励差距的非平凡（non-vacuous）上界，并提出一种可控随机性的采样方案，在保持数据边缘分布的同时缩小该差距。
- **关键技术细节**：
  - 针对通用扩散模型，推导出偏好-奖励差距的一般性界；针对Variance Exploding（VE）和Variance Preserving（VP）模型以及（混合）高斯数据，给出更紧的界。
  - 利用**随机gDDIM方案**（stochastic generalized DDIM）：该方案允许在训练过程中实现任意高程度的随机性，同时保持数据边际分布不变，从而在训练阶段模拟高随机探索，在推理阶段可以退化为确定性ODE采样。
  - 评估了多种偏好奖励下的RL算法，例如log-likelihood和group-relative policy变体。
- **公式或算法流程（文字说明）**：
  - 首先定义偏好-奖励差距的数学表达式。
  - 通过分析SDE采样与ODE采样在随机性上的差异，利用概率论和随机过程工具推导出上界。
  - 引入随机gDDIM，通过调节噪声注入程度（参数）来连续控制采样随机性，且保证边缘分布与目标扩散过程一致。
  - 在RLHF训练循环中，使用高随机性的SDE（如随机gDDIM）更新模型参数，计算偏好奖励；推理时采用低随机性或确定性ODE。

## 3. 实验设计
- **数据集/场景**：文中未明确说明具体数据集，仅提到使用（混合）高斯数据进行了理论推导，并在数值实验中验证了奖励差距和采样质量。可能使用了合成数据或标准图像数据集（但未提及）。
- **Benchmark**：未提及具体的对比基准方法。
- **对比方法**：未详细说明。可能对比了不同随机性水平的采样方案，以及不同RL算法变体（如log-likelihood vs. group-relative policy）。

## 4. 资源与算力
- **文中未明确说明**：未提及使用的GPU型号、数量、训练时长等算力信息。因此无法总结。

## 5. 实验数量与充分性
- **实验数量**：摘要仅提到“numerical experiments validate that reward gaps consistently narrow over training, and ODE sampling quality improves”，未列出具体实验组数（例如不同随机性水平、不同偏好奖励、不同数据分布等）。可能实验数量有限，主要作为理论结论的验证。
- **充分性与客观性**：由于缺乏数据集、基准方法和详细实验设置，无法判断实验是否充分、客观。理论部分较扎实，但实验覆盖可能不够全面，例如仅验证了高斯数据情况，缺少在真实图像/文本数据集上的评测。

## 6. 主要结论与发现
- 偏好-奖励差距存在非平凡上界，且对于VE/VP模型与高斯数据可得到更紧的界。
- 随机gDDIM方案能够实现任意高随机性并保持数据边缘分布，从而在训练中有效缩小偏好-奖励差距。
- 实验验证：随着训练进行，奖励差距持续缩小；使用更高随机性SDE训练更新的模型，其ODE采样质量显著提升。

## 7. 优点
- **理论贡献**：首次为扩散模型RLHF中的采样随机性差异建立非平凡理论上界，提供了严谨的分析框架。
- **方法实用**：随机gDDIM方案不仅理论上可控，而且易于实现，能够兼容现有扩散模型和RLHF流程。
- **兼顾探索与效率**：通过调节随机性，在训练时促进探索，推理时保持快速稳定，弥补了SDE/ODE分离的不足。

## 8. 不足与局限
- **实验覆盖有限**：仅提及数值实验，未提供具体数据集、对比方法和定量指标，难以评估方法的通用性和实际性能。
- **理论假设较强**：对VE/VP模型的分析依赖于（混合）高斯数据假设，对于非高斯数据（如图像、文本）的扩展性未讨论。
- **缺乏消融研究**：未分析不同随机性水平对收敛速度、最终性能的影响，也未与现有RLHF基线方法（如DDPO、DPO等）比较。
- **算力和资源未公开**：缺少复现所需的环境细节，降低了可重复性。

（完）
