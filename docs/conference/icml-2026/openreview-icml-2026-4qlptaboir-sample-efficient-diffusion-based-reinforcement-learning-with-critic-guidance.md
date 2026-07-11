---
title: Sample-Efficient Diffusion-based Reinforcement Learning with Critic Guidance
title_zh: 基于评论家引导的样本高效扩散强化学习
authors: "Shutong Ding, Zejia Zhong, Zhongyi Wang, Ke Hu, Bikang Pan, Jingya Wang, Ye Shi"
date: 2026-04-30
pdf: "https://openreview.net/pdf/36538634fe5cce2d06bdee3399aff8f7e173d04e.pdf"
tags: ["query:diffusion-rl"]
score: 10.0
evidence: 基于评论家引导的样本高效扩散强化学习
tldr: 该文针对扩散策略强化学习中采样型方法探索好但利用不足、梯度型方法利用好但坍缩为单峰的问题，提出评论家引导的扩散策略优化（CGPO）。该框架同时结合采样和梯度优势，实现高效探索与价值利用。在多个机器人控制任务上，CGPO显著提升了样本效率和最终性能。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 现有扩散强化学习方法在探索-利用平衡上存在缺陷，导致收敛慢或多样性丢失。
method: 提出评论家引导的扩散策略优化，融合采样和梯度信号进行策略更新。
result: 在多个MuJoCo和机器人任务上，CGPO在样本效率和收敛性能上均超越基线。
conclusion: 评论家引导有效平衡了扩散策略的探索与利用，实现样本高效的强化学习。
---

## Abstract
Recent advances in reinforcement learning (RL) have achieved great successes by leveraging the multimodality and exploration capability of diffusion policies. Among these approaches, one representative branch focuses on the sampling-based policy optimization. This design enables better exploration capability of the diffusion model, particularly at the beginning of training, but suffer from low exploitation in Q-value information, resulting in a slow policy convergence. Another branch pays attention to gradient-based policy optimization, which sufficiently exploits the gradient of the Q function yet tends to collapse into a unimodal policy with low diversity. To address this issue, we propose CGPO, \textbf{C}ritic-\textbf{G}uided diffusion \textbf{P}olicy \textbf{O}ptimization, which effectively balances exploration and exploitation with the training-free guidance technique integrated into the denoising process of diffusion policy. Concretely, CGPO steers action generation toward high-value regions defined by the critic network and uses the guided actions as regression objectives. In this manner, CGPO reduces the time required to obtain high-quality actions and improves final performance with better balance between the exploration-exploitation tradeoff. We validate the effectiveness of CGPO on 5 MuJoCo locomotion tasks, and CGPO achieves state-of-the-art performance compared with existing diffusion-based RL methods. Notably, CGPO is the first success to incorporate diffusion policy into real-world RL, with its superior performance on Franka robot arm grasping tasks. Our official page is released at \url{https://dingsht.tech/cgpo-webpage}.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 核心问题与整体含义（研究动机和背景）

扩散模型因其多模态表达能力和强探索性，近期在强化学习（RL）中取得了显著成功。然而，现有扩散策略方法在 **探索-利用平衡** 上存在两类典型缺陷：

- **采样型方法**（如扩散策略直接采样动作）：前期探索能力强，但对 Q 值信息利用不足，导致策略收敛缓慢。
- **梯度型方法**（如对 Q 函数求梯度优化策略）：能充分利用价值梯度，但容易坍缩为单峰策略，丧失多样性。

因此，本文旨在设计一种能同时兼顾扩散策略的探索能力与价值函数利用效率的方法，实现样本高效的强化学习。

## 2. 方法论：核心思想、关键技术细节

### 核心思想
提出 **评论家引导的扩散策略优化（CGPO, Critic-Guided Diffusion Policy Optimization）**，在扩散策略的去噪过程中引入 **训练无关的引导（training-free guidance）** 技术，将评论家（Critic）网络的 Q 值信息作为引导信号，使动作生成偏向高价值区域，并将引导后的动作作为回归目标更新策略。

### 关键技术细节
- **引导机制**：在扩散模型反向去噪的每一步，根据评论家网络（通常是 Q 函数）对当前噪声动作的梯度或打分（具体公式未在摘要展开），调整采样方向，使生成的动作更接近高 Q 值区域。
- **训练方式**：将引导后的动作视为“专家演示”，用作策略回归的监督信号，从而在不额外收集数据的情况下提升样本效率。
- **平衡策略**：通过调节引导强度（guidance scale）控制探索与利用的权衡，避免过度引导导致多样性丧失。

### 算法流程（文字描述）
1. 使用扩散策略模型表示策略 \( \pi(a|s) \)。
2. 在采样阶段：对于给定状态 \( s \)，从随机噪声开始迭代去噪。
3. 每次去噪步骤中，使用评论家网络提供的 Q 值梯度或得分调整预测的噪声。
4. 最终生成引导后的动作 \( a_{\text{guided}} \)。
5. 将 \( a_{\text{guided}} \) 作为优化目标，通过最小化扩散模型的负对数似然或均方误差来更新策略参数。
6. 重复上述过程，交替更新评论家和策略（类似标准 actor-critic 框架）。

> 注：论文摘要未给出完整公式，但说明该方法为“training-free guidance”，暗示引导过程不需要额外训练辅助网络。

## 3. 实验设计：数据集 / 场景、基准、对比方法

- **场景**：
  - 5 个 MuJoCo 机器人运动控制任务（具体名称未在摘要列出，通常包含 Hopper、Walker2d、HalfCheetah、Ant、Humanoid 等）。
  - 真实世界的 Franka 机器人臂抓取任务。
- **基准**：与现有基于扩散的强化学习方法进行比较（如 Diffusion-QL、DIPO、EDP 等扩散RL方法，以及可能的 SAC、TD3 等非扩散基线）。
- **对比方法**：摘要提到“state-of-the-art performance compared with existing diffusion-based RL methods”，但未列出具体方法名称。
- **评估指标**：样本效率（收敛所需交互步数）、最终性能（平均累积回报）、策略多样性等（推测论文正文会有相关消融）。

## 4. 资源与算力

论文摘要及提供的元数据中 **未明确说明** 使用的 GPU 型号、数量、训练时长等算力信息。在正文完整的论文中可能有相关描述，但此摘要未提供。通常这类工作使用单块或数块 GPU（如 A100 或 RTX 3090）进行实验。

## 5. 实验数量与充分性

- **数量**：在 5 个 MuJoCo 任务和 1 个真实机器人任务上进行了评估，消融实验（如引导强度的影响、是否添加引导等）在正文中应有体现（摘要未列具体数量）。
- **充分性**：实验覆盖了从仿真到真实任务的迁移，验证了方法的泛化性。但 MuJoCo 任务数量（5个）在机器人 RL 论文中属于中等偏少，且缺少更多复杂任务（如 Adroit、Kitchen 等）或高维图像输入任务。真实世界实验仅有一个 Franka 抓取场景，外部有效性有待扩展。
- **客观性与公平性**：对比方法应为同等实验条件下的相同种子，但摘要未说明是否进行了多随机种子的统计显著性检验。通常 ICML 论文会报告均值和标准差。

## 6. 主要结论与发现

1. CGPO 在 5 个 MuJoCo 任务上取得了优于现有扩散 RL 方法的样本效率和最终性能。
2. 首次将扩散策略成功应用于真实机器人（Franka 臂抓取）的强化学习，验证了方法的实际可行性。
3. 评论家引导有效平衡了探索与利用：既保持了扩散策略的早期探索优势，又通过价值信号加速收敛，避免了梯度型方法的多样性崩溃。

## 7. 优点：方法或实验设计上的亮点

- **方法创新**：巧妙地将“训练无关的引导”引入扩散策略 RL 中，无需额外训练一个引导模型，节约计算成本且简单易用。
- **探索-利用权衡**：通过可调节的引导强度精细控制，解决了采样型和梯度型方法的各自缺陷。
- **真实世界验证**：在真实物理机器人任务上证明了方法可行性，这在扩散 RL 中较为少见，增加了工作实用价值。
- **样本高效**：对数据效率的显著提升，有利于减少在线交互成本。

## 8. 不足与局限

- **实验覆盖有限**：仅测试了 MuJoCo 的低维连续控制任务，未在更复杂的高维观测（如视觉输入、稀疏奖励、多智能体等）场景下验证。
- **理论分析缺失**：摘要未提及对引导机制的理论保证（如收敛性、最优性），仅凭实验效果论证。
- **引导强度调参**：引导强度可能对性能敏感，需要手动调节，缺乏自适应机制。
- **真实机器人实验细节**：未说明真实任务中的训练步数、成功率等量化指标，仅称“superior performance”，说服力稍弱。
- **算力资源未披露**：不利于复现和评估计算开销。

（完）
