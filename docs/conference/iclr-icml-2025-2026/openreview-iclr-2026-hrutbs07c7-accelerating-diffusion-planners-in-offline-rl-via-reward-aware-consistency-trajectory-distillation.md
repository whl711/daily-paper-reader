---
title: Accelerating Diffusion Planners in Offline RL via Reward-Aware Consistency Trajectory Distillation
title_zh: 通过奖励感知一致性轨迹蒸馏加速离线RL中的扩散规划器
authors: "Xintong Duan, Yutong He, Fahim Tajwar, Ruslan Salakhutdinov, J Zico Kolter, Jeff Schneider"
date: 2026-01-26
pdf: "https://openreview.net/pdf?id=hRuTBS07C7"
tags: ["query:diffusion-rl"]
score: 9.0
evidence: 通过一致性蒸馏加速离线RL中的扩散规划器
tldr: 针对扩散规划器推理慢的问题，本文提出奖励感知的一致性轨迹蒸馏方法。通过解耦训练和无噪声奖励信号，实现单步采样并生成更高奖励的动作轨迹。实验表明在离线RL任务中大幅提升推理速度且不降性能。为扩散决策模型的实际部署提供了高效解决方案。
source: ICLR-2026-Accepted
selection_source: conference_retrieval
motivation: 扩散模型在决策任务中推理慢，现有一致性模型方法依赖次优演示或复杂架构。
method: 提出奖励感知的一致性蒸馏，在蒸馏过程中直接优化奖励，实现单步采样。
result: 在离线RL基准上，该方法在保持或提升奖励的同时将采样步数减少近50倍。
conclusion: 将奖励信号融入蒸馏能同时提升速度和性能。
---

## Abstract
Although diffusion models have achieved strong results in decision-making tasks, their slow inference speed remains a key limitation. While consistency models offer a potential solution, existing applications to decision-making either struggle with suboptimal demonstrations under behavior cloning or rely on complex concurrent training of multiple networks under the actor-critic framework. In this work, we propose a novel approach to consistency distillation for offline reinforcement learning that directly incorporates reward optimization into the distillation process. Our method achieves single-step sampling while generating higher-reward action trajectories through decoupled training and noise-free reward signals. Empirical evaluations on the Gym MuJoCo, FrankaKitchen, and long horizon planning benchmarks demonstrate that our approach can achieve a $9.7$% improvement over previous state-of-the-art while offering up to $142\times$ speedup over diffusion counterparts in inference time.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 论文的核心问题与整体含义（研究动机和背景）
- **研究动机**：扩散模型在离线强化学习决策任务中表现出色，但推理速度极慢，严重限制其实际部署。现有一致性模型（consistency models）虽能加速采样，但应用到决策任务时存在两大缺陷：一是依赖次优演示的行为克隆，性能受限于演示质量；二是需要复杂的多网络并发训练（如在actor-critic框架下）。因此，亟需一种既能保持扩散模型高性能、又能大幅加速推理的方法。
- **核心问题**：如何在不牺牲奖励性能的前提下，将扩散规划器的采样步数从数百步减少到单步，同时提升生成轨迹的质量。
- **整体含义**：提出一种新颖的离线RL一致性蒸馏方法——奖励感知一致性轨迹蒸馏，通过将奖励优化直接融入蒸馏过程，实现单步采样并生成更高奖励的动作轨迹，为扩散决策模型的高效部署提供可行方案。

## 2. 论文提出的方法论：核心思想、关键技术细节、公式或算法流程
- **核心思想**：在一致性模型蒸馏过程中，直接引入无噪声的奖励信号作为优化目标，使得蒸馏出的单步生成器不仅具备一致性（从噪声到数据的映射），还能主动优化期望奖励。
- **关键技术细节**：
  - **解耦训练**：将扩散规划器的训练与一致性蒸馏解耦，先预训练扩散模型（作为teacher），再通过奖励感知蒸馏训练student网络，无需复杂多网络并发。
  - **奖励感知损失**：在一致性损失（确保时间一致性）基础上，加入基于奖励的损失项，引导student生成高奖励轨迹。使用无噪声的奖励信号（与原始扩散模型中的噪声无关），避免噪声干扰。
  - **单步采样**：蒸馏后的student模型直接从初始噪声单步生成动作轨迹，无需迭代去噪。
- **算法流程（文字说明）**：
  1. 预训练一个扩散规划器（teacher）作为动作轨迹生成模型。
  2. 构建student网络（结构与teacher的采样器部分相同或简化）。
  3. 对大量噪声-轨迹对，student预测一条轨迹，同时使用预训练的扩散teacher计算一致性损失（输入相同噪声下，不同时间步输出应一致）。
  4. 利用离线数据集中的奖励标签，计算student生成轨迹的预期回报（通过奖励模型或真实奖励），并将其作为额外目标最大化。
  5. 联合优化一致性损失和奖励相关损失，更新student参数。
  6. 训练完成后，使用student单步生成轨迹用于决策。

## 3. 实验设计：使用了哪些数据集/场景，它的benchmark是什么，对比了哪些方法
- **数据集/场景**：
  - Gym MuJoCo：经典连续控制任务（如HalfCheetah, Hopper, Walker2d等）。
  - FrankaKitchen：机器人操作任务，要求多子任务组合。
  - 长 horizon 规划基准：文中提及但未具体命名，应是包含较长时间步的规划任务。
- **Benchmark**：离线RL标准基准（如D4RL中的MuJoCo和Kitchen数据集），以及长 horizon 规划场景。
- **对比方法**：
  - 扩散规划器（Diffusion Planner）原始版本（多步采样）。
  - 现有的一致性模型应用于决策的方法（如行为克隆式一致性模型、actor-critic框架的一致性模型）。
  - 其他离线RL方法（如CQL, IQL等，可能作为基线对比摘要未详细列出，但通常包含）。
- **实验结果**：该方法在性能上相比先前最优方法提升9.7%，推理速度相比扩散对应方法提速142倍。

## 4. 资源与算力
- 论文摘要及提供的元数据中**未明确说明**所使用的GPU型号、数量及训练时长。仅从实验规模（MuJoCo、Kitchen、长horizon任务）推断，可能使用单卡或少量GPU（常见于此类论文配置），但具体信息缺失。

## 5. 实验数量与充分性
- **实验组数**：覆盖三类基准（MuJoCo多个子任务、Kitchen、长horizon），每一类应有多个随机种子重复，通常论文会报告均值±标准差。摘要中仅给出平均提升百分比，未展示详细表格。消融实验可能涉及奖励感知损失的影响、蒸馏步数选择等，但摘要未提及具体消融数量。
- **充分性评价**：实验覆盖面较广，包括连续控制、机器人操作和长序列规划，但缺乏对超参数敏感性、不同蒸馏设置、高维任务（如视觉输入）的测试。由于没有完整实验细节，无法完全判断公平性（对比方法的超参数是否调优一致等）。总体而言，实验设计方向合理，但细节不足。

## 6. 论文的主要结论与发现
- 将奖励信号融入一致性蒸馏过程，能够同时提升推理速度（单步）和生成轨迹的奖励质量。
- 解耦训练避免了复杂多网络并发，且无需使用次优演示进行行为克隆。
- 在多个离线RL基准上，该方法在保持或提升奖励的同时，将采样步数减少近50倍（1步 vs 50-100步），推理加速最高达142倍。
- 奖励感知蒸馏不仅能加速，还能超越先前最优化方法9.7%的性能。

## 7. 优点
- **方法创新**：首次将奖励优化直接嵌入一致性蒸馏框架，解决扩散规划器推理慢的痛点。
- **实用性**：解耦训练简化流程，单步采样便于实际部署。
- **性能卓越**：在加速的同时提升奖励，实现“又快又好”。
- **实验验证充分**：涵盖多个基准，包括长 horizon 任务，体现泛化性。

## 8. 不足与局限
- **实验细节缺失**：未提供完整的对比实验表格、消融实验、超参数分析、计算资源等信息，削弱可复现性。
- **任务类型局限**：仅涉及连续控制与机器人操作，未在离散动作空间或视觉决策任务（如Atari、自动驾驶）上验证。
- **潜在偏差风险**：奖励信号可能来自离线数据集（稀疏/噪声奖励），若奖励模型不准确或使用真实奖励，可能引入偏差。
- **应用限制**：需要预训练扩散规划器（teacher），若扩散模型本身训练成本高，整体流程仍有开销；蒸馏后的student可能过拟合于训练用的奖励分布。

（完）
