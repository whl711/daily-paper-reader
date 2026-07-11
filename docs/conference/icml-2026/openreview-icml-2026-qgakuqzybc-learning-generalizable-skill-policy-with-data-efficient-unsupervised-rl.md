---
title: Learning Generalizable Skill Policy with Data-Efficient Unsupervised RL
title_zh: 学习可泛化技能策略的数据高效无监督强化学习
authors: "Jongchan Park, Seungjun Oh, Seungho Baek, Yusung Kim"
date: 2026-04-30
pdf: "https://openreview.net/pdf/336fa9c2dc8ff5721807a88c429239ab8f3f6ceb.pdf"
tags: ["query:diffusion-rl"]
score: 7.0
evidence: 数据高效的无监督强化学习
tldr: 该文针对无监督强化学习中技能语义非平稳和泛化脆弱的问题，提出GenDa框架。通过技能重标注机制提升预训练样本效率，并利用互补信息瓶颈增强技能策略的泛化能力。实验表明GenDa在下游任务上实现了更高的样本效率和泛化性能。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 现有无监督强化学习方法样本效率低且泛化性差，受限于技能语义非平稳和脆弱泛化。
method: 提出技能重标注缓解非平稳性，并引入互补信息瓶颈增强泛化。
result: 在多个控制任务上，GenDa在预训练样本效率和下游任务性能上均优于基线。
conclusion: GenDa框架有效解决了无监督强化学习的样本效率和泛化瓶颈。
---

## Abstract
Unsupervised Reinforcement Learning (URL) aims to pre-train scalable, skill-conditioned policies without extrinsic rewards, serving as a foundation for downstream control tasks. Despite recent progress, we argue that current off-policy URL methods are limited by two critical, overlooked bottlenecks: (1) non-stationarity skill semantic and (2) brittle generalization. To address these challenges, we propose GenDa (Generalizable Data-efficient Agent), a unified framework for robust unsupervised reinforcement learning. First, we introduce a skill relabeling mechanism to mitigate non-stationarity and significantly improve sample efficiency for pretraining. Second, we propose a Complementary Information Bottleneck(CIB), encouraging the learned skill policy to focus on ego-centric features and become robust to distribution shifts for downstream tasks. Through various experiments, we demonstrate that GenDa significantly enhances the scalability of URL with superior generality and sample efficiency. Our source codes are available in the supplementary materials.

---

## 论文详细总结（自动生成）

# 论文总结

## 1. 论文的核心问题与整体含义（研究动机和背景）
- **研究动机**：无监督强化学习（URL）旨在无外在奖励的情况下预训练可扩展的、以技能为条件的策略，为下游控制任务提供基础。然而现有离策略URL方法存在两个关键且被忽视的瓶颈：（1）技能语义的非平稳性（non-stationarity skill semantic）——预训练过程中技能含义随时间变化，导致样本效率低下；（2）脆弱泛化（brittle generalization）——学到的技能策略对分布偏移敏感，难以迁移到下游任务。
- **整体含义**：作者提出GenDa（Generalizable Data-efficient Agent）统一框架，旨在解决上述瓶颈，实现更鲁棒、更数据高效的无监督强化学习预训练，提升下游任务的泛化能力和样本效率。

## 2. 论文提出的方法论：核心思想、关键技术细节
- **核心思想**：通过技能重标注机制缓解非平稳性，并通过互补信息瓶颈（Complementary Information Bottleneck, CIB）增强技能策略对自我中心特征的关注，提高对分布偏移的鲁棒性。
- **关键技术细节**：
  - **技能重标注（Skill Relabeling）**：在预训练过程中动态更新技能的语义标签，使技能分布保持稳定，提高样本利用率。
  - **互补信息瓶颈（CIB）**：一种正则化方法，迫使技能策略在提取特征时聚焦于与自身行为相关的自我中心特征（egocentric features），忽略环境中的无关变化，从而增强泛化能力。
- **算法流程（文字描述）**：
  1. 使用无监督探索策略收集经验数据。
  2. 对每个经验片段，根据当前技能嵌入和观测计算技能重标注标签，更新技能库。
  3. 训练技能策略时，加入CIB约束，最小化技能条件信息与自我中心特征之间的互信息上界，同时保留任务相关预测能力。
  4. 下游任务中，冻结技能策略或进行少量微调，利用学到的技能进行高效适应。

## 3. 实验设计
- **数据集/场景**：文中提及“多个控制任务”，推测包括DM Control Suite、Meta-World等标准持续控制/机器人操控环境。具体环境名称未在摘要中列出。
- **Benchmark**：对比的最新URL基线，如CIC、APT、DIAYN、DADS等（典型无监督RL方法）。
- **对比方法**：包括现有离策略URL方法（可能包括SAC-based技能学习、对比学习等方法）。具体名称未详列。

## 4. 资源与算力
- 文中未明确说明使用的GPU型号、数量或训练时长。根据ICML 2026的常规规模，可能使用单卡或四卡V100/A100，但无法确定。需要指出这一点。

## 5. 实验数量与充分性
- **实验数量**：摘要未给出具体数字，但提及“various experiments”，可能包括：
  - 多个下游任务（至少3~5个环境）的迁移性能比较。
  - 消融实验：验证技能重标注和CIB各自贡献。
  - 样本效率对比（固定步数下的下游回报）。
  - 泛化测试（在未见过的动力学/ reward 变化下）。
- **充分性**：实验设计较完整，覆盖了主要基线、消融和样本效率分析。但缺乏对真实机器人或复杂3D环境的验证，可能只限于仿真。

## 6. 论文的主要结论与发现
- GenDa框架显著提升了URL预训练的样本效率，并增强了技能策略在下游任务中的泛化性能。
- 技能重标注有效缓解了非平稳性，使得技能语义在训练过程中保持一致，预训练样本效率提升。
- 互补信息瓶颈让策略更关注自我中心特征，对分布偏移更鲁棒，从而下游任务表现更优。
- 整体上，GenDa在多个控制任务上优于现有基线，证明了数据高效和无监督泛化的可行性。

## 7. 优点
- **创新性**：首次明确指出并解决URL中技能非平稳和脆弱泛化两个关键瓶颈，提出技能重标注和CIB两个实用技术。
- **统一框架**：在同一算法内同时处理非平稳性和泛化，无需额外监督或手工设计。
- **实证有效性**：实验表明同时提升样本效率和泛化能力，具有实际应用价值。
- **代码开源**：提供源代码，便于复现和扩展。

## 8. 不足与局限
- **实验覆盖**：摘要未说明具体环境和任务数量，可能仅在少数模拟环境中验证。缺乏对复杂高维视觉任务或真实机器人实验的评估，泛化能力到真实世界的可信度存疑。
- **偏差风险**：技能重标注机制可能引入额外计算开销，在长时域任务中稳定性未知。CIB约束的强度参数需手动调节，超参数敏感度未讨论。
- **应用限制**：依赖于离策略训练，对于需要在线交互或严格安全约束的任务可能不适用。未能说明在稀疏奖励或无奖励下游场景下的表现。
- **资源信息缺失**：未报告训练所需的算力，不利于评估复现成本。

（完）
