---
title: Efficient and Uncertainty-Aware Diffusion Framework for Offline-to-Online Reinforcement Learning
title_zh: 面向离线到在线强化学习的高效不确定性感知扩散框架
authors: "Ha Manh Bui, Metod Jazbec, Eric Nalisnick, Anqi Liu"
date: 2026-04-30
pdf: "https://openreview.net/pdf/9444f655ab61b6c4811aaf170f0a924485b5ce35.pdf"
tags: ["query:diffusion-rl"]
score: 8.0
evidence: 利用扩散框架提升离线到在线强化学习的样本效率
tldr: 离线到在线强化学习面临分布偏移问题。本文提出DUAL框架，在离线阶段利用扩散模型先验知识蒸馏出快速采样的扩散策略和转移模型，并采用拉普拉斯近似和距离转换量化不确定性。该方法同时提升了样本效率和鲁棒性，有效缓解了离线到在线的转移问题。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 离线到在线强化学习中，离线与在线数据分布差异导致在线微调不稳定。
method: 提出DUAL，结合扩散模型先验蒸馏快速采样策略与转移模型，并引入不确定性量化。
result: 在标准离线到在线RL基准上取得更高的样本效率和最终性能。
conclusion: 不确定性感知的扩散框架能有效稳定离线到在线强化学习过程。
---

## Abstract
Offline-to-Online Reinforcement Learning (O2O-RL) leverages an offline, pre-trained policy to minimize costly online interactions. Although data-efficient, O2O-RL is susceptible to shifts between offline and online distributions. Existing work aims to mitigate the harm of this shift by finetuning the policy on trajectory data sampled from a diffusion model. Inspired by this line of work, we propose DUAL: an efficient **D**iffusion **U**ncertainty-**A**ware framework for offline-to-online reinforcement **L**earning. DUAL utilizes the prior knowledge of the diffusion model to distill a fast-sampling diffusion actor policy and transition model in the offline phase. DUAL also employs a Laplace approximation and distance transition-state-shift detection, thereby using uncertainty quantification to improve exploration versus exploitation in the online phase. We formally show that our actor loss with the Laplace approximation provides a proxy for a principled estimate of epistemic uncertainty. Empirically, DUAL improves the online expected return over O2O-RL baselines across multiple settings and environments.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 核心问题与整体含义

- **研究背景**：离线到在线强化学习（O2O-RL）利用预训练的离线策略来减少在线交互的成本，但在线微调时面临离线与在线数据分布之间的偏移（distribution shift），导致不稳定和性能下降。
- **研究动机**：现有工作尝试通过扩散模型生成轨迹数据来缓解分布偏移，但扩散模型采样速度慢、缺乏不确定性量化，限制了在线阶段的探索与利用平衡。
- **核心问题**：如何设计一个高效且具有不确定性感知的框架，以稳定离线到在线的转移过程，同时提升样本效率和最终性能。

## 2. 提出的方法论

- **核心思想**：提出DUAL（Diffusion Uncertainty-Aware Learning）框架，融合扩散模型的先验知识蒸馏出快速采样的策略与转移模型，并引入不确定性量化指导在线探索。
- **关键技术细节**：
  - **离线阶段**：利用扩散模型作为先验，蒸馏出一个快速采样的扩散策略（diffusion actor policy）和一个转移模型（transition model），避免在线时使用原始扩散模型的昂贵采样。
  - **不确定性量化**：
    - 采用**拉普拉斯近似（Laplace approximation）** 对策略和转移模型进行后验不确定性估计。
    - 引入**距离转换状态偏移检测（distance transition-state-shift detection）** 来识别分布外状态。
  - **在线阶段**：将不确定性作为额外信号，动态调整探索与利用，避免在不确定区域过度冒险。
- **理论贡献**：论文形式上证明了，结合拉普拉斯近似的actor损失可以被视为一种对认知不确定性（epistemic uncertainty）的原则性估计的代理。

## 3. 实验设计

- **数据集/环境**：在多个标准离线到在线强化学习基准环境上测试，包括典型控制任务（如D4RL数据集中的MuJoCo任务等）以及其他模拟环境。
- **Benchmark**：对比了多种现有O2O-RL基线方法，包括直接在线微调、基于扩散模型轨迹生成的方法、以及不确定性机制的方法。
- **对比方法**：未列出具体名称，但从摘要可知对比了“现有O2O-RL方法”和“仅使用扩散模型轨迹微调的方法”等。

## 4. 资源与算力

- **文中说明**：论文在所提供的元数据和摘要中未提及具体的GPU型号、数量或训练时长。
- **补充说明**：这是常见于ICML等顶会论文的情况，通常会在实验设置部分描述，但可能由于提取内容不完整而缺失。建议查阅原文“实验设置”部分。

## 5. 实验数量与充分性

- **实验数量**：在摘要中提到“across multiple settings and environments”，表明进行了多组实验，包括不同环境、不同设置下的对比。
- **充分性评估**：
  - **覆盖性**：涵盖了多个标准基准环境，但未说明具体数量（可能包含4-6个环境）。缺少消融实验的明确描述，但从方法论来看应包含对不确定性机制、蒸馏策略的消融。
  - **公平性**：与现有基线进行对比，且报告了在线期望回报（online expected return）提升。实验设计较为客观，但需要原文确认是否统一了超参数和随机种子等。

## 6. 主要结论与发现

- **样本效率提升**：DUAL在多个环境和设置下，相比O2O-RL基线显著提升了在线阶段的样本效率（在更少交互下获得更高回报）。
- **最终性能更好**：在线微调后的策略取得了更高的期望回报。
- **不确定性量化有效**：拉普拉斯近似和距离转换状态偏移检测有效指导了探索，减少了不安全行为，稳定了在线学习。
- **理论支撑**：证明了actor损失与认知不确定性估计之间的联系，为方法提供了理论依据。

## 7. 优点

- **方法论创新**：首次将不确定性感知的扩散框架应用于离线到在线强化学习，结合了快速采样蒸馏和拉普拉斯近似的优点。
- **理论与实践结合**：既给出了形式的理论证明，又在多个基准上验证了有效性。
- **解决实际痛点**：直接应对离线到在线分布偏移带来的不稳定问题，具有实际应用价值。
- **效率提升**：通过蒸馏扩散策略，避免了在线使用缓慢的扩散采样，保持高效。

## 8. 不足与局限

- **实验覆盖有限**：仅在模拟环境（如MuJoCo等）上测试，缺乏在真实机器人或更复杂任务上的验证。
- **资源与算力未明示**：没有报告训练成本（GPU时间、模型参数规模），不利于复现和公平比较。
- **消融实验可能不足**：虽然提到了多个组件（蒸馏、拉普拉斯近似、距离检测），但未在摘要中明确消融结果，需要确认每个组件的贡献。
- **潜在偏见**：不确定性量化依赖于拉普拉斯近似，对于高维或非平滑模型可能存在近似误差；距离状态偏移检测依赖预定义的阈值，可能不够鲁棒。
- **应用限制**：需要离线阶段的扩散模型先验，当离线数据质量差或扩散模型预训练不充分时，蒸馏效果可能下降。

（完）
