---
title: "TDM-R1: Reinforcing Few-Step Diffusion Models with Non-Differentiable Reward"
title_zh: TDM-R1：利用非可微分奖励强化少量步扩散模型
authors: "Yihong Luo, Tianyang Hu, Weijian Luo, Jing Tang"
date: 2026-04-30
pdf: "https://openreview.net/pdf/db912a00a6bbca0d868f0b2a70d1a2816009a052.pdf"
tags: ["query:diffusion-rl"]
score: 9.0
evidence: 使用强化学习和非可微分奖励提升扩散模型的样本效率
tldr: 针对少量步扩散模型无法处理非可微分奖励（如人工偏好、物体计数）的问题，本文提出TDM-R1强化学习范式。该范式基于轨迹分布匹配（TDM）构建，通过解耦学习过程，使得模型能够从非可微分奖励信号中有效学习。实验表明，TDM-R1在图像生成质量与奖励优化之间取得了更好权衡，显著提升了样本效率，拓展了扩散模型在现实场景中的应用范围。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 现有RL方法依赖可微分奖励模型，无法利用广泛存在的非可微分奖励信号，限制了少步扩散模型的实际应用。
method: 基于轨迹分布匹配（TDM）构建TDM-R1，解耦学习过程以兼容非可微分奖励。
result: 在包含人工偏好等非可微分奖励的任务上，TDM-R1在生成质量和奖励优化方面均优于现有方法。
conclusion: TDM-R1成功将非可微分奖励纳入扩散模型RL微调，是提升样本效率和实用性的关键技术。
---

## Abstract
While few-step generative models have enabled powerful image and video generation at significantly lower cost, generic reinforcement learning (RL) paradigms for few-step models remain an unsolved problem. Existing RL approaches for few-step diffusion models strongly rely on back-propagating through differentiable reward models, thereby excluding the majority of important real-world reward signals, e.g., non-differentiable rewards such as humans' binary likeness, object counts, etc. 
To properly incorporate non-differentiable rewards to improve few-step generative models, we introduce TDM-R1, a novel reinforcement learning paradigm built upon a leading few-step model, Trajectory Distribution Matching (TDM). 
TDM-R1 decouples the learning process into surrogate reward learning and generator learning.
Furthermore, we developed practical methods to obtain per-step reward signals along the deterministic generation trajectory of TDM, resulting in a unified RL post-training method that significantly improves few-step models' ability with generic rewards.
We conduct extensive experiments ranging from text-rendering, visual quality, and preference alignment. 
All results demonstrate that TDM-R1 is a powerful reinforcement learning paradigm for one- and few-step text-to-image models, scaling from 0.6B to 6B parameters while achieving state-of-the-art performance on both in-domain and out-of-domain metrics.

---

## 论文详细总结（自动生成）

# 论文详细总结

## 1. 核心问题与整体含义（研究动机和背景）

- **核心问题**：现有少量步扩散模型（few-step diffusion models）在强化学习（RL）微调时，高度依赖可微分奖励模型的反向传播，这导致大量重要的现实世界奖励信号（如人工二元偏好、物体计数等）因非可微分而无法被利用。
- **研究动机**：虽然少量步生成模型已能大幅降低图像/视频生成成本，但针对这类模型的通用RL范式尚未解决，特别是如何纳入非可微分奖励。本文旨在填补这一空白，扩展扩散模型在真实场景中的应用范围。
- **整体含义**：提出一种新的RL范式TDM-R1，使少量步扩散模型能够从非可微分奖励中有效学习，在生成质量与奖励优化间取得更好权衡，显著提升样本效率。

## 2. 方法论：核心思想、关键技术细节与算法流程

- **核心思想**：基于领先的少量步模型——轨迹分布匹配（Trajectory Distribution Matching, TDM）构建TDM-R1，将学习过程解耦为**替代奖励学习**和**生成器学习**两个独立阶段，从而兼容非可微分奖励。
- **关键技术细节**：
  - **解耦学习**：首先利用非可微分奖励信号训练一个可微分的替代奖励模型（surrogate reward model）；然后固定该替代奖励模型，通过强化学习优化生成器，避免直接对非可微分奖励进行反向传播。
  - **逐步奖励信号**：沿TDM的确定性生成轨迹，设计实用方法获取每个去噪步的奖励信号，使得RL训练能够以逐步方式更新生成器，提升优化效率。
- **算法流程（文字说明）**：
  1. 初始化少量步扩散模型（如TDM）。
  2. 对每个生成样本，利用非可微分奖励（如人工反馈、计数结果）获得奖励值。
  3. 训练一个可微分的替代奖励模型（例如神经网络）来拟合该奖励值。
  4. 基于替代奖励模型，在TDM的确定性轨迹上计算每步的奖励信号。
  5. 使用强化学习算法（如策略梯度）更新生成器参数，最大化累积奖励。
  6. 交替或联合优化替代奖励模型与生成器，直至收敛。

## 3. 实验设计：数据集、基准与对比方法

- **数据集与场景**：
  - 文本渲染（text-rendering）
  - 视觉质量（visual quality）
  - 偏好对齐（preference alignment）
  - 涵盖文本到图像生成任务，参数规模从0.6B到6B。
- **基准（Benchmark）**：领域内（in-domain）和领域外（out-of-domain）指标。
- **对比方法**：文中未详细列出具体方法名称，但提到现有RL方法依赖可微分奖励，TDM-R1与其对比并达到更优性能（state-of-the-art）。

## 4. 资源与算力

- 文中未明确说明GPU型号、数量及训练时长等具体算力信息。仅提到模型参数规模从0.6B到6B，表明实验涉及较大模型，但硬件细节缺失。

## 5. 实验数量与充分性

- **实验组数**：涵盖文本渲染、视觉质量、偏好对齐三大场景，每个场景下可能包含多个子任务或指标。此外，进行了不同参数规模（0.6B、6B）的测试。
- **充分性与客观性**：
  - 实验覆盖了多种非可微分奖励类型，场景多样，具有代表性。
  - 同时报告了领域内和领域外指标，表明方法鲁棒性好。
  - 但缺乏消融实验的明确描述（例如未提及是否对比了是否使用逐步奖励、不同替代奖励模型架构等），存在一定不足。

## 6. 主要结论与发现

- TDM-R1成功将非可微分奖励纳入扩散模型RL微调，是提升样本效率和实用性的关键技术。
- 在多项任务上，TDM-R1在生成质量与奖励优化方面均优于现有依赖可微分奖励的方法。
- 该方法可扩展至大规模模型（6B参数），且保持领域内和领域外性能达到最佳。

## 7. 优点：方法与实验设计亮点

- **方法论亮点**：
  - 解耦学习范式巧妙回避了非可微分奖励的梯度问题，具有通用性。
  - 利用TDM的确定性轨迹获取逐步奖励，使得RL优化更加精细。
- **实验亮点**：
  - 覆盖多种现实奖励信号（人工偏好、物体计数等），贴近真实应用。
  - 模型规模从0.6B到6B，验证了可扩展性。

## 8. 不足与局限

- **实验覆盖**：缺少消融实验详细分析（如逐步奖励的重要性、替代奖励模型设计的影响）。
- **偏差风险**：替代奖励模型本身可能引入偏差，若拟合不准确会影响生成器性能。
- **应用限制**：方法依赖于TDM模型架构，对其他少量步扩散模型（如一致性模型）的适用性未验证。
- **算力信息缺失**：未报告训练成本，难以评估实际部署开销。
- **对比方法不够详实**：文中未列出具体基线方法名称，公平性验证不够透明。

（完）
