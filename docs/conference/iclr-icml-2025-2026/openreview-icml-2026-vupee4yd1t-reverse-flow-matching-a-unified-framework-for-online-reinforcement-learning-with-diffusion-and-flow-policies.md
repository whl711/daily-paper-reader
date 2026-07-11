---
title: "Reverse Flow Matching: A Unified Framework for Online Reinforcement Learning with Diffusion and Flow Policies"
title_zh: 反向流匹配：扩散与流策略在线强化学习的统一框架
authors: "Zeyang Li, Sunbochen Tang, Navid Azizan"
date: 2026-04-30
pdf: "https://openreview.net/pdf/74b510cb0fa1b7fd689479c5222e15002c2fcbb7.pdf"
tags: ["query:diffusion-rl"]
score: 9.0
evidence: 统一框架提升扩散/流策略在线RL的样本效率
tldr: 针对扩散和流策略在线RL训练效率问题，提出反向流匹配统一框架，揭示了噪声期望族和梯度期望族方法之间的形式化关系，从而为设计更高效的训练目标提供理论基础，有望提升样本效率和可扩展性。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 扩散策略在线RL的两类方法（噪声期望和梯度期望）之间关系不明，限制了效率提升。
method: 提出反向流匹配统一框架，形式化连接两类方法并推导统一训练目标。
result: 理论分析建立联系，实验验证统一框架的有效性。
conclusion: 为扩散/流策略在线RL训练提供了统一的理论视角和方法基础。
---

## Abstract
Diffusion and flow policies are gaining prominence in online reinforcement learning (RL) due to their expressive power, yet training them efficiently remains a critical challenge. A fundamental difficulty that distinguishes online RL from standard generative modeling is the lack of direct samples from the target Boltzmann distribution defined by the Q-function. To address this, two seemingly distinct families of methods have been proposed for diffusion policies: a noise-expectation family, which uses a weighted average of noise as the training target, and a gradient-expectation family, which employs a weighted average of Q-function gradients. However, it remains unclear how these objectives are formally related, or whether they can be synthesized into a more general formulation. In this paper, we propose a unified framework, reverse flow matching (RFM), which rigorously addresses the problem of training diffusion and flow models without direct target samples. By adopting a reverse inferential perspective, we formulate the training target as a posterior mean estimation problem given an intermediate noisy sample. Crucially, we introduce Langevin Stein operators to construct zero-mean control variates, deriving a general class of estimators that share the same expectation. We show that existing noise-expectation and gradient-expectation methods are simply two specific instances within this broader class. This unified view yields two key advancements: it extends the capability of targeting Boltzmann distributions from diffusion to flow policies, and it enables the principled combination of Q-value and Q-gradient information to form an effective estimator, thereby improving training efficiency and stability. We instantiate RFM to train a flow policy in online RL and demonstrate improved performance on continuous-control benchmarks compared to diffusion policy baselines.

---

## 论文详细总结（自动生成）

## 论文详细中文总结

### 1. 核心问题与整体含义（研究动机和背景）

- **研究动机**：在在线强化学习（RL）中，扩散策略和流策略因其强大的表达能力而受到关注，但其高效训练面临根本性挑战——**无法直接获取由 Q 函数定义的 Boltzmann 目标分布的样本**，这与标准生成建模不同。
- **现有方法的局限**：针对扩散策略在线 RL 的训练，现有方法可归为两类：
  - **噪声期望族**：使用噪声的加权平均作为训练目标。
  - **梯度期望族**：使用 Q 函数梯度的加权平均作为训练目标。
  这两类方法的形式关系不明确，缺乏统一的理论框架，限制了训练效率的进一步提升。
- **整体含义**：论文旨在弥合两类方法之间的理论鸿沟，提供一个统一框架，从而提升扩散/流策略在在线 RL 中的训练效率、稳定性和可扩展性，并向流策略自然扩展。

### 2. 方法论：核心思想、关键技术细节、公式/算法

- **核心思想**：提出 **反向流匹配（Reverse Flow Matching, RFM）** 统一框架。通过采用**反向推断视角**，将训练目标形式化为**给定中间含噪样本的后验均值估计问题**。
- **关键技术细节**：
  - 引入 **Langevin Stein 算子** 构造零均值控制变量（zero-mean control variates），从而导出一类**期望相同的广义估计器族**。
  - 在该估计器族中，现有的**噪声期望方法**和**梯度期望方法**只是两个特定实例。
  - 统一框架带来两个关键进展：
    1. 将针对 Boltzmann 分布的训练能力从**扩散策略扩展至流策略**。
    2. 允许**原则性结合 Q 值和 Q 梯度信息**以形成更有效的估计器，进而提升训练效率和稳定性。
- **算法流程**（文字说明）：
  - 给定 Q 函数和当前策略，利用 Langevin Stein 算子构建控制变量。
  - 基于反向流匹配损失，在线学习阶段通过最小化估计器的方差来训练策略网络（流策略或扩散策略）。
  - 具体实施时，RFM 被实例化用于训练流策略，并使用加权组合的估计器进行梯度更新。

### 3. 实验设计

- **数据集/场景**：使用 **连续控制基准任务**（Continuous-Control Benchmarks），具体环境未在摘要中详列（推测为 MuJoCo 或类似标准连续控制环境）。
- **基准（Benchmark）**：与**扩散策略基线**（diffusion policy baselines）进行对比，未列出具体基线名称，但应包含常见的扩散策略在线 RL 方法（如 Diffusion Q-learning、EDP 等）。
- **对比方法**：噪声期望族方法、梯度期望族方法（作为框架的特殊情况被比较），以及现有扩散策略基线。

### 4. 资源与算力

- **文中未明确说明**使用的 GPU 型号、数量、训练时长等算力信息。仅能从 ICML 2026 接收论文推测其可能使用了标准实验配置（如单块或少量高端 GPU），但具体细节缺失。

### 5. 实验数量与充分性

- **实验组数**：摘要未提供具体消融实验或不同数据集的数量。从“demonstrated improved performance on continuous-control benchmarks compared to diffusion policy baselines”推断，至少包含多个连续控制任务（如 Hopper, Walker2d, HalfCheetah 等常见基准）以及对比实验。
- **充分性与公平性**：
  - 实验设计较为合理，对比了代表性基线。
  - 但**缺乏消融实验细节**（如不同估计器组合的贡献、噪声期望与梯度期望的单独效果等），因此从摘要看实验数量相对有限，不足以全面覆盖消融分析。
  - 未提及统计显著性和多次重复实验的设置，客观性有待详细论文补充。

### 6. 主要结论与发现

- 提出了反向流匹配（RFM）统一框架，**成功形式化联系了噪声期望和梯度期望两类方法**，证明它们是同一广义估计器族的特例。
- 该框架**将处理 Boltzmann 分布的能力从扩散策略扩展到流策略**，并允许组合 Q 值和 Q 梯度信息，从而提升训练效率与稳定性。
- 在连续控制基准上，使用 RFM 训练的流策略**性能优于扩散策略基线**，验证了框架的有效性。

### 7. 优点（方法或实验设计亮点）

- **理论创新**：首次从反向推断视角将在线 RL 中扩散/流策略训练统一为后验均值估计问题，并引入 Langevin Stein 算子构建控制变量，提供了坚实的理论基础。
- **统一性**：将两个看似不同的方法家族纳入同一框架，揭示了深层联系，有助于指导未来更高效的目标设计。
- **扩展性**：自然地将方法推广到流策略，拓宽了应用范围。
- **实用性**：原则性地融合 Q 值和 Q 梯度信息，比单纯使用其中一种更稳定、有效。

### 8. 不足与局限

- **实验覆盖有限**：摘要仅提及连续控制基准，未覆盖离散动作空间、多智能体或稀疏奖励等更复杂场景，泛化性待验证。
- **消融分析缺失**：未详细比较不同估计器组合（如单独使用噪音期望、单独使用梯度期望、加权组合）的贡献，以及 RFM 框架在不同超参数下的敏感性。
- **负迁移风险**：对于特定任务，结合 Q 值和 Q 梯度可能引入额外噪声，导致性能下降，文内未讨论此风险。
- **计算开销**：控制变量和 Langevin Stein 算子的计算可能增加每步复杂度，但未与基线进行效率对比。
- **公开资源细节不足**：无法复现实验，需作者提供开源代码和详细超参数。

（完）
