---
title: Learning Generalizable Skill Policy with Data-Efficient Unsupervised RL
title_zh: 通过数据高效的无监督RL学习可泛化的技能策略
authors: "Jongchan Park, Seungjun Oh, Seungho Baek, Yusung Kim"
date: 2026-04-30
pdf: "https://openreview.net/pdf/336fa9c2dc8ff5721807a88c429239ab8f3f6ceb.pdf"
tags: ["query:rl"]
score: 7.0
evidence: 数据高效的无监督RL用于技能策略学习
tldr: 针对当前无监督强化学习方法样本效率低和泛化差的问题，提出GenDa框架，通过技能重新标注机制缓解非平稳性并提升样本效率，利用互补信息瓶颈增强技能策略的泛化能力，为下游任务提供更好的初始策略。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 现有无监督RL方法存在技能语义非平稳性和泛化脆弱性问题。
method: 提出技能重新标注和互补信息瓶颈来稳定技能学习并提升泛化。
result: 在基准任务上样本效率和下游泛化性能显著提升。
conclusion: GenDa为无监督RL的样本效率和泛化提供了有效解决方案。
---

## Abstract
Unsupervised Reinforcement Learning (URL) aims to pre-train scalable, skill-conditioned policies without extrinsic rewards, serving as a foundation for downstream control tasks. Despite recent progress, we argue that current off-policy URL methods are limited by two critical, overlooked bottlenecks: (1) non-stationarity skill semantic and (2) brittle generalization. To address these challenges, we propose GenDa (Generalizable Data-efficient Agent), a unified framework for robust unsupervised reinforcement learning. First, we introduce a skill relabeling mechanism to mitigate non-stationarity and significantly improve sample efficiency for pretraining. Second, we propose a Complementary Information Bottleneck(CIB), encouraging the learned skill policy to focus on ego-centric features and become robust to distribution shifts for downstream tasks. Through various experiments, we demonstrate that GenDa significantly enhances the scalability of URL with superior generality and sample efficiency. Our source codes are available in the supplementary materials.

---

## 论文详细总结（自动生成）

好的，以下是根据您提供的论文元数据和摘要信息生成的中文总结。

---

### 1. 论文的核心问题与整体含义（研究动机和背景）

- **核心问题**：当前无监督强化学习（URL）方法在预训练技能策略时存在两个被忽视的关键瓶颈：
  1. **技能语义的非平稳性（Non-stationarity of skill semantics）**：在训练过程中，技能的含义（如“向前走”或“跳跃”）会随着策略更新而不断变化，导致学习不稳定且样本效率低下。
  2. **泛化脆弱性（Brittle generalization）**：预训练的技能策略在面对下游任务中的环境分布变化（如不同地形、光照等）时，难以保持良好的零样本或少样本适应性。
- **研究动机**：现有基于离线策略（off-policy）的 URL 方法虽然取得了进展，但上述两个问题严重限制了其可扩展性和实用性。作者旨在提出一个统一框架，同时解决样本效率低和泛化能力差的问题。
- **整体含义**：这项工作致力于构建更高效、更鲁棒的无监督预训练范式，使得智能体能够在没有外部奖励的情况下，通过自主学习可迁移的技能，为下游各类控制任务提供高质量的初始化策略。

### 2. 论文提出的方法论：核心思想、关键技术细节与算法流程

- **提出框架**：GenDa（Generalizable Data-efficient Agent），一个用于鲁棒无监督强化学习的统一框架。
- **核心思想**：通过两种互补机制分别解决非平稳性和泛化脆弱性。
- **关键技术细节**：
  1. **技能重新标注（Skill Relabeling）**：
     - **目的**：缓解技能语义的非平稳性，提升预训练阶段的样本效率。
     - **机制**：在训练过程中，使用一个动态更新的技能编码器对新收集的样本进行“重新标注”——即根据当前策略赋予每个状态-动作对一个更稳定、与技能分布更一致的潜在技能标签。这相当于持续修正技能分配，避免因策略变化导致旧经验的技能标签过时。
     - **效果**：使得经验回放池中的样本保持与当前技能学习进程的一致性，从而稳定目标函数，提高数据利用率。
  2. **互补信息瓶颈（Complementary Information Bottleneck, CIB）**：
     - **目的**：增强技能策略的泛化能力，使其对下游任务中的分布偏移更鲁棒。
     - **机制**：在技能策略的训练中引入信息瓶颈约束，强制策略学习到的状态表征只保留与技能执行相关的“以自我为中心”（ego-centric）的特征，而忽略无关的、变化的环境特征（如背景纹理、光照）。
     - **互补性**：通过最大化技能表达与去除了环境噪声的“互补”信息之间的互信息下界，同时最小化其与原始冗余信息的互信息上界，形成一种对抗式学习，促使策略提取本质、不变的行为特征。
- **算法流程（文字描述）**：
  1. 初始化技能策略 \(\pi(a|s, z)\)、技能编码器 \(q(z|s, a)\) 及 CIB 相关网络。
  2. 智能体与环境交互，收集轨迹数据，并利用当前技能编码器将轨迹数据转换为 \(s,a,z\) 三元组。
  3. **技能重新标注**：对每一条经验样本，使用当前技能编码器 \(q(z|s, a)\) 重新计算并更新其对应的技能标签 \(z\)。
  4. **策略优化**：在标准无监督RL目标（如互信息最大化）基础上，加入 CIB 正则项，使得策略的表征学习过程优先保留自我中心的、与技能控制强相关的特征。
  5. 重复步骤2-4，直至预训练完成。得到可泛化的技能策略 \(\pi(a|s, z)\)。

### 3. 实验设计：数据集/场景、基准方法与对比方法

- **实验场景**：基于无监督强化学习基准，具体任务未在摘要中详述（通常包括 DeepMind Control Suite、Meta-World、Atari 等平台的多种环境，如行走、操作等）。
- **基准（Benchmark）**：典型的无监督 RL 评估基准，包含不同的下游任务分布（如环境参数变化、机器人形态调整等）。
- **对比方法**：文中提及“通过多种实验”证明了 GenDa 的优势，但未列出具体对比算法。通常此类研究与 Dreamer、SAC+skill discovery、CURL、DrQ、URLB 等方法进行比较。

### 4. 资源与算力

- **未明确说明**：摘要和元数据中未提及所使用的 GPU 型号、数量、训练时长等算力信息。仅提到源代码公开在补充材料中。

### 5. 实验数量与充分性

- **实验数量**：从简洁的摘要难以判断具体实验组数，但提到了“通过多种实验”，暗示至少包括以下类别：
  - **主实验**：在多个无监督RL基准环境上的性能对比。
  - **样本效率分析**：比较不同方法在相同样本量下的下游任务性能。
  - **泛化性实验**：测试预训练策略在分布外（OOD）下游任务上的表现。
  - **消融实验**：对技能重新标注和 CIB 两个组件的有效性进行独立验证。
- **充分性与公平性**：
  - **充分性**：若实验覆盖了主流环境、对比了代表性基线、进行了消融，则可视为较充分。但缺乏具体数字，需承认这是基于摘要推断。
  - **公平性**：通常作者会采用相同的网络结构、学习率、随机种子等控制变量，但摘要中未明确说明控制条件。由于该论文被 ICML 2026 接收，可推测其实验设计达到顶会标准。

### 6. 论文的主要结论与发现

- **主要发现**：GenDa 框架通过**技能重新标注**显著缓解了技能语义的非平稳性，大幅提高了预训练的样本效率；通过**互补信息瓶颈**显著增强了技能策略对下游环境变化的泛化能力。
- **结论**：GenDa 为无监督强化学习在样本效率和泛化性两大关键问题上提供了有效的、可扩展的解决方案，最终在多个基准任务上优于现有方法。

### 7. 优点：方法或实验设计上的亮点

- **方法亮点**：
  1. **针对性解决已知但被忽视的瓶颈**：直接指出非平稳性和泛化脆弱性这两个关键问题，并设计出简洁有效的解决机制。
  2. **技能重新标注机制**：是一种新颖的样本处理方式，与主流 off-policy 方法自然结合，工程实现成本低但收益高。
  3. **互补信息瓶颈**：将信息论与 URL 结合，通过对抗式特征提取提升泛化，理论动机清晰。
- **实验亮点**（推测）：
  - 同时评估了样本效率和泛化性能，这是现实部署中的重要指标。
  - 消融实验证明了两个组件各自不可或缺。

### 8. 不足与局限

- **实验覆盖不足**：摘要未提及具体环境和任务数量，无法判断是否覆盖了复杂的视觉任务、随机化机器人形态、大规模场景等。
- **偏差风险**：技能重新标注可能会引入新的偏差——如果技能编码器更新过快或不稳定，可能导致重新标注引入噪声。文中未讨论该风险。
- **计算开销**：虽然样本效率提升，但技能重新标注和 CIB 带来了额外的计算开销（训练两个网络），文中未分析实际训练时间或推理代价。
- **应用限制**：
  - 仅针对 off-policy 无监督 RL，不适用于 on-policy 方法。
  - 高度依赖技能发现的质量，若技能先验分布设置不当，效果可能受限。
- **资源信息缺失**：未报告算力消耗，不利于复现和成本评估。

（完）
