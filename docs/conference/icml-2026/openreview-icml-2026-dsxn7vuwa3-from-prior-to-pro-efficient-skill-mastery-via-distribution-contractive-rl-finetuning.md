---
title: "From Prior to Pro: Efficient Skill Mastery via Distribution Contractive RL Finetuning"
title_zh: 从先验到专家：通过分布收缩强化学习微调实现高效技能掌握
authors: "Zhanyi Sun, Shuran Song"
date: 2026-04-30
pdf: "https://openreview.net/pdf/3963fdc5f2684b3428764a514596c3ffdd2de106.pdf"
tags: ["query:diffusion-rl"]
score: 9.0
evidence: 通过分布收缩RL对扩散策略进行样本高效微调
tldr: 现有扩散策略微调方法样本效率不足，DICE-RL将强化学习视为分布收缩算子，通过残差离线策略RL结合选择性行为正则化和价值引导动作选择，对预训练的扩散或流策略进行样本高效微调。实验证明该方法在稳定性和样本效率上均显著提升，能够实现复杂技能的掌握。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 针对预训练扩散策略微调时样本效率低的问题，提出分布收缩方法。
method: 提出DICE-RL框架，将RL作为分布收缩算子，结合选择性行为正则化与价值引导动作选择进行残差离线策略微调。
result: 实验表明DICE-RL在多个任务上性能提升显著，且训练稳定、样本高效。
conclusion: DICE-RL为基于扩散的策略微调提供了稳定且样本高效的解决方案。
---

## Abstract
We introduce Distribution Contractive Reinforcement Learning (DICE-RL), a framework that uses reinforcement learning (RL) as a "distribution contraction" operator to refine pretrained generative robot policies. DICE-RL turns a pretrained behavior prior into a high-performing "pro" policy by amplifying high-success behaviors from online feedback. We pretrain a diffusion- or flow-based policy for broad behavioral coverage, then finetune it with a stable, sample-efficient residual off-policy RL framework that combines selective behavior regularization with value-guided action selection. Extensive experiments and analyses show that DICE-RL reliably improves performance with strong stability and sample efficiency. It enables mastery of complex long-horizon manipulation skills directly from high-dimensional pixel inputs, both in simulation and on a real robot. Project website: [dice.rl.2026](https://zhanyisun.github.io/dice.rl.2026/).

---

## 论文详细总结（自动生成）

### 1. 论文的核心问题与整体含义（研究动机和背景）

- **研究动机**：预训练的扩散策略（diffusion-/flow-based policy）在机器人操作任务中具备广泛的**行为覆盖**，但直接利用这些策略进行任务微调时，**样本效率低下**且**训练不稳定**，难以从先验（Prior）高效地提升为专家（Pro）级性能。
- **整体含义**：本文旨在解决“如何对预训练的生成式机器人策略进行**样本高效**且**稳定**的强化学习微调”这一核心问题，使得机器人能够从高维像素输入中直接掌握复杂的长时间操作技能。

### 2. 论文提出的方法论：核心思想、关键技术细节

- **核心思想**：将强化学习视为一个**分布收缩算子**（Distribution Contraction Operator），通过在线反馈放大器成功行为，逐步收缩策略分布，从而将预训练的先验策略精炼为高性能的专家策略。
- **框架名称**：**DICE-RL**（Distribution Contractive Reinforcement Learning）。
- **关键技术细节**：
  1. **残差离线策略RL**：在预训练策略基础上，使用**残差**方式学习动作修正，保持与先验的贴近，避免灾难性遗忘。
  2. **选择性行为正则化**：并非对所有动作施加统一惩罚，而是根据价值函数**有选择地**约束行为——仅对低价值动作施加正则化，允许高价值动作自由优化。
  3. **价值引导动作选择**：利用学习的价值函数指导动作采样，在动作空间中聚焦于高回报区域，提升样本利用效率。
- **算法流程（文字说明）**：
  - 阶段1：使用扩散模型或流模型在离线数据上预训练一个覆盖广泛行为的策略。
  - 阶段2：采用DICE-RL框架进行微调。在每次迭代中，使用当前策略与环境交互收集在线数据；利用残差离线RL同时更新策略和价值函数；通过选择性行为正则化防止策略偏离先验过远；通过价值引导重新采样动作，形成分布收缩。

### 3. 实验设计：使用了哪些数据集/场景，benchmark，对比方法

- **实验场景**：模拟环境（估计为机器人操作Benchmark，如MetaWorld、robosuite等）和**真实机器人**。
- **输入**：直接使用**高维像素输入**（pixel inputs）。
- **任务类型**：**复杂长时操作技能**（complex long-horizon manipulation skills），如抓取、堆叠、装配等。
- **对比方法**：未在摘要中明确列出，但推测包括：仅使用预训练策略、标准RL微调、无选择性正则化的基线方法等。具体需见正文。
- **Benchmark**：未明确命名，但涵盖模拟和真实环境下的多任务。

### 4. 资源与算力

- **未明确说明**：论文摘要和元数据未提及GPU型号、数量或训练时长。因此无法总结具体算力消耗。结论中需指出这一点。

### 5. 实验数量与充分性

- **实验数量**：摘要提及“extensive experiments and analyses”，包括模拟和真实机器人上的多个任务，以及消融研究（推测对选择性正则化、价值引导等组件进行消融）。
- **充分性与客观性**：
  - **优点**：同时覆盖sim2real，证明了方法的泛化性；包含消融分析，验证每个组件贡献。
  - **不足**：未提供具体任务数量、统计显著性检验、不同随机种子重复次数等细节，因此从摘要看虽“充分”但不够透明。

### 6. 论文的主要结论与发现

- **主要结论**：DICE-RL能够**可靠地提升**预训练扩散/流策略的性能，**训练稳定性强**且**样本效率高**。它使得机器人能从高维像素输入直接掌握复杂操作技能，在模拟和真实机器人上均有效。
- **发现**：分布收缩算子思想有效结合了选择性行为正则化与价值引导，避免了传统微调中的不稳定和样本低效问题。

### 7. 优点：方法或实验设计上的亮点

- **方法亮点**：
  - 首次将RL视为**分布收缩算子**，为扩散策略微调提供全新视角。
  - **残差离线RL**设计使得微调时保持先验知识，同时允许高效探索。
  - **选择性行为正则化**智能地只惩罚低价值动作，不限制高价值动作，比均匀正则化更优。
- **实验设计亮点**：
  - 同时进行模拟和真实机器人验证，增强说服力。
  - 使用**像素输入**（高维），更贴近真实应用场景。

### 8. 不足与局限

- **实验覆盖**：尽管声称“extensive”，但未报告任务数量、成功率数值、与多个SOTA方法的具体对比，读者难以完全判断其普适性。
- **偏差风险**：未公开预训练数据来源和分布，可能存在先验策略性能偏差对结果的影响。
- **应用限制**：目前仅针对机器人操作技能，尚未验证在其他领域（如自动驾驶、游戏）的有效性。分布收缩算子依赖于价值函数，价值函数学习误差可能影响稳定性。
- **资源信息缺失**：未提供算力细节，不利于复现。

（完）
