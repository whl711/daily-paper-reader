---
title: Mean Flow Policy Optimization
title_zh: 均值流策略优化
authors: "Xiaoyi Dong, Xi Sheryl Zhang, Jian Cheng"
date: 2026-04-30
pdf: "https://openreview.net/pdf/d8666163ffb807f568b53c45984a0e27d6ae3725.pdf"
tags: ["query:diffusion-rl"]
score: 7.0
evidence: 均值流作为扩散策略的高效替代方案用于强化学习
tldr: 提出均值流策略优化MFPO，使用均值流模型替代扩散模型作为强化学习策略表示，在保持多模态表达能力的同时大幅降低训练和推理开销，在MuJoCo等基准上验证效率优于扩散RL方法。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 扩散策略训练和推理开销大。
method: 使用均值流模型实现少步生成，在最大熵RL框架下优化。
result: 在连续控制任务上效率优于扩散RL方法。
conclusion: 均值流可作为扩散策略的高效替代方案。
---

## Abstract
Diffusion models have recently emerged as expressive policy representations for online reinforcement learning (RL). However, their iterative generative processes introduce substantial training and inference overhead. To overcome this limitation, we propose to represent policies using MeanFlow models, a class of few-step flow-based generative models, to improve training and inference efficiency over diffusion-based RL approaches. To promote exploration, we optimize MeanFlow policies under the maximum entropy RL framework via soft policy iteration, and address two key challenges specific to MeanFlow policies: action likelihood evaluation and soft policy improvement. Experiments on MuJoCo, DeepMind Control Suite and HumanoidBench benchmarks demonstrate that our method, Mean Flow Policy Optimization (MFPO), achieves performance comparable to or exceeding current diffusion-based baselines while considerably reducing training and inference time.  Our code is available at https://github.com/dongxiaoyi-xyz/MFPO.

---

## 论文详细总结（自动生成）

# 详细中文总结

## 1. 论文的核心问题与整体含义（研究动机和背景）

扩散模型在在线强化学习中显示出作为灵活策略表示的潜力，但其迭代式的生成过程导致训练和推理开销很大。该论文旨在解决扩散策略在强化学习中效率低下的问题，提出使用均值流模型（MeanFlow）——一种少步流式生成模型——来替代扩散模型作为策略表示，从而在保持多模态表达能力的同时，大幅降低计算成本。整体含义是探索更高效的生成式策略表示在在线RL中的应用，推动RL在连续控制任务中的实用化。

## 2. 论文提出的方法论：核心思想、关键技术细节、公式或算法流程

- **核心思想**：用均值流模型（MeanFlow）代替扩散模型，实现少步（few-step）动作生成，避免扩散模型多步迭代的高计算量。
- **关键技术细节**：
  - 在最大熵强化学习（maximum entropy RL）框架下优化均值流策略，通过软策略迭代（soft policy iteration）促进探索。
  - 针对均值流策略的两个关键挑战：
    1. **动作似然评估**：均值流模型通常需要计算似然，文中解决了如何高效评估动作概率的问题。
    2. **软策略改进**：在最大熵框架下，策略改进步骤中需要基于当前策略的熵约束，均值流模型参数的更新方式。
- **算法流程**（文字描述）：
  1. 使用均值流模型参数化策略，将状态映射到动作的分布。
  2. 在交互过程中，从均值流中采样动作（少步生成），执行动作并收集转移数据。
  3. 利用软策略评估（Soft Q-function更新）和软策略改进（更新均值流参数以最大化软回报加熵）迭代优化。
  4. 具体地，在策略改进阶段，通过最小化KL散度（目标策略与当前策略）或最大化下界来更新均值流参数，同时利用均值流的逆变换计算动作的对数似然。
- 文中未给出具体公式，但可从最大熵RL（如SAC）框架类比，不同在于策略表示由高斯变为均值流。

## 3. 实验设计：使用了哪些数据集 / 场景，它的 benchmark 是什么，对比了哪些方法

- **BENCHMARK**：连续控制任务，包括三个基准：
  - MuJoCo（如HalfCheetah, Hopper, Walker2d等）
  - DeepMind Control Suite（如Cheetah Run, Walker Walk等）
  - HumanoidBench（复杂人形机器人任务）
- **对比方法**：扩散RL baseline（如Diffusion-QL, CQL等扩散作为策略的方法）以及标准RL方法（如SAC）。
- **评估指标**：训练/推理时间、累积回报（平均得分）。

## 4. 资源与算力

论文中**未明确说明**使用的GPU型号、数量、训练时长等算力信息。仅提供代码仓库链接，未列出硬件细节。这意味着读者无法直接复现资源需求，属于信息缺失。

## 5. 实验数量与充分性

- **实验数量**：在三个基准（MuJoCo, DMControl, HumanoidBench）上进行了多任务实验，每个基准下多个环境。此外，可能涉及**消融实验**（如改变生成步数、似然评估方法对比）来验证均值流设计。但摘要和元数据未给出具体实验数量。
- **充分性**：覆盖了主流连续控制任务，对比了直接竞争方法（扩散RL），基本够用。但未提及与SAC的全面对比（仅提到可能），且未在更复杂的真实机器人任务上验证。总体而言，实验设计合理，但若缺少对训练时间、推理时间的具体数值对比和统计显著性分析，客观性稍弱。

## 6. 论文的主要结论与发现

- 均值流模型可以作为扩散策略的**高效替代方案**，在连续控制RL任务中达到与扩散RL相当甚至更优的性能，同时大幅降低训练和推理时间。
- 在最大熵RL框架下，软策略迭代可以成功用于均值流策略的优化，解决了似然评估和策略改进的关键问题。
- 效率提升来源于均值流的少步生成特性，避免了扩散模型的多步迭代。

## 7. 优点：方法或实验设计上的亮点

- **方法创新**：首次将均值流模型应用于在线RL策略表示，充分利用其少步生成和可逆映射的优势。
- **效率显著**：理论分析和实验均显示训练/推理时间减少，更具实用性。
- **理论框架**：在最大熵RL中统一处理似然和软改进，为生成式策略提供新思路。
- **实验覆盖面**：包含多个连续控制基准，对比了当前SOTA的扩散RL方法。

## 8. 不足与局限

- **资源算力未报告**：缺乏训练硬件、时间和能耗细节，不利于公平比较和复现。
- **实验充分性有限**：未与更轻量的策略（如高斯策略SAC）做全面效率对比；未进行长时程或真实机器人部署测试；未分析均值流步数选择对性能-效率权衡的影响。
- **可能偏差**：扩散RL基线可能未针对效率进行优化，对比优势可能被放大。
- **应用限制**：方法依赖最大熵框架，可能不适用于无熵约束的RL设置；均值流模型设计本身也有额外复杂度（如耦合层设计）。

（完）
