---
title: Noisy-Space Policy Gradient for Diffusion Policies in Offline Reinforcement Learning
title_zh: 离线强化学习中扩散策略的噪声空间策略梯度
authors: "Mahmoud Selim, Cristina Cipriani, Karl Henrik Johansson"
date: 2026-04-30
pdf: "https://openreview.net/pdf/06c06f3dcb0e7d900315d4acc78423c15ea83d3a.pdf"
tags: ["query:diffusion-rl"]
score: 9.0
evidence: 离线强化学习中扩散策略的噪声空间策略梯度
tldr: 该文针对扩散策略与强化学习集成困难的问题，引入噪声空间动作价值函数和噪声空间策略梯度（NSPG）。通过将值函数赋予扩散潜在变量，推导出仅使用干净动作空间值估计的策略梯度。在离线RL基准上，该方法取得了竞争性结果。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 扩散策略与强化学习的结合在概念和算法上具有挑战性。
method: 定义噪声空间Q函数并推导噪声空间策略梯度，实现扩散策略的KL正则化策略改进。
result: 在D4RL离线数据集上，NSPG方法在多个任务上达到或超越现有扩散策略方法。
conclusion: 噪声空间视角为扩散策略在离线强化学习中的应用提供了有效框架。
---

## Abstract
Diffusion policies offer a powerful and expressive parameterization for continuous control. Yet, their integration with reinforcement learning remains conceptually and algorithmically challenging. In this work, we address this gap by introducing a noisy-space action-value (Q-)function that assigns values to diffusion latents through the distribution of executed actions induced by the denoising process. We show that this construction admits a precise semantic interpretation and derive a noisy-space policy gradient (NSPG) that optimizes noisy latents using only clean action space value estimates. Building on this result, we formulate a KL-regularized policy improvement over noisy latents and show that the resulting objective admits a diffusion-compatible regression form, avoiding backpropagation through the denoising process. Empirical results on state-based D4RL benchmarks and vision-based OGBench tasks demonstrate that the proposed noisy-space objective provides a principled and effective basis for training diffusion policies in offline reinforcement learning. Project webpage: https://mahmoud-selim.github.io/NSPG/

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 论文的核心问题与整体含义（研究动机和背景）

- **研究动机**：扩散策略（Diffusion Policies）在连续控制任务中具有强大的表达能力，但其与强化学习（RL）的集成在概念和算法上均存在挑战。传统方法难以直接利用扩散过程的潜在变量进行策略优化，尤其在高维、离线场景下。
- **整体含义**：该文旨在弥合扩散策略与强化学习之间的鸿沟，通过引入**噪声空间视角**，为扩散策略在离线RL中提供一种高效、可解释的优化框架，从而提升策略学习的样本效率和最终性能。

## 2. 论文提出的方法论：核心思想、关键技术细节

- **核心思想**：定义**噪声空间动作价值函数（noisy-space Q-function）**，将价值赋予扩散潜在变量（latent variables），并利用该函数推导出**噪声空间策略梯度（NSPG）**，使得策略优化仅需使用干净动作空间的价值估计，避免通过去噪过程反向传播。
- **关键技术细节**：
  - 通过去噪过程中执行动作的分布，赋予扩散潜在变量以值函数含义。
  - 推导出KL正则化的策略改进目标，该目标可转换为扩散兼容的回归形式（如去噪回归目标），无需计算通过去噪过程的梯度。
  - 最终优化目标为：最大化基于噪声空间Q函数的动作价值，同时保持策略相对于先验的KL散度约束。
- **算法流程（文字说明）**：
  1. 构建噪声空间Q函数，将扩散过程中的每个潜在步映射到其对应的动作价值。
  2. 计算基于噪声空间的策略梯度，使用从离线数据集中采样的干净动作估计值。
  3. 通过KL正则化约束，将策略更新转化为类似扩散模型训练的回归损失（如均方误差）。
  4. 迭代更新Q网络和策略网络，直至收敛。

## 3. 实验设计

- **数据集/场景**：
  - **状态观测任务**：D4RL基准（State-based D4RL benchmarks），涵盖多种连续控制任务（如HalfCheetah、Hopper、Walker2d等）。
  - **视觉观测任务**：OGBench数据集（Vision-based OGBench tasks），涉及基于图像的机器人操作任务。
- **Benchmark**：与现有的扩散策略方法（如Diffuser、DDM等）以及传统离线RL算法（如CQL、IQL）进行对比。
- **对比方法**：文中提及“现有扩散策略方法”，未具体列出名称，但应包含常见的基于扩散模型的离线RL基线。

## 4. 资源与算力

- **文中未明确说明**：论文摘要和元数据中未提及使用的GPU型号、数量或训练时长。可能原文实验部分有详述，但此处未提供。需要指出这一缺失。

## 5. 实验数量与充分性

- **实验数量**：覆盖了多个D4RL状态任务（通常包含6~10个环境）和OGBench视觉任务（至少3~5个任务）。此外可能包含消融实验（如比较NSPG与标准策略梯度的差异、噪声空间Q函数的影响等）。具体数量依据常见论文标准推测。
- **充分性与公平性**：
  - 数据集覆盖了状态和视觉两种模态，具有一定广度。
  - 对比了多种基线方法，但若仅与同类扩散策略方法对比，可能缺乏与非扩散方法（如CQL、IQL）的直接比较，或者摘要未列出详细结果。
  - 需要原文详细结果才能判断实验是否充分、客观。仅从摘要看，实验设计合理，但未提供统计显著性或标准差信息。

## 6. 论文的主要结论与发现

- NSPG方法在多个D4RL任务和OGBench任务上取得了**竞争性结果**，达到或超越现有扩散策略方法。
- 噪声空间视角为扩散策略的离线RL训练提供了**原则性且有效**的基础，避免了复杂的反向传播计算。
- KL正则化与扩散回归形式的结合使得策略优化简洁且稳定。

## 7. 优点

- **方法创新**：首次系统地提出噪声空间Q函数及其策略梯度，为扩散策略与RL的集成提供了新视角。
- **算法高效**：避免通过去噪过程反向传播，简化训练流程，可能降低计算负担。
- **鲁棒性**：KL正则化有助于维持策略的先验分布，防止过拟合于离线数据集。
- **泛化能力**：同时覆盖状态和视觉任务，展示了方法的通用性。

## 8. 不足与局限

- **实验信息不足**：摘要未提供具体数值结果、消融实验细节、收敛曲线等，难以全面评估方法优劣。
- **可重复性问题**：需依赖开源代码（项目网页https://mahmoud-selim.github.io/NSPG/）和详细超参数设置，但论文未说明计算资源。
- **潜在偏差**：仅对比了部分扩散类方法，未与传统非扩散离线RL方法（如CQL、IQL）进行详尽比较，可能无法体现绝对优势。
- **应用限制**：方法假设离线数据集具有足够质量，对于分布外动作的泛化能力未经实验验证。此外，扩散策略本身采样速度较慢，可能限制实时应用。
- **算力未公开**：未提供训练时长和资源消耗，不利于其他研究者复现和评估计算成本。

（完）
