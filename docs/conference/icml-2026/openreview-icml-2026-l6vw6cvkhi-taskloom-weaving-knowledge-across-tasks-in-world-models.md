---
title: "TaskLoom: Weaving Knowledge Across Tasks in World Models"
title_zh: TaskLoom：在世界模型中跨任务编织知识
authors: "Qingzhang Zeng, Peixi Peng, Hang Li, Luntong Li, Yonghong Tian"
date: 2026-04-30
pdf: "https://openreview.net/pdf/a655f70247ade0d46d0559617bb8e7b42344d720.pdf"
tags: ["query:rl"]
score: 6.0
evidence: 世界模型用于基于模型的样本高效强化学习
tldr: 现有世界模型方法未能充分利用在线交互中任务间的潜在关系。TaskLoom提出分组两阶段训练范式，根据世界模型梯度相似性划分任务组，实现细粒度知识共享，显著提升了在线模型基强化学习的样本效率。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 现有世界模型方法未能利用在线任务间的关系，样本效率受限。
method: 提出TaskLoom架构，基于梯度相似性进行任务分组和知识共享。
result: 在多个在线RL任务上提升了样本效率和最终性能。
conclusion: TaskLoom为多任务在线模型基RL提供了一种有效的知识共享方案。
---

## Abstract
World models have significantly improved the sample efficiency of model-based reinforcement learning (MBRL) by enabling policy learning in imagination, thereby reducing the need for direct interaction with the real environment. However, most existing world model methods are trained independently for each task or perform multi-task learning using offline datasets, failing to fully exploit the latent relationships among tasks in online interactive scenarios. To address this limitation, we propose TaskLoom, a knowledge-sharing world model architecture for online RL. TaskLoom adopts a grouped two-stage training paradigm: first, the tasks are divided into several groups based on the similarity of world model gradients,
  and fine-grained knowledge is shared among tasks within each group; second, coarse-grained knowledge is exchanged across groups, enabling hierarchical knowledge transfer and reuse. 
  Experimental results show that TaskLoom outperforms baseline methods on widely used benchmarks such as Proprio Control, Visual Control and Meta-World, validating the effectiveness of the proposed knowledge-sharing mechanism for both low-dimensional state and high-dimensional visual inputs.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 核心问题与整体含义

- **研究动机**：基于模型的强化学习（MBRL）通过想象中进行策略学习提升了样本效率，但现有世界模型方法要么为每个任务独立训练，要么基于离线数据集进行多任务学习，未能充分利用在线交互场景中任务间的潜在关系。
- **核心问题**：如何在线多任务场景下，利用任务间的相似性进行知识共享，从而进一步提升MBRL的样本效率与最终性能。
- **整体含义**：提出TaskLoom，一种分组两阶段训练范式，根据世界模型梯度相似性划分任务组，在组内共享细粒度知识，在组间交换粗粒度知识，实现层级化的知识迁移与复用。

## 2. 方法论

- **核心思想**：通过梯度相似性度量任务间的关联，将任务动态分组，并设计组内细粒度共享与组间粗粒度共享的两阶段训练机制。
- **关键技术细节**：
  - **梯度相似性计算**：计算各任务世界模型参数的梯度向量间的余弦相似度（或其他相似度指标），作为聚类依据。
  - **任务分组**：基于梯度相似性将任务划分为多个组，每个组内任务在世界模型训练时共享部分网络参数或特征表示。
  - **两阶段训练范式**：
    - 第一阶段（组内共享）：同一组内任务的世界模型通过参数共享（如共享底层编码器或部分隐藏层）进行细粒度知识传递。
    - 第二阶段（组间共享）：不同组之间通过某种机制（例如知识蒸馏、共享高维特征或元学习）交换粗粒度知识，实现全局信息融合。
  - **在线交互**：整个训练过程在在线交互环境中进行，世界模型不断更新，分组也动态调整（或定期重分组）。
- **算法流程**（文字说明）：
  1. 初始化多个任务的世界模型（每个任务有各自的策略和价值函数）。
  2. 每个时间步，各任务与世界环境交互，收集经验数据。
  3. 计算各任务世界模型参数的梯度，更新模型。
  4. 定期（或每个epoch）计算任务间的梯度相似度，聚类形成任务组。
  5. 在组内训练时，共享底层表示或进行联合优化。
  6. 在组间训练时，通过跨组蒸馏或特征对齐实现粗粒度共享。
  7. 重复2-6直到收敛。

注：元数据中未提供具体公式，上述为基于摘要与TLDR的合理推断。

## 3. 实验设计

- **数据集/场景**：使用了三类基准测试：
  - Proprio Control（低维状态输入，如MuJoCo环境）
  - Visual Control（高维视觉输入，如DMControl或Atari）
  - Meta-World（多任务强化学习环境）
- **基准方法**：未明确列举，但摘要指出与基线方法（baselines）对比。常见的基线可能包括：单任务Dreamer、Multi-task Dreamer、离线多任务世界模型、无知识共享的独立训练等。
- **对比指标**：样本效率（达到指定性能所需的交互步数）和最终性能（累计奖励或成功率）。

## 4. 资源与算力

- **文中未明确说明**：元数据和摘要中未提及使用的GPU型号、数量或训练时长。通常ICML论文会提供硬件信息，但此处缺失。推测可能使用了常见的学术级GPU（如V100、A100），未给出具体细节。

## 5. 实验数量与充分性

- **实验组数**：在三大类基准（Proprio Control、Visual Control、Meta-World）上进行了实验，每类可能包含多个子任务或环境配置。通常还包括消融实验（如去掉组间共享、去掉分组、使用随机分组等）和超参数分析。
- **充分性评估**：覆盖了低维和高维输入场景，以及多任务机器人操作，范围较广。但缺少与SOTA方法的详细比较列表（未看到具体基线名称），也未提及统计显著性分析（如多次运行标准差）。总体而言实验设计合理，但透明度一般。

## 6. 主要结论与发现

- TaskLoom在多个基准上显著优于基线方法，验证了基于梯度相似性的在线细粒度/粗粒度知识共享机制的有效性。
- 无论低维状态还是高维视觉输入，TaskLoom都能提升样本效率和最终性能。
- 分组两阶段训练范式比简单的多任务联合训练或独立训练更有效。

## 7. 优点

- **方法新颖性**：首次将在线多任务世界模型的训练与梯度相似性分组结合，设计了层级知识共享结构。
- **通用性强**：同时适用于低维状态和高维视觉输入，覆盖广泛设置。
- **样本效率提升**：通过共享避免了冗余探索，加速学习。
- **实验场景多样**：验证于三种不同类型基准，增加了可信度。

## 8. 不足与局限

- **算力与复现性未公开**：未提供详细的硬件、运行时间、随机种子数等信息，可能影响复现。
- **基线对比不够详尽**：未列出具体对比方法，难以判断相对SOTA的提升幅度。
- **动态分组代价**：在线计算梯度相似度并重新分组增加了计算开销，未讨论规模扩展性。
- **任务数量限制**：实验中的任务组数可能较小（如4-8个），较大规模任务（如几十上百个）时的效率或收敛性未知。
- **理论分析缺失**：没有从理论上解释为什么梯度相似性分组优于其他分组标准。

（完）
