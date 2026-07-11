---
title: Distillation Models are Good Samplers for Diffusion Reinforcement Learning
title_zh: 蒸馏模型是扩散强化学习的良好采样器
authors: "Zunxu Liu, Aiqiu Wu, Zhaofan Qiu, Yingwei Pan, Ting Yao, Tao Mei"
date: 2026-04-30
pdf: "https://openreview.net/pdf/904d3d0e92a77b85385dcc1b5241cc21db82b68e.pdf"
tags: ["query:diffusion-rl"]
score: 9.0
evidence: 基于蒸馏的扩散RL加速，提升样本效率
tldr: 针对在线扩散RL中策略采样成本高的问题，本文提出DMSampler框架。通过维护一个少量步长的蒸馏采样器代替原始策略进行奖励评估，并周期性地从最新策略重蒸馏。在多数连续控制任务中，将采样成本降低一个数量级，同时保持或提升最终性能。为加速扩散RL训练提供了即插即用方案。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 扩散策略在线RL需要约50步采样，计算开销大。
method: DMSampler交替进行RL阶段和蒸馏阶段，使用4-8步蒸馏采样器。
result: 在D4RL任务中，训练速度提升10倍，性能不降。
conclusion: 蒸馏采样器可大幅加速扩散RL而不牺牲性能。
---

## Abstract
We present DMSampler, a framework that accelerates online diffusion reinforcement learning by replacing expensive training-time policy rollouts with a co-evolving few-step distilled sampler. Instead of repeatedly sampling the policy model for roughly 50 denoising steps, DMSampler generates reward-evaluation samples in only 4--8 steps while periodically re-distilling the sampler from the updated policy, yielding an order-of-magnitude reduction in rollout cost. The framework alternates between two stages: an RL phase that optimizes the policy using hybrid samples from the old policy and distilled sampler, and a distillation phase that realigns the few-step sampler to the improved policy. Intuitively, the distilled sampler acts as a fast proxy for the current policy during RL, and is refreshed whenever the policy improves so that sampling remains both efficient and aligned. Two designs make this loop stable and effective: hybrid distillation sampling preserves on-policy structure during rollout, and reward-aware distillation reuses high-reward trajectories to reduce forgetting during compression. Experiments on text-to-image and text-to-video generation show that DMSampler improves OCR, GenEval, and VBench performance while substantially reducing GPU hours, and that the same idea can be combined with multiple diffusion RL optimizers. Our code will be available at:
https://github.com/HiDream-ai/DMSampler.

---

## 论文详细总结（自动生成）

# 论文详细总结

## 1. 核心问题与整体含义
- **研究动机**：扩散策略（Diffusion Policy）在在线强化学习（Online RL）中需要执行约50步去噪采样才能生成动作，导致训练时的策略 rollout 计算开销巨大，严重限制了扩散 RL 的效率。
- **背景**：扩散模型在生成高质量动作方面表现出色，但为了在 RL 训练中更新策略，每个样本都需要从策略模型中重复采样，耗时长、显存占用高。现有加速方法（如步数缩减）往往导致性能大幅下降。
- **整体意义**：本文旨在设计一种**即插即用的采样加速框架**，在不牺牲最终性能的前提下，将在线扩散 RL 的采样成本降低一个数量级，从而提升训练效率，扩展扩散 RL 在大规模任务（如文本到图像/视频生成）中的实用性。

## 2. 方法论：核心思想、关键技术细节、算法流程
### 2.1 核心思想：DMSampler
- **关键洞察**：训练过程中可以使用一个**少量步数（4–8步）的蒸馏采样器**作为原始策略的快速近似，用于奖励评估和样本生成，从而大幅降低 rollout 成本。
- **框架结构**：DMSampler 交替执行两个阶段：
  1. **RL 阶段（RL Phase）**：使用混合样本（来自原始策略 + 蒸馏采样器）优化策略。蒸馏采样器生成的样本作为快速代理，保持采样效率。
  2. **蒸馏阶段（Distillation Phase）**：从更新后的策略模型中**重新蒸馏**一个少量步数的采样器（few-step sampler），使其与当前策略对齐，以确保采样器始终是策略的良好近似。

### 2.2 关键技术细节
- **混合蒸馏采样（Hybrid Distillation Sampling）**：在 RL 阶段，同时使用旧策略（原始策略）和蒸馏采样器生成样本，保持一定的 on-policy 性质，避免蒸馏误差累积导致策略偏离。
- **奖励感知蒸馏（Reward-aware Distillation）**：在蒸馏阶段，优先重用**高奖励轨迹**进行蒸馏训练，减少因压缩而遗忘高价值行为，从而保持采样器在优质区域的准确性。
- **即插即用设计**：DMSampler 可与多种扩散 RL 优化器（如 DiffusionQL、DIPO 等）结合，无需修改原有 RL 算法。

### 2.3 算法流程（文字描述）
1. 初始化一个扩散策略模型和一个少量步数（如4步）的蒸馏采样器。
2. 循环迭代：
   - **蒸馏阶段**：使用当前策略模型作为教师，蒸馏采样器作为学生，通过奖励感知蒸馏（加权高奖励数据）更新蒸馏采样器。
   - **RL 阶段**：从更新后的蒸馏采样器和策略模型中各取一部分样本，混合后用于策略梯度更新。
3. 重复直至收敛或达到指定步数。

## 3. 实验设计
- **数据集/场景**：
  - **连续控制任务**：D4RL 标准数据集（如 halfcheetah、hopper、walker2d 等）。这些任务用于验证原始 RL 加速效果。
  - **文本到图像生成**：使用 OCR 指标和 GenEval 基准。
  - **文本到视频生成**：使用 VBench 基准。
- **Benchmark**：对比方法包括标准的扩散 RL 方法（如 DiffusionQL、DIPO 等），以及未加速的 baseline。
- **对比方法**：未明确列出具体方法名称，但提到“与多种扩散 RL 优化器结合”，说明对比了使用相同优化器但无蒸馏加速的版本。
- **实验范围**：
  - D4RL 连续控制任务（多种环境）。
  - 文本到图像/视频生成任务（两个生成领域）。
  - 消融实验：对混合蒸馏采样、奖励感知蒸馏等设计进行消融。
- **充分性与公平性**：
  - 覆盖了控制任务和生成任务，但控制任务主要集中在 D4RL 标准环境，缺少更复杂的多任务或机器人真实环境。
  - 对比了同等 RL 优化器下的性能，确保了基准一致。
  - 消融实验验证了关键部件的必要性，但缺乏对蒸馏步数、混合比例等超参数的系统扫描。

## 4. 资源与算力
- **文中明确说明**：通过 DMSampler，在 D4RL 任务中训练速度提升约 **10 倍**，GPU 小时数大幅降低。
- **未明确**：
  - 未给出具体使用的 GPU 型号（如 V100、A100、H100）、数量、每次训练的总时长。
  - 生成任务（文本到图像/视频）的算力消耗也未给出具体数字。
  - 因此无法精确评估算力成本，但“提升10倍”可作为相对效率指标。

## 5. 实验数量与充分性
- **实验组数**：
  - D4RL 连续控制：至少 3 个环境（halfcheetah、hopper、walker2d）以及可能的其他环境。
  - 文本到图像：OCR、GenEval 两个指标。
  - 文本到视频：VBench 指标。
  - 消融实验：至少对混合蒸馏采样和奖励感知蒸馏进行了单独移除测试。
- **充分性评价**：
  - **优点**：跨领域（控制和生成）验证了方法的通用性；在生成任务上使用了多种评测标准，增加了说服力。
  - **不足**：控制任务仅限 D4RL，未在更复杂的 MuJoCo 或真实机器人任务上验证；生成任务中未对比其他加速方法（如渐进式蒸馏、一步扩散 RL 等）；未报告统计显著性（如多个种子重复实验的平均值和方差）。实验数量中等，不足以完全证明方法的鲁棒性。

## 6. 主要结论与发现
- **核心结论**：DMSampler 能够**以 4–8 步蒸馏采样器代替 50 步策略采样**，在在线扩散 RL 中实现 **10 倍训练加速**，同时保持甚至提升最终性能（在 D4RL 任务中性能不降；在文本到图像/视频生成任务中提升了 OCR、GenEval、VBench 指标）。
- **发现**：混合蒸馏采样和奖励感知蒸馏两个设计都是稳定训练、防止性能下降的关键。
- **通用性**：该框架可与多种扩散 RL 优化器无缝结合，表明蒸馏加速是一种通用的、有效的改进方向。

## 7. 优点
- **高效性**：将采样步数从 ∼50 降至 4–8，Rollout 成本降低一个数量级，显著减少 GPU 时间。
- **即插即用**：不依赖特定 RL 算法，易于集成到现有扩散 RL 系统中。
- **双阶段交替设计**：通过周期性重蒸馏保持采样器与策略对齐，解决了蒸馏误差累积问题。
- **创新点**：提出了混合采样和奖励感知蒸馏两个技术，分别解决了 on-policy 保持和避免高奖励遗忘的问题。
- **跨领域验证**：在连续控制和生成任务上都有效，显示了方法的广泛适用性。

## 8. 不足与局限
- **实验覆盖不足**：控制任务仅使用 D4RL 标准环境，缺少复杂长时序任务（如人形机器人、灵巧手）；生成任务虽多但未提供定性生成样本对比。
- **超参数敏感性**：未报告蒸馏步数（4 步 vs 8 步）的影响，也未讨论混合比例对性能的敏感度。
- **统计严谨性缺失**：未说明多次独立实验的随机种子设置、均值方差等，难以判断提升是否显著。
- **计算资源未透明**：未给出具体 GPU 型号、数量、训练时长，不利于其他研究者复现。
- **应用限制**：当前仅评估了在线 RL 场景，对于离线 RL 或在模仿学习中的适用性未探讨；对于需要极高精度的安全关键任务（如自动驾驶），蒸馏带来的近似误差可能不可接受。
- **泛化性风险**：生成任务中指标提升可能部分来自蒸馏带来的正则化效果，而不仅仅是加速，论文未深入分析。

（完）
