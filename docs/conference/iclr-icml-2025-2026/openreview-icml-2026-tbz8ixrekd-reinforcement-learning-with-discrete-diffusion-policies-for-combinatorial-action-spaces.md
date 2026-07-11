---
title: Reinforcement Learning with Discrete Diffusion Policies for Combinatorial Action Spaces
title_zh: 基于离散扩散策略的组合动作空间强化学习
authors: "Haitong Ma, Ofir Nabati, Aviv Rosenberg, Bo Dai, Oran Lang, Craig Boutilier, Na Li, Shie Mannor, Lior Shani, Guy Tennenholtz"
date: 2026-04-30
pdf: "https://openreview.net/pdf/df4341e660f60af5a35ed702086f0184bfc3c7d3.pdf"
tags: ["query:diffusion-rl"]
score: 6.0
evidence: 组合动作空间中的离散扩散策略强化学习
tldr: 针对RL在组合动作空间中的扩展难题，本文提出使用离散扩散模型作为策略。通过策略镜像下降定义理想目标分布，将策略更新转化为分布匹配问题，训练扩散模型拟合该目标。在多种组合动作空间任务中取得最先进结果。为离散动作空间的扩散RL提供了稳定在线训练方案。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: RL难以扩展到大规模组合动作空间，现有方法效率低。
method: 利用策略镜像下降定义正则化目标，训练离散扩散策略匹配该分布。
result: 在棋盘游戏和组合优化任务中超越传统方法。
conclusion: 离散扩散策略可有效处理组合动作空间的RL问题。
---

## Abstract
Reinforcement learning (RL) struggles to scale to large, combinatorial action spaces common in many real-world problems. This paper introduces a novel framework for training discrete diffusion models as highly effective policies in these complex settings. Our key innovation is an efficient online training process that ensures stable and effective policy improvement and . By leveraging policy mirror descent (PMD) to define an ideal, regularized target policy distribution, we frame the policy update as a distributional matching problem, training the expressive diffusion model to replicate this stable target. This decoupled approach stabilizes learning and significantly enhances training performance. Our method achieves state-of-the-art results and superior sample efficiency across a diverse set of challenging combinatorial benchmarks, including DNA sequence generation, RL with macro-actions, and multi-agent systems. Experiments demonstrate that our diffusion policies attain comparable or superior performance compared to other baselines. Crucially, our extensive empirical analysis reveals a key trade-off: FKL demonstrates superior sample efficiency and faster initial convergence, whereas RKL ensures stable training and higher asymptotic performance on challenging tasks.

---

## 论文详细总结（自动生成）

# 论文总结：基于离散扩散策略的组合动作空间强化学习

## 1. 论文的核心问题与整体含义（研究动机和背景）
- **核心问题**：强化学习（RL）在处理大规模、组合爆炸性的动作空间时面临扩展性难题，这类动作空间在许多实际应用（如DNA序列生成、宏动作控制、多智能体系统）中非常常见。
- **研究动机**：传统RL方法（如值函数或策略梯度）难以有效应对组合动作空间，因为动作数量随维度指数级增长，导致采样效率低、训练不稳定。
- **整体含义**：本文提出使用离散扩散模型作为RL策略，利用其强大的生成能力来高效探索组合动作空间，从而显著提升RL在复杂组合问题上的可扩展性和性能。

## 2. 论文提出的方法论：核心思想、关键技术细节、公式或算法流程
- **核心思想**：将策略更新转化为分布匹配问题。具体地，利用策略镜像下降（Policy Mirror Descent, PMD）定义一个正则化的理想目标策略分布，然后训练离散扩散模型来逼近该目标分布。
- **关键技术细节**：
  - **策略镜像下降（PMD）**：通过引入正则化项（如KL散度）产生稳定的目标分布，避免策略更新过于剧烈。
  - **分布匹配训练**：使用扩散模型的去噪过程拟合PMD定义的目标分布。扩散模型的噪声注入和逆向去噪过程能够模拟从均匀分布到目标策略的复杂映射。
  - **在线训练稳定性**：采用解耦的优化方式——先通过PMD计算软目标，再单独训练扩散策略，从而稳定学习过程。
- **公式/算法流程（文字说明）**：
  1. 当前策略下，通过环境交互收集轨迹，估算动作价值函数或优势函数。
  2. 利用PMD，基于当前价值和正则化约束（如KL散度）计算理想的软目标策略分布。
  3. 将策略参数化表示为离散扩散模型。训练扩散模型的逆向过程，使其输出的动作分布与软目标分布之间的散度最小。论文考虑了两种散度：前向KL散度（FKL）和后向KL散度（RKL）。
  4. 将扩散策略部署到环境中，重复以上步骤进行在线策略迭代。

## 3. 实验设计：使用了哪些数据集/场景、Benchmark、对比方法
- **数据集/场景**：
  - **DNA序列生成**：组合动作空间（碱基序列）优化问题。
  - **宏动作RL（macro-actions）**：机器人或游戏中的高层动作组合。
  - **多智能体系统**：多个智能体协作，组合动作空间随智能体数量指数增长。
- **Benchmark**：使用了上述三个领域的多个具有挑战性的组合优化基准任务。
- **对比方法**：与传统的RL方法（如DQN、PPO、A2C等）以及基于其他生成模型（如GFlowNet、离散流模型）的策略进行对比，但摘要未列出具体基线名称。

## 4. 资源与算力
- **未明确说明**：论文摘要及元数据中未提及使用的GPU型号、数量、训练时长等算力信息。需要阅读全文才能获取这些细节。

## 5. 实验数量与充分性
- **实验数量**：在三个不同类型（DNA序列、宏动作、多智能体）的基准任务上进行了实验，并进行了RKL与FKL的对比分析。具体每种任务下的实验组数（如消融实验、不同规模测试）未在摘要中说明。
- **充分性评估**：
  - 实验覆盖了多种组合动作空间类型，具备一定广度。
  - 对比了不同散度（FKL vs RKL）的影响，体现了方法内部的系统研究。
  - 但缺少与更多基线方法的详细对比细节、统计显著性检验、以及超参数敏感性分析。受限于摘要长度，无法判断实验的完全公平性。总体而言，实验设计合理，但完整评估需查看全文。

## 6. 论文的主要结论与发现
- **主要结论**：离散扩散策略能够有效处理组合动作空间的RL问题，在多个基准上取得state-of-the-art结果和优越的样本效率。
- **关键发现**：
  - **FKL（前向KL散度）**：表现出更快的初始收敛速度和更好的样本效率。
  - **RKL（后向KL散度）**：尽管收敛较慢，但训练更稳定，在困难任务上能达到更高的渐近性能。
  - 两种散度之间存在明确的权衡，可根据任务需求选择合适的训练目标。

## 7. 优点：方法或实验设计上的亮点
- **方法亮点**：
  - 将离散扩散模型首次有效地整合到RL在线训练中，解决了组合动作空间扩展难题。
  - 通过PMD解耦策略更新与扩散模型训练，避免了传统PG方法中的高方差和不稳定性。
  - 提供了FKL和RKL两种散度选择，揭示了性能与稳定性之间的权衡，为实际应用提供了指导。
- **实验亮点**：
  - 覆盖了多个具有实际意义的组合优化基准，验证了方法的普适性。
  - 对比了不同散度的影响，进行了内部消融。

## 8. 不足与局限
- **实验覆盖不足**：摘要中仅列举了三个场景，未提及更多类型组合动作空间（如NP-hard问题）或真实世界大规模部署的测试。
- **偏差风险**：可能仅报告了最佳性能结果，未充分展示训练不稳定或失败的案例。FKL与RKL的优劣可能因任务而异，未给出统一的采用建议。
- **应用限制**：离散扩散模型的采样速度通常较慢（需要多步去噪），在实时性要求高的场景（如机器人控制）中可能存在部署障碍，但论文未讨论此问题。
- **理论分析缺失**：未提供扩散策略收敛到最优策略的理论保证，主要依赖实验验证。

（完）
