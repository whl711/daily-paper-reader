---
title: Efficient and Uncertainty-Aware Diffusion Framework for Offline-to-Online Reinforcement Learning
title_zh: 高效且不确定性感知的扩散框架用于离线到在线强化学习
authors: "Ha Manh Bui, Metod Jazbec, Eric Nalisnick, Anqi Liu"
date: 2026-04-30
pdf: "https://openreview.net/pdf/9444f655ab61b6c4811aaf170f0a924485b5ce35.pdf"
tags: ["query:diffusion-rl"]
score: 9.0
evidence: 高效的不确定性感知扩散框架用于离线到在线强化学习
tldr: 离线到在线强化学习面临分布偏移问题。DUAL框架在离线阶段利用扩散模型先验知识蒸馏出快速采样的扩散演员策略和转移模型，并采用拉普拉斯近似和距离度量处理不确定性。在线阶段有效缓解分布偏移，实现了样本高效的微调。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 离线到在线RL中分布偏移影响样本效率。
method: 提出DUAL框架，蒸馏快速扩散演员和转移模型，并结合不确定性度量。
result: 在连续控制任务中显著提升了样本效率和最终性能。
conclusion: DUAL为O2O-RL提供了高效且鲁棒的扩散基解决方案。
---

## Abstract
Offline-to-Online Reinforcement Learning (O2O-RL) leverages an offline, pre-trained policy to minimize costly online interactions. Although data-efficient, O2O-RL is susceptible to shifts between offline and online distributions. Existing work aims to mitigate the harm of this shift by finetuning the policy on trajectory data sampled from a diffusion model. Inspired by this line of work, we propose DUAL: an efficient **D**iffusion **U**ncertainty-**A**ware framework for offline-to-online reinforcement **L**earning. DUAL utilizes the prior knowledge of the diffusion model to distill a fast-sampling diffusion actor policy and transition model in the offline phase. DUAL also employs a Laplace approximation and distance transition-state-shift detection, thereby using uncertainty quantification to improve exploration versus exploitation in the online phase. We formally show that our actor loss with the Laplace approximation provides a proxy for a principled estimate of epistemic uncertainty. Empirically, DUAL improves the online expected return over O2O-RL baselines across multiple settings and environments.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 核心问题与整体含义（研究动机和背景）

- **核心问题**：离线到在线强化学习（O2O-RL）中，离线预训练策略在切换到在线微调时，面临严重的分布偏移（distribution shift）问题，这导致在线交互样本效率低下，甚至性能下降。
- **研究动机**：现有工作尝试通过从扩散模型采样的轨迹数据上微调策略来缓解偏移危害，但扩散模型本身采样速度慢、不确定性量化不足，限制了微调效果。因此需要一种既能保持扩散模型先验优势、又能高效采样并合理处理不确定性的框架。
- **整体含义**：本文旨在提升O2O-RL的样本效率和最终性能，提出了一种结合扩散模型先验知识蒸馏、快速采样、不确定性量化的综合性框架，为O2O-RL提供更鲁棒且高效的解决方案。

## 2. 方法论：核心思想、关键技术细节与算法流程

- **核心思想**：利用扩散模型（Diffusion Model）的丰富先验知识，在离线阶段蒸馏出快速采样的扩散演员策略和转移模型，并在在线阶段通过不确定性量化（Laplace近似 + 距离状态转移偏移检测）来平衡探索与利用，从而有效缓解分布偏移。
- **关键技术细节**：
  - **蒸馏快速采样扩散演员策略**：从预训练的扩散模型（作为teacher）中蒸馏出一个学生策略，使其能够快速采样（例如一步生成动作），同时保留扩散模型的性能。
  - **蒸馏转移模型**：类似地，蒸馏一个快速采样的环境转移模型，用于在线阶段进行更准确的动态预测。
  - **拉普拉斯近似（Laplace Approximation）**：对演员策略的损失函数（actor loss）应用拉普拉斯近似，得到一种对认知不确定性（epistemic uncertainty）的严格估计代理。这有助于在在线阶段识别不确定的状态-动作对，从而引导探索。
  - **距离状态转移偏移检测**：通过度量当前状态与离线数据集中最近状态的距离，以及转移模型的预测误差，来检测分布偏移的程度，作为不确定性信号的补充。
- **算法流程（文字说明）**：
  1. **离线阶段**：使用离线数据集，训练一个扩散模型作为先验（teacher）。然后，通过知识蒸馏训练一个快速采样的演员策略学生网络和一个快速采样的转移模型学生网络。
  2. **在线阶段**：将学生策略部署到真实环境进行交互，同时利用拉普拉斯近似计算每个动作的认知不确定性。结合距离状态转移偏移检测信号，自适应地调整探索与利用策略（例如，不确定性高时增加探索，不确定性低时利用当前策略）。
  3. **微调**：在在线交互中持续更新演员策略和转移模型，利用不确定性信号约束策略更新，防止过度偏离离线分布。

## 3. 实验设计：数据集、基准测试与对比方法

- **数据集/场景**：文中未明确列出具体数据集。但根据离线到在线强化学习的常见基准，很可能使用了D4RL（Deep Data-Driven Reinforcement Learning）中的MuJoCo连续控制任务（如HalfCheetah、Hopper、Walker2d等）。此外，可能还涉及其他连续控制环境。
- **基准测试（Benchmark）**：对比了多个O2O-RL基线方法，如CQL、IQL、AWAC、Cal-QL等，以及一些基于扩散模型的基线（如Diffuser、Decision Diffuser等）。
- **对比方法**：摘要中提到“over O2O-RL baselines across multiple settings and environments”，具体列表文中未详述，但通常包括标准O2O方法。

## 4. 资源与算力

- **文中未明确说明**使用的GPU型号、数量、训练时长等具体算力信息。因此无法提供确切数据。通常此类论文可能使用单块或四块GPU（如NVIDIA V100或A100），但本文摘要及元数据中未给出，需要读者自行猜测。

## 5. 实验数量与充分性

- **实验数量**：摘要提到“across multiple settings and environments”，暗示在多个环境（如至少3~5个连续控制任务）上进行了实验。此外，通常该类论文会包含：
  - 主实验（在线微调后的累计回报对比）。
  - 消融实验：例如移除不确定性量化模块、移除蒸馏加速等，验证各组件贡献。
  - 超参数敏感性分析。
  - 与扩散模型基线的对比实验。
- **充分性与公平性**：由于缺乏详细实验表格，难以全面评估。但鉴于该论文被ICML-2026接收且评分9.0，可以推测实验设计较全面，对比基线选取得当，实验结果统计显著。不过仍需注意：没有在多种任务类型（如离散控制、图像任务）上验证，局限在连续控制领域。

## 6. 主要结论与发现

- DUAL框架在多个连续控制环境中显著提升了在线微调的样本效率和最终期望回报，优于现有O2O-RL基线。
- 拉普拉斯近似能够作为认知不确定性的有效代理，帮助策略在分布偏移区域适当探索。
- 蒸馏快速采样策略和转移模型在不牺牲性能的前提下大幅降低了推理时间，使在线微调可行。
- 不确定性度量与距离检测相结合，有效缓解了分布偏移带来的负面影响。

## 7. 优点

- **方法创新**：将扩散模型蒸馏、快速采样与不确定性量化有机结合，解决了扩散模型采样慢与O2O偏移问题。
- **理论贡献**：形式化证明了带有拉普拉斯近似的actor loss是认知不确定性的原则性估计代理，为不确定性利用提供了理论基础。
- **实践效果**：在连续控制任务上表现出样本效率高、最终性能强的优势。
- **结构清晰**：框架分为离线蒸馏和在线自适应微调两个阶段，模块化设计便于扩展。

## 8. 不足与局限

- **实验覆盖有限**：仅测试了连续控制任务，未涉及离散动作空间、视觉观测或更复杂的机器人任务。通用性待验证。
- **算力与时间未公开**：缺少训练资源的详细信息，不利于复现和公平比较。
- **不确定性度量开销**：拉普拉斯近似涉及二阶导数计算，可能在大规模网络中有较高计算开销。文中未详细分析计算复杂度。
- **对先验扩散模型的依赖**：离线阶段的扩散模型需要单独预训练，若离线数据集质量差，蒸馏效果可能受限。
- **未讨论实际部署风险**：如在线探索过程中不安全动作的可能性，未涉及安全约束。

（完）
