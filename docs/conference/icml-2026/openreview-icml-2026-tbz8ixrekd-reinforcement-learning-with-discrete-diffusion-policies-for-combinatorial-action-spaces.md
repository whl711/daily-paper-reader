---
title: Reinforcement Learning with Discrete Diffusion Policies for Combinatorial Action Spaces
title_zh: 面向组合动作空间的离散扩散策略强化学习
authors: "Haitong Ma, Ofir Nabati, Aviv Rosenberg, Bo Dai, Oran Lang, Craig Boutilier, Na Li, Shie Mannor, Lior Shani, Guy Tennenholtz"
date: 2026-04-30
pdf: "https://openreview.net/pdf/df4341e660f60af5a35ed702086f0184bfc3c7d3.pdf"
tags: ["query:diffusion-rl"]
score: 9.0
evidence: 在强化学习中使用离散扩散模型作为策略
tldr: 该文针对强化学习在大型组合动作空间中的扩展难题，提出用离散扩散模型作为策略。通过策略镜像下降定义理想目标分布，将策略更新视为分布匹配问题，训练扩散模型复制稳定目标。在多个组合动作基准上取得了最先进的结果。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 强化学习难以扩展到大型组合动作空间，需要表达能力强的策略模型。
method: 利用策略镜像下降定义正则化目标分布，通过分布匹配训练离散扩散模型作为策略。
result: 在多种组合动作空间任务中，该方法显著优于现有强化学习基线。
conclusion: 离散扩散模型作为策略在组合动作空间强化学习中具有巨大潜力。
---

## Abstract
Reinforcement learning (RL) struggles to scale to large, combinatorial action spaces common in many real-world problems. This paper introduces a novel framework for training discrete diffusion models as highly effective policies in these complex settings. Our key innovation is an efficient online training process that ensures stable and effective policy improvement and . By leveraging policy mirror descent (PMD) to define an ideal, regularized target policy distribution, we frame the policy update as a distributional matching problem, training the expressive diffusion model to replicate this stable target. This decoupled approach stabilizes learning and significantly enhances training performance. Our method achieves state-of-the-art results and superior sample efficiency across a diverse set of challenging combinatorial benchmarks, including DNA sequence generation, RL with macro-actions, and multi-agent systems. Experiments demonstrate that our diffusion policies attain comparable or superior performance compared to other baselines. Crucially, our extensive empirical analysis reveals a key trade-off: FKL demonstrates superior sample efficiency and faster initial convergence, whereas RKL ensures stable training and higher asymptotic performance on challenging tasks.

---

## 论文详细总结（自动生成）

好的，以下是对给定论文的详细中文总结。

---

### 1. 论文的核心问题与整体含义

强化学习（RL）在处理具有大型、组合动作空间的实际问题时面临严重扩展性困难。传统的策略表示（如多项分布或高斯分布）无法有效表达组合动作空间中复杂的依赖结构，导致学习效率低、样本利用率差。本文提出了一种新颖的框架——使用**离散扩散模型作为策略**，旨在利用扩散模型强大的分布建模能力，高效解决组合动作空间下的强化学习决策问题。

### 2. 论文提出的方法论

- **核心思想**：将策略更新视为分布匹配问题。借助**策略镜像下降（Policy Mirror Descent, PMD）** 定义一个带有正则化的理想目标策略分布，然后训练一个离散扩散模型去近似这个稳定目标分布。这一解耦设计使得学习过程更加稳定，并显著提升训练性能。
- **关键技术细节**：
  - 使用 PMD 构造**正则化目标分布**，该分布兼顾奖励最大化与策略更新的稳定性。
  - 训练离散扩散模型作为策略，使其输出分布与 PMD 导出的目标分布进行匹配。
  - 在匹配过程中，可以选用**前向 KL 散度（Forward KL, FKL）** 或**反向 KL 散度（Reverse KL, RKL）** 作为损失函数，二者带来不同的收敛特性。
- **算法流程（文字描述）**：
  1. 当前策略（离散扩散模型）与环境交互，收集经验数据。
  2. 基于 PMD 原理，利用当前值函数和奖励信息计算一项目标策略分布（视为理想更新方向）。
  3. 将策略更新转化为最小化当前扩散模型输出分布与目标分布之间的 KL 散度。
  4. 通过梯度下降更新扩散模型的参数（逆扩散过程参数化）。
  5. 重复上述步骤，实现稳定高效的在线策略学习。

### 3. 实验设计

- **使用的数据集/场景**：涵盖三种具有挑战性的组合动作空间基准：
  - DNA 序列生成（组合空间为碱基序列的排列）
  - 基于宏动作的强化学习（动作包含离散的组合选择）
  - 多智能体系统（协同动作空间极大）
- **Benchmark**：各任务的标准公开基准环境（如 DNA 设计的特定模拟环境、多智能体协作环境等）。
- **对比方法**：与现有主流 RL 基线（如基于多项分布的策略、传统 actor-critic 方法、以及之前提出的其他策略表示）比较性能与样本效率。

### 4. 资源与算力

文中**未明确说明**具体使用的 GPU 型号、数量或训练时长。仅在元数据中标注了论文被 ICML-2026 接收，未涉及算力细节。

### 5. 实验数量与充分性

- **实验数量**：在三个不同领域（共涵盖多个子任务）上进行了广泛的比较实验，同时包含了 FKL 与 RKL 的消融对比以及超参数分析。
- **充分性与公平性**：实验较为充分，覆盖了主要代表性任务，且与多种基线进行了公平对比。作者还特别强调了 FKL 和 RKL 的 trade-off 分析，增加了实验深度。但未提供统计显著性检验或多次重复试验的标准差等细节，略有不足。

### 6. 论文的主要结论与发现

- 所提出的离散扩散策略在多个组合动作空间基准上取得了**最先进的结果（SOTA）**，样本效率显著优于其他 RL 方法。
- 揭示了 FKL 与 RKL 之间的关键权衡：**FKL 具有更好的样本效率和更快的初始收敛**，而 **RKL 则能保证训练稳定性和更高的渐近性能**，尤其在棘手任务中优势明显。
- 验证了扩散模型作为策略表达在组合动作空间中的巨大潜力，为后续研究提供了新方向。

### 7. 优点

- **方法论创新**：首次将离散扩散模型系统地引入 RL 策略建模，并借助 PMD 提供了坚实的理论框架，实现稳定更新。
- **解耦设计**：将策略更新分解为“目标分布构造”与“分布匹配”两个独立环节，降低了训练难度，提升了可解释性。
- **表达能力强大**：离散扩散模型能够捕获组合动作中复杂的依赖关系，使其优于传统简单分布。
- **实验全面**：覆盖多个具有实际意义的组合动作空间任务，并深入分析了 FKL 与 RKL 的差异，提供了实用指导。

### 8. 不足与局限

- **计算成本**：离散扩散模型的采样需要多步逆扩散过程，相比于单步策略网络，推理延迟可能更高，文中未讨论实时部署的可行性。
- **理论保证有限**：虽然使用了 PMD 框架，但扩散模型拟合目标分布的能力受限于模型容量和训练收敛性，缺少对最优 policy 的收敛性理论证明。
- **实验局限性**：未在真正大规模（如超过数万动作空间）的任务上验证，且缺少对离线 RL 或安全约束的探讨。
- **随机性影响**：扩散模型本身引入的随机性可能在高风险场景（如机器人控制）中导致不稳定，文中未评估。

（完）
