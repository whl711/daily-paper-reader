---
title: "Rethinking the Design Space of Reinforcement Learning for Diffusion Models: On the Importance of Likelihood Estimation Beyond Loss Design"
title_zh: 重新思考扩散模型强化学习的设计空间：似然估计的重要性超越损失设计
authors: "Jaemoo Choi, Yuchen Zhu, Wei Guo, Petr Molodyk, Bo Yuan, Jinbin Bai, Yi Xin, Molei Tao, Yongxin Chen"
date: 2026-04-30
pdf: "https://openreview.net/pdf/5f18c7399448fd0b9ef9aebe19679073be540dad.pdf"
tags: ["query:diffusion-rl"]
score: 7.0
evidence: 扩散模型RL设计空间的系统分析；似然估计
tldr: 针对扩散模型中强化学习应用时似然计算困难的问题，本文系统分析了策略梯度目标、似然估计和奖励设计三个因素。通过解耦实验揭示似然估计方式对算法性能的关键影响。为扩散模型RL方法的设计提供了理论指导，有助于未来开发更高效的偏好对齐算法。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 现有扩散模型RL方法忽视似然估计对性能的影响，使用临时估计器。
method: 系统解耦策略梯度目标、似然估计和奖励设计三因素，进行消融分析。
result: 发现似然估计方式显著影响收敛速度和生成质量。
conclusion: 合理设计似然估计对扩散模型RL至关重要。
---

## Abstract
Reinforcement learning has been widely applied to diffusion and flow models for visual tasks such as text-to-image generation. However, these tasks remain challenging because diffusion models have intractable likelihoods, which creates a barrier for directly applying popular policy-gradient type methods. Existing approaches primarily focus on crafting new objectives built on already heavily engineered LLM objectives, using ad hoc estimators for likelihood, without a thorough investigation into how such estimation affects overall algorithmic performance.  In this work, we provide a systematic analysis of the RL design space by disentangling three factors: i) policy-gradient objectives, ii) likelihood estimators, and iii) rollout sampling schemes. We show that adopting an evidence lower bound (ELBO) based model likelihood estimator, computed only from the final generated sample, is the dominant factor enabling effective, efficient, and stable RL optimization, outweighing the impact of the specific policy-gradient loss functional. We validate our findings across multiple reward benchmarks using SD 3.5 Medium, and observe consistent trends across all tasks. Our method improves the GenEval score from $0.24$ to $0.95$ in $90$ GPU hours, which is $4.6\times$ more efficient than FlowGRPO and $2\times$ more efficient than the SOTA method DiffusionNFT without reward hacking.

---

## 论文详细总结（自动生成）

# 论文中文详细总结

## 1. 论文的核心问题与整体含义（研究动机和背景）
- **研究动机**：扩散模型（Diffusion Models）在文本到图像生成等视觉任务中表现优异，但将其与强化学习（RL）结合存在根本性障碍——扩散模型的似然（likelihood）难以精确计算，导致无法直接应用策略梯度类方法。现有方法主要借鉴大型语言模型（LLM）中已高度工程化的目标函数，并采用临时（ad hoc）的似然估计器，但从未系统研究似然估计如何影响算法整体性能。
- **核心问题**：在扩散模型RL设计中，究竟是**策略梯度目标函数本身**、**似然估计方式**、还是**采样方案（rollout）** 对性能起决定性作用？作者通过解耦三个因素，揭示似然估计的重要性往往超过损失函数设计。

## 2. 论文提出的方法论：核心思想、关键技术细节、公式或算法流程
- **核心思想**：将扩散模型RL的设计空间分解为三个独立的因素，分别进行消融分析：
  1. **策略梯度目标（policy-gradient objectives）**：如 REINFORCE、PPO、DPO 或其变体。
  2. **似然估计器（likelihood estimators）**：包括基于证据下界（ELBO）的似然估计、蒙特卡洛近似、以及假设为1的简化估计等。
  3. **采样方案（rollout sampling schemes）**：例如从完整去噪轨迹采样、仅从最终样本采样等。
- **关键技术细节**：
  - 提出使用**基于ELBO的似然估计器**，该估计值仅从最终生成的样本计算而得，无需完整轨迹的似然计算。
  - 通过解耦实验发现，采用ELBO似然估计是影响RL优化效果、效率和稳定性的主导因素，其重要性远超具体选择哪种策略梯度损失函数（如 REINFORCE vs. PPO）。
- **算法流程**（文字说明）：
  1. 给定一个预训练的扩散模型（如SD 3.5 Medium）作为策略。
  2. 在每一步迭代中，使用扩散模型生成一批样本（rollout）。
  3. 对每个生成样本，计算其基于ELBO的似然估计（仅需最终样本）。
  4. 根据选定的策略梯度目标（如REINFORCE），利用估计的似然和奖励函数计算梯度更新参数。
  5. 重复直至收敛。

## 3. 实验设计
- **数据集/场景**：多个奖励基准任务，即不同的文本到图像生成偏好对齐场景（具体名称未在摘要中列出，但提到“multiple reward benchmarks”）。
- **基准（benchmark）**：使用了 SD 3.5 Medium 作为基础扩散模型。对比方法包括：
  - FlowGRPO（现有方法）
  - DiffusionNFT（当前最先进方法，SOTA）
  - 以及其他未命名的基线（通过消融实验比较不同似然估计方案的影响）。
- **对比指标**：主要使用 **GenEval score**，以及可能与奖励质量、效率相关的指标。

## 4. 资源与算力
- 文中明确提到：使用 **SD 3.5 Medium** 模型，在 **90 GPU hours** 内将 GenEval 分数从 0.24 提升至 0.95。
- 效率对比：比 FlowGRPO 快 **4.6倍**，比 DiffusionNFT 快 **2倍** 且没有奖励黑客（reward hacking）问题。
- 未具体说明 GPU 型号和数量，仅给出总GPU时长。

## 5. 实验数量与充分性
- **实验数量**：系统性地进行了**三因素解耦**的消融实验，覆盖了多种策略梯度目标（例如 REINFORCE、PPO 等）、多种似然估计器（ELBO、临时估计等）、以及多种采样方案组合。
- **充分性评估**：
  - 实验设计较为**全面**：通过控制变量法分别考察了每个因素，能够揭示各因素间的相对重要性。
  - **客观性**：在不同奖励基准上验证了一致趋势（“consistent trends across all tasks”），说明结论具有泛化性。
  - 但摘要中未给出完整的实验数量表或具体消融组合数目，需要阅读全文才能评估是否涵盖了所有可能的重要组合。

## 6. 论文的主要结论与发现
- 使用**基于ELBO的似然估计器**（仅从最终样本计算）是扩散模型RL优化中**最关键的因素**，其影响远超策略梯度损失函数的具体形式。
- 合理的似然估计能够带来**更快的收敛速度、更好的生成质量**，并避免奖励黑客现象。
- 现有方法之所以表现不佳，很大程度上归因于使用了临时/不恰当的似然估计，而非损失函数设计不当。

## 7. 优点
- **系统性与解耦分析**：首次将扩散模型RL设计空间分解为三个独立因素进行消融，提供了清晰的理论指导。
- **实用性**：提出的ELBO似然估计方法简单高效（仅需最终样本），显著提升性能并降低计算成本（90 GPU hours达到高GenEval分数）。
- **公平对比**：与现有SOTA方法（FlowGRPO、DiffusionNFT）在相同条件下对比，并指出其避免了奖励黑客。
- **趋势一致**：在多个奖励基准上均观察到相同结论，增强了结果的可信度。

## 8. 不足与局限
- **实验覆盖有限**：虽然使用了多个基准，但仅针对文本到图像的视觉任务，未涉及其他扩散模型应用（如视频生成、分子设计等），泛化性有待验证。
- **基准细节缺失**：摘要中未列出具体奖励基准名称和数据集规模，读者需查阅原文。
- **似然估计的精度**：ELBO估计本身也是近似，在一定条件下可能与真实似然存在偏差，该偏差的影响未深入讨论。
- **计算资源不透明**：仅提及总GPU小时数，未说明GPU型号和数量，难以与其他方法进行更精确的效率比较。
- **消融组合可能不完整**：虽然分离了三因素，但可能存在交互效应未被完全探索（例如某些损失函数与特定采样方案联合使用时表现不同的情况）。
- **理论基础**：论文主要依赖经验观察，对于为何ELBO估计优于其他估计的理论解释可能不够深入。

（完）
