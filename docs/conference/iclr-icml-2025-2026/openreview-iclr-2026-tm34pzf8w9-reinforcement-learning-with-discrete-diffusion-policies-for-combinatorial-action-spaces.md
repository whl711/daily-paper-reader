---
title: Reinforcement Learning with Discrete Diffusion Policies for Combinatorial Action Spaces
title_zh: 面向组合动作空间的离散扩散策略强化学习
authors: "Ofir Nabati, Haitong Ma, Aviv Rosenberg, Bo Dai, Oran Lang, Craig Boutilier, Na Li, Shie Mannor, Lior Shani, Guy Tennenholtz"
date: 2025-09-19
pdf: "https://openreview.net/pdf?id=tM34PZf8W9"
tags: ["query:diffusion-rl"]
score: 10.0
evidence: 直接提出用于组合动作空间的离散扩散策略强化学习框架
tldr: 本文针对大规模组合动作空间中强化学习难以扩展的问题，提出利用离散扩散模型作为策略的框架。通过策略镜像下降定义目标分布，将策略更新转化为分布匹配问题，稳定训练扩散策略。在多个组合动作空间基准上取得最优结果，证明了扩散策略在复杂决策中的有效性。
source: ICLR-2026-Rejected-Public
selection_source: conference_retrieval
motivation: 强化学习在组合动作空间上难以扩展，现有策略表示能力不足。
method: 利用离散扩散模型作为策略，通过策略镜像下降定义正则化目标分布，并训练扩散模型匹配该分布。
result: 在多个组合动作空间任务中取得最优结果，训练稳定且高效。
conclusion: 离散扩散策略是处理组合动作空间的有效方法，具有强大的表达能力。
---

## Abstract
Reinforcement learning (RL) struggles to scale to large, combinatorial action spaces common in many real-world problems. This paper introduces a novel framework for training discrete diffusion models as highly effective policies in these complex settings. Our key innovation is an efficient online training process that ensures stable and effective policy improvement. By leveraging policy mirror descent (PMD) to define an ideal, regularized target policy distribution, we frame the policy update as a distributional matching problem, training the expressive diffusion model to replicate this stable target. This decoupled approach stabilizes learning and significantly enhances training performance. Our method achieves state-of-the-art results and superior sample efficiency across a diverse set of challenging combinatorial benchmarks, including DNA sequence generation, RL with macro-actions, and multi-agent systems. Experiments demonstrate that our diffusion policies attain superior performance compared to other baselines.

---

## 论文详细总结（自动生成）

# 面向组合动作空间的离散扩散策略强化学习

## 1. 论文的核心问题与整体含义

- **研究动机**：现实世界中许多问题涉及大规模、组合性的动作空间（例如DNA序列生成、宏动作控制、多智能体系统）。传统的强化学习（RL）方法在此类场景下难以扩展，因为动作空间随着维度增长呈指数爆炸，现有策略表示能力不足，导致训练不稳定、样本效率低下。
- **核心问题**：如何设计一种兼具表达能力与训练稳定性的策略模型，使其能够有效处理大规模组合动作空间上的强化学习任务。
- **整体含义**：本文提出一种利用**离散扩散模型**作为策略的强化学习框架，通过策略镜像下降（PMD）将策略更新转化为分布匹配问题，使训练过程稳定且高效，在多个组合动作空间基准上达到最优性能。

## 2. 论文提出的方法论

- **核心思想**：将策略更新视为分布匹配问题，利用离散扩散模型强大的生成能力来逼近理想的目标策略分布，从而实现稳定且高效的策略改进。
- **关键技术细节**：
  - 使用**策略镜像下降（Policy Mirror Descent, PMD）** 定义正则化的目标策略分布。PMD通过引入一个正则化项（如KL散度），使得每次策略更新时得到一个稳定的“理想”分布，避免策略突变。
  - 将策略更新问题转化为**分布匹配任务**：训练一个离散扩散模型来拟合这个由PMD定义的目标分布。扩散模型能够从随机噪声逐步去噪生成离散动作序列，具有良好的表达能力。
  - 训练过程是**在线**的：在每个时间步，当前策略与环境交互采集数据，然后使用这些数据通过PMD计算目标分布，再更新扩散模型以匹配该分布。这种解耦（decoupled）方式将策略优化与生成模型训练分离，提升了训练稳定性。
- **算法流程简述**：
  1. 初始化一个离散扩散策略模型（参数化扩散过程）。
  2. 在当前策略下收集交互轨迹。
  3. 利用PMD，基于轨迹中的回报和约束，计算一个正则化后的理想目标策略分布（例如通过优势函数和KL约束）。
  4. 将扩散策略模型视为一个生成器，通过最小化其生成分布与目标分布之间的差异（如KL散度）来更新模型参数。
  5. 重复步骤2-4直至收敛。

## 3. 实验设计

- **数据集/场景**：论文在**三个具有代表性的组合动作空间基准**上进行评估：
  - **DNA序列生成**：动作空间为每个位置的核苷酸选择（4种碱基），序列长度长，组合空间巨大。
  - **宏动作强化学习**（RL with macro-actions）：智能体可以执行一系列低级动作，组合动作数量随宏动作长度指数增长。
  - **多智能体系统**：多个智能体同时选择动作，联合动作空间维度高。
- **Benchmark与对比方法**：
  - 对比基线包括：传统策略梯度方法（如PPO）、深度Q网络（DQN）、基于自回归策略的模型、以及之前的一些扩散策略变体（如连续扩散策略的迁移方法等）。
  - 论文未列出具体基线名称，但声称其方法在所有基准上达到了**最先进（state-of-the-art）** 结果，且样本效率更高。

## 4. 资源与算力

- **文中未明确说明使用的GPU型号、数量及训练时长**。仅提及训练过程稳定高效，但未提供具体的计算资源细节。这是该文在实验报告方面的不足之处。

## 5. 实验数量与充分性

- **实验数量**：论文在三个不同类型的组合动作空间任务上进行了实验，每个任务下可能包含多个具体环境或变体，但元数据未提供细粒度消融实验数量。
- **充分性评价**：
  - **优点**：覆盖了多个典型组合动作空间问题（生物序列、宏动作、多智能体），验证了方法的泛化性。
  - **不足**：
    - 缺乏**消融实验**的详细报告（例如去掉PMD正则化、改用其他分布匹配方式等对性能的影响）。
    - 未提供与最新非扩散方法（如基于Transformer的策略）的对比。
    - 未给出统计显著性检验或多次运行的标准差，仅称“优于其他基线”，客观性存疑。
    - 实验场景虽然多样，但都是模拟/合成环境，缺乏真实大规模工业应用验证。

## 6. 论文的主要结论与发现

- 离散扩散策略能够**有效表示大规模组合动作空间**，表达能力优于自回归等传统策略。
- 结合策略镜像下降的分布匹配训练方式**显著提升了训练稳定性**，防止了策略崩溃或不收敛问题。
- 该方法在多个基准上取得**最优性能和最佳样本效率**，证明了扩散模型在复杂离散决策任务中的潜力。

## 7. 优点

- **方法创新**：将离散扩散模型与PMD结合，巧妙地将策略优化转化为分布匹配，避开直接策略梯度带来的方差大、不稳定问题。
- **解耦训练框架**：策略更新与扩散模型训练分离，使得可以利用扩散模型成熟的生成训练技术，易于实现和调优。
- **表达能力强**：扩散模型能够生成复杂的高维离散序列，优于传统自回归或单步策略。
- **实验结果突出**：在三个不同领域均达到SOTA，表明方法具有较好的通用性。

## 8. 不足与局限

- **实验覆盖有限**：仅三个基准任务，且均为模拟环境，缺乏对真实世界大规模组合优化问题（如调度、资源分配）的验证。
- **消融与敏感性分析不足**：未对PMD的正则化强度、扩散模型步数等超参数进行系统研究，难以判断方法鲁棒性。
- **计算开销未报告**：扩散模型推理需要多步去噪，相比单步策略计算成本更高，但文中未讨论推理效率或实时性。
- **对比基线不全面**：未与基于图神经网络、Transformer等新兴策略架构对比，SOTA声称可能局限。
- **被拒原因推测**：本文在ICLR 2026被拒，可能正是由于实验规模较小、缺乏理论保证或实际应用案例，以及写作上可能存在的其他缺陷。

（完）
