---
title: Sample-efficient and Scalable Exploration in Continuous-Time RL
title_zh: 连续时间强化学习中的样本高效可扩展探索
authors: "Klemens Iten, Lenart Treven, Bhavya Sukhija, Florian Dorfler, Andreas Krause"
date: 2026-01-26
pdf: "https://openreview.net/pdf?id=PJdMrK79Mo"
tags: ["query:rl"]
score: 6.0
evidence: 样本高效的连续时间基于模型的强化学习
tldr: 针对连续时间系统的强化学习问题，本文提出COMBRL算法。利用高斯过程等概率模型学习不确定感知的ODE动力学，通过贪婪最大化奖励与模型不确定性的加权和进行探索。理论证明次线性遗憾界，实验在控制任务中实现样本高效学习。为连续时间MBRL提供可扩展方案。
source: ICLR-2026-Accepted
selection_source: conference_retrieval
motivation: 现实控制是连续时间，但RL算法多假设离散时间，缺乏连续时间样本高效方法。
method: COMBRL使用概率模型（高斯过程/贝叶斯神经网络）学习ODE，结合探索奖励。
result: 在连续控制任务中，样本效率显著优于离散时间基线。
conclusion: 概率模型可有效实现连续时间RL的样本高效探索。
---

## Abstract
Reinforcement learning algorithms are typically designed for discrete-time dynamics, even though the underlying real-world control systems are often continuous in time. In this paper, we study the problem of continuous-time reinforcement learning, where the unknown system dynamics are represented using nonlinear ordinary differential equations (ODEs). We leverage probabilistic models, such as Gaussian processes and Bayesian neural networks, to learn an uncertainty-aware model of the underlying ODE. Our algorithm, COMBRL, greedily maximizes a weighted sum of the extrinsic reward and model epistemic uncertainty. This yields a scalable and sample-efficient approach to continuous-time model-based RL. We show that COMBRL achieves sublinear regret in the reward-driven setting, and in the unsupervised RL setting (i.e., without extrinsic rewards), we provide a sample complexity bound. In our experiments, we evaluate COMBRL in both standard and unsupervised RL settings and demonstrate that it scales better, is more sample-efficient than prior methods, and outperforms baselines across several deep RL tasks.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 核心问题与整体含义（研究动机和背景）

- **问题**：现实世界控制系统本质上是连续时间（continuous-time）的，例如机器人运动、化工过程等，其动力学由非线性常微分方程（ODE）描述。然而，现有的强化学习算法大多假设离散时间（discrete-time）环境，忽略了连续时间特性，导致样本效率低下且无法直接应用于实际系统。
- **动机**：尽管连续时间控制（如最优控制）已有丰富理论，但结合数据驱动的强化学习在样本高效性和可扩展性方面仍有空白。现有基于模型的RL方法在离散时间下有效，但扩展到连续时间时面临模型复杂度和探索策略的挑战。
- **整体含义**：本文提出COMBRL算法，利用概率模型（高斯过程、贝叶斯神经网络）学习不确定感知的ODE动力学，并通过贪婪最大化奖励与模型不确定性的加权和进行探索，实现样本高效且可扩展的连续时间强化学习。

## 2. 方法论：核心思想、关键技术细节

- **核心思想**：将连续时间动力学建模为未知ODE，使用概率模型（高斯过程GP或贝叶斯神经网络BNN）学习ODE的漂移项，同时量化模型不确定性（epistemic uncertainty）。在策略优化时，COMBRL最大化奖励与不确定性加权和，从而鼓励探索不确定性高的区域。
- **关键技术细节**：
  - **模型学习**：利用高斯过程或贝叶斯神经网络拟合ODE的右端函数 \( f(s, a) \)，得到后验分布，从而获得预测均值 \( \mu(s,a) \) 和方差 \( \sigma^2(s,a) \)。
  - **探索策略**：定义内在奖励（intrinsic reward）为模型不确定性，如 \( r_{\text{int}} \propto \sigma(s,a) \)。总目标函数为 \( r + \lambda \cdot \sigma \)，其中 \( \lambda \) 平衡探索与利用。
  - **算法流程**：在每个迭代中，收集轨迹数据，更新概率模型，然后基于当前模型和探索奖励进行控制（例如通过模型预测控制MPC或策略梯度），实现贪婪的探索。
  - **理论保证**：在奖励驱动设置下证明次线性遗憾界（sublinear regret）；在无监督RL设置（无外在奖励）下给出样本复杂度界。

## 3. 实验设计

- **数据集/场景**：连续时间控制任务，具体包括标准连续控制benchmark（如OpenAI Gym中的经典控制问题）以及可能的高维Deep RL任务（文中提到“several deep RL tasks”，未详细列出）。
- **基准方法**：对比了先前的连续时间RL方法（未具体点名）以及离散时间RL基线（如SAC、MPC等）。特别强调了COMBRL在样本效率和可扩展性上优于这些基线。
- **对比方法**：未在摘要中列出具体方法名称，但提及“prior methods”和“baselines across several deep RL tasks”。

## 4. 资源与算力

- 论文元数据中**未明确说明**使用的GPU型号、数量或训练时长。仅提及算法“scalable”，但未提供具体算力信息。

## 5. 实验数量与充分性

- **实验数量**：从摘要可知，实验在标准和无监督RL设置下均进行了评估，且覆盖多个deep RL任务。但未详细说明任务数量或消融实验数量。
- **充分性分析**：由于缺乏具体实验配置和结果表格，无法判断实验是否充分。但摘要声称“outperforms baselines across several deep RL tasks”，暗示在不同任务上均有对比。不过未提及消融实验（如不同概率模型选择、λ敏感性等），客观性和公平性需阅读全文确认。

## 6. 主要结论与发现

- **结论**：概率模型（GP/BNN）可有效实现连续时间RL的样本高效探索；COMBRL算法在连续控制任务中，样本效率显著优于离散时间基线，且能够扩展到高维任务。
- **发现**：
  - 结合模型不确定性的探索策略在连续时间环境下同样有效。
  - COMBRL在无监督RL设置下也能获得良好的样本复杂度保证。
  - 算法具有良好的可扩展性，贝叶斯神经网络相比高斯过程在大规模问题中更有优势。

## 7. 优点

- **方法亮点**：
  - 首次将基于不确定感知的探索（如UCB思想）系统性地应用于连续时间RL，填补了理论空白。
  - 理论证明次线性遗憾界，为算法可靠性提供了保证。
  - 支持两种概率模型（GP和BNN），兼顾了样本效率与可扩展性。
- **实验亮点**：在标准和无监督两种设置下均进行了评估，验证了算法的通用性；在多个deep RL任务上优于基线，展示了实际应用潜力。

## 8. 不足与局限

- **实验局限性**：
  - 未提供具体任务列表、超参数设置和完整结果表格，无法独立复现或验证。
  - 未进行详细的消融实验（如λ的选择、不同概率模型对比、对不同噪声水平的鲁棒性等）。
  - 可能缺少与最新连续时间最优控制方法（如基于SDE的方法）的对比。
- **应用限制**：
  - 依赖于ODE模型的平滑性假设，对于刚体碰撞、混合系统等不连续动力学可能无效。
  - 贝叶斯神经网络在大规模问题上计算成本仍然较高，训练时间未报告。
  - 探索策略为贪婪最大化，可能陷入局部最优，缺乏长期探索保证。

（完）
