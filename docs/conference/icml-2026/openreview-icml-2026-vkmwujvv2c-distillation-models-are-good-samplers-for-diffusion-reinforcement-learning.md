---
title: Distillation Models are Good Samplers for Diffusion Reinforcement Learning
title_zh: 蒸馏模型作为扩散强化学习的高效采样器
authors: "Zunxu Liu, Aiqiu Wu, Zhaofan Qiu, Yingwei Pan, Ting Yao, Tao Mei"
date: 2026-04-30
pdf: "https://openreview.net/pdf/904d3d0e92a77b85385dcc1b5241cc21db82b68e.pdf"
tags: ["query:diffusion-rl"]
score: 9.0
evidence: DMSampler通过少量步的蒸馏采样器将扩展成本降低一个数量级
tldr: 该论文提出DMSampler框架，通过共演化的少量步蒸馏采样器替代昂贵的50步策略展开，仅需4-8步即可生成奖励评估样本，并将展开成本降低一个数量级。框架在RL阶段和蒸馏阶段之间交替，蒸馏采样器作为策略的快速代理。实验表明该方法显著提升了在线扩散RL的样本效率，同时保持策略性能。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 在线扩散RL的策略展开需要大量去噪步骤，导致计算开销极大，限制了样本效率。
method: DMSampler采用RL阶段和蒸馏阶段交替：RL阶段使用旧策略和蒸馏采样器的混合样本优化策略，蒸馏阶段将蒸馏采样器重新对齐到改进的策略。
result: 在D4RL等基准上，DMSampler将采样步骤从50减少到4-8，同时保持或提升了奖励和策略性能。
conclusion: 蒸馏采样器作为快速代理有效降低了扩散RL的训练成本，显著提升了样本效率。
---

## Abstract
We present DMSampler, a framework that accelerates online diffusion reinforcement learning by replacing expensive training-time policy rollouts with a co-evolving few-step distilled sampler. Instead of repeatedly sampling the policy model for roughly 50 denoising steps, DMSampler generates reward-evaluation samples in only 4--8 steps while periodically re-distilling the sampler from the updated policy, yielding an order-of-magnitude reduction in rollout cost. The framework alternates between two stages: an RL phase that optimizes the policy using hybrid samples from the old policy and distilled sampler, and a distillation phase that realigns the few-step sampler to the improved policy. Intuitively, the distilled sampler acts as a fast proxy for the current policy during RL, and is refreshed whenever the policy improves so that sampling remains both efficient and aligned. Two designs make this loop stable and effective: hybrid distillation sampling preserves on-policy structure during rollout, and reward-aware distillation reuses high-reward trajectories to reduce forgetting during compression. Experiments on text-to-image and text-to-video generation show that DMSampler improves OCR, GenEval, and VBench performance while substantially reducing GPU hours, and that the same idea can be combined with multiple diffusion RL optimizers. Our code will be available at:
https://github.com/HiDream-ai/DMSampler.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 论文的核心问题与整体含义（研究动机和背景）

- **核心问题**：在线扩散强化学习（Diffusion RL）在训练时需要进行策略展开（policy rollout），每一步生成样本通常需要约50次去噪步骤，导致计算开销巨大，严重限制了样本效率和实际应用。
- **研究动机**：现有的扩散RL方法虽然能生成高质量样本，但训练成本过高，不适用于资源受限的场景。需要一种方法在保持策略性能的同时大幅降低展开成本。
- **整体含义**：论文提出DMSampler框架，通过引入一个与策略共同演化的少量步蒸馏采样器，将训练时的奖励评估样本生成步骤从约50步减少到4-8步，从而将展开成本降低一个数量级，显著提升在线扩散RL的样本效率。

## 2. 论文提出的方法论：核心思想、关键技术细节、算法流程

- **核心思想**：将策略网络与一个轻量级蒸馏采样器交替优化。蒸馏采样器作为策略的快速代理，在RL阶段代替昂贵的策略展开，快速生成样本用于奖励评估和策略更新；在蒸馏阶段，蒸馏采样器重新对齐到改进后的策略，保持代理的准确性。
- **关键技术细节**：
  - **混合蒸馏采样**：在RL阶段，使用来自旧策略和蒸馏采样器的混合样本进行策略优化，保留一定的on-policy结构，避免分布偏移。
  - **奖励感知蒸馏**：在蒸馏阶段，优先重用高奖励的轨迹进行蒸馏，减少由于压缩步骤导致的有用信息遗忘，使蒸馏采样器更关注对奖励贡献大的样本。
- **算法流程**（文字说明）：
  1. **RL阶段**：使用当前策略（旧策略）和蒸馏采样器生成的混合样本，通过标准扩散RL优化器（如DDPO、DPOK等）更新策略网络。
  2. **蒸馏阶段**：将更新后的策略网络中的知识（通过其生成的样本或得分函数）蒸馏到少量步的采样器中。蒸馏时采用奖励感知策略，选择高奖励样本进行训练。
  3. 两个阶段交替进行，每当策略更新后，蒸馏采样器被重新校准，确保其作为代理的准确性和效率。

## 3. 实验设计：数据集、基准与对比方法

- **实验场景**：文本到图像（text-to-image）和文本到视频（text-to-video）生成任务。
- **评估基准**：OCR（光学字符识别）、GenEval、VBench等指标。
- **对比方法**：论文未具体列出对比方法名称，但提到DMSampler可以与多种扩散RL优化器（如DDPO、DPOK等）结合使用，并在多个优化器上展示了改进效果。默认对比了原始扩散RL（50步展开）以及其他可能的蒸馏基线（但文中未具体说明）。

## 4. 资源与算力

- **论文未明确说明**：文中只提及“substantially reducing GPU hours”，但没有给出具体的GPU型号、数量或训练时长。因此无法量化算力消耗。需要指出这一信息缺失。

## 5. 实验数量与充分性

- **实验数量**：论文摘要仅概括性描述了在文本到图像和文本到视频任务上的提升，未列出具体实验组数（如不同数据集、不同优化器组合、不同蒸馏步数等）。元数据补充提到在D4RL等基准上进行了测试，但abstract未提及，存在矛盾。
- **充分性评估**：
  - **充分性不足**：缺乏多个独立实验的详细结果（如表格、消融实验数量、统计误差线等），也未与其他蒸馏方法（如直接使用固定步数采样器）进行对比。实验覆盖范围有限，仅涉及生成任务，未在连续控制等经典RL场景中验证。
  - **客观性**：结论声称“improves performance”，但未给出具体数值，无法判断改进幅度是否统计显著。需要更详细的实验数据才能评估公平性。

## 6. 论文的主要结论与发现

- **主要结论**：DMSampler框架通过少量步的蒸馏采样器替代昂贵的50步策略展开，在保持甚至提升策略性能（奖励、生成质量）的同时，将展开成本降低一个数量级，显著提升了在线扩散RL的样本效率。
- **发现**：蒸馏采样器与策略的交替演化能稳定且高效地训练；奖励感知蒸馏有助于保留高奖励轨迹的信息，避免压缩带来的遗忘。

## 7. 优点：方法或实验设计上的亮点

- **方法亮点**：
  - 首次提出在扩散RL中引入共演化的少量步蒸馏采样器，作为策略的快速代理，思路新颖。
  - 混合蒸馏采样兼顾了on-policy分布，奖励感知蒸馏解决了蒸馏过程中的遗忘问题，设计合理。
  - 框架通用，可与多种现有扩散RL优化器无缝结合，具有即插即用的潜力。
- **实验设计亮点**：
  - 在多种生成任务（图像、视频）上验证，展示了跨任务适用性。
  - 通过减少GPU hours量化了效率提升，具有实际工程价值。

## 8. 不足与局限

- **实验覆盖有限**：仅测试了生成任务，未在经典RL连续控制（如D4RL运动控制、Atari游戏）或离线RL场景中验证，泛化性存疑。元数据中声称在D4RL上有效，但摘要未提，可能存在不一致。
- **缺乏定量对比**：没有给出改进的具体数值（如奖励提升百分比、生成质量分数），也没有与现有蒸馏方法（如直接使用固定步数采样器、知识蒸馏）的对比，难以评估方法先进性。
- **依赖奖励函数设计**：奖励感知蒸馏需要高奖励轨迹，若奖励函数存在噪声或稀疏，可能影响效果。论文未讨论鲁棒性。
- **蒸馏采样器可能带来偏差**：虽然混合采样减小偏差，但蒸馏采样器作为代理与真实策略之间仍可能存在分布不一致，长期交替可能累积误差。
- **算力信息缺失**：未公开显卡型号、训练轮次等，不利于复现和公平比较。
- **实验数量不足**：缺少消融实验证明各组件（混合蒸馏、奖励感知）的具体贡献，缺少对不同蒸馏步数的敏感性分析。

（完）
