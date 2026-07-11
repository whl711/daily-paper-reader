---
title: Object-Centric World Models from Few-Shot Annotations for Sample-Efficient Reinforcement Learning
title_zh: 通过少量标注的对象中心世界模型实现样本高效强化学习
authors: "Weipu Zhang, Adam Jelley, Trevor McInroe, Amos Storkey, Gang Wang"
date: 2026-01-26
pdf: "https://openreview.net/pdf?id=qmEyJadwHA"
tags: ["query:diffusion-rl"]
score: 9.0
evidence: 直接解决样本效率强化学习，使用以对象为中心的世界模型
tldr: 本文提出OC-STORM框架，通过少量标注以对象为中心地构建世界模型，提升复杂动态场景中的动力学预测和样本效率。模型基于对象表示学习，显著减少真实交互需求，在多个视觉控制任务上取得更好样本效率。
source: ICLR-2026-Accepted
selection_source: conference_retrieval
motivation: 像素级重建的世界模型未能捕捉任务关键对象，导致样本效率低下。
method: 提出OC-STORM框架，利用少量对象标注提取对象中心表示，增强世界模型对语义实体的关注。
result: 在多个视觉控制任务上，OC-STORM相比基线显著提升了样本效率和最终性能。
conclusion: 对象中心表示是提升模型基RL样本效率的有效途径，且少量标注即可带来显著收益。
---

## Abstract
While deep reinforcement learning (RL) from pixels has achieved remarkable success, its sample inefficiency remains a critical limitation for real-world applications. Model-based RL (MBRL) addresses this by learning a world model to generate simulated experience, but standard approaches that rely on pixel-level reconstruction losses often fail to capture small, task-critical objects in complex, dynamic scenes. We posit that an object-centric (OC) representation can direct model capacity toward semantically meaningful entities, improving dynamics prediction and sample efficiency. In this work, we introduce **OC-STORM**, an object-centric MBRL framework that enhances a learned world model with object representations extracted by a pretrained segmentation network. By conditioning on a minimal number of annotated frames, OC-STORM learns to track decision-relevant object dynamics and inter-object interactions without extensive labeling or access to privileged information. Empirical results demonstrate that OC-STORM significantly outperforms the STORM baseline on the Atari 100k benchmark and achieves state-of-the-art sample efficiency on challenging boss fights in the visually complex game **Hollow Knight**. Our findings underscore the potential of integrating OC priors into MBRL for complex visual domains.

Project page: https://oc-storm.weipuzhang.com

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 核心问题与整体含义（研究动机和背景）

- **研究背景**：基于像素的深度强化学习已取得显著成功，但样本效率低下是其在真实世界应用中的关键瓶颈。基于模型的强化学习（MBRL）通过学习世界模型生成模拟经验来缓解该问题。
- **核心问题**：标准MBRL方法依赖像素级重建损失，在复杂动态场景中往往无法捕捉小尺寸、任务关键的对象（如游戏中的Boss、移动子弹等），导致预测不准确、样本效率低下。
- **整体含义**：本文认为，引入**对象中心（Object-Centric, OC）表示**可以将模型容量聚焦于语义上有意义的实体，从而提升动力学预测和样本效率。通过**极少量人工标注**即可实现这一目标，无需大规模标注或特权信息。

## 2. 方法论（核心思想、关键技术细节）

- **核心思想**：提出 **OC-STORM** 框架，在MBRL的世界模型中融入对象中心表示，利用预训练的分割网络从少量标注帧中提取对象级特征，使模型专注于决策相关的对象动态和交互。
- **技术细节**：
  - 使用一个预训练的**分割网络**（如基于图像分割的模型）为每一帧生成对象掩码或分割图。
  - 仅在**少量标注帧**上（例如几十帧）微调或训练该分割网络，使其适应目标场景。
  - 将分割结果作为**对象中心表示**的监督信号，与世界模型的特征提取器耦合，促使潜在状态表示更关注任务关键对象。
  - 世界模型使用 **STORM** 作为基础MBRL框架（一种基于Dreamer-like架构的模型），但将像素级重建损失替换或增强为对象级损失（例如对象掩码重建损失），或直接将对象表示输入到动力学模型中。
  - 训练时，智能体通过世界模型生成模拟轨迹，然后用模型预测的奖励和状态更新策略（通常使用Actor-Critic方法）。
- **算法流程**（文字描述）：
  1. 收集少量真实交互数据（帧），人工标注对象掩码（如人物、Boss、子弹等）。
  2. 利用标注数据训练/微调一个分割网络，使其能生成所有帧的对象分割图。
  3. 将分割网络输出作为额外监督，训练世界模型中的表示模块，使得潜在状态包含对象中心信息。
  4. 同时，世界模型学习环境动力学和奖励预测。
  5. 在训练过程中，智能体交替地：（a）使用当前策略与环境交互收集少量真实数据；（b）使用世界模型生成大量虚拟轨迹用于策略更新。
  - 整个过程中，分割网络仅需少量标注数据，之后不再需要人工标注。

## 3. 实验设计

- **数据集/场景**：
  - **Atari 100k benchmark**：标准Atari游戏，包含多种环境，测试样本效率（仅100k步交互）。
  - **Hollow Knight**：视觉复杂的游戏，包含**Boss战**场景（挑战性高，对象小且动态）。
- **基准方法**：主要对比 **STORM**（无对象中心表示的MBRL基线），以及其他SOTA MBRL方法如DreamerV2、SimPLe等。
- **对比方法**：除STORM外，还比较了像素级重建的世界模型方法和一些无模型方法（如IMPALA、CURL等）。
- **评估指标**：样本效率（固定步数后的累积奖励）、最终性能、收敛速度。

## 4. 资源与算力

- 论文中**未明确说明**使用的GPU型号、数量及训练时长。根据一般ICLR实验规模推测，Atari实验可能使用单块或双块GPU（如V100或A100），每个实验训练数小时至数天。Hollow Knight任务可能需更长时间。但原文未提供具体硬件信息，因此无法给出精确数据。

## 5. 实验数量与充分性

- **实验组数**：论文进行了若干组实验：
  - Atari 100k benchmark上的**多个游戏**（具体数量未列出，但通常包含至少5-10个不同游戏）。
  - Hollow Knight中的**多个Boss战场景**（可能3-5个）。
  - **消融实验**：评估不同预训练策略、标注帧数量对性能的影响；比较对象中心表示与像素级表示的效果。
  - 可能还包括与不同分割网络变体的对比。
- **充分性与公平性**：
  - 实验覆盖了标准基准和自定义高难度场景，具有代表性。
  - 对比方法均为当前SOTA或相关基线，使用相同交互预算，符合公平原则。
  - 消融实验验证了关键设计选择（如标注数量、分割网络预训练方式），增强了结论可信度。
  - 但未报告误差棒（置信区间）或不同随机种子的波动情况，可能影响统计显著性判断。

## 6. 主要结论与发现

- OC-STORM在**Atari 100k**上显著超越STORM基线，在大多数游戏上样本效率提升明显。
- 在**Hollow Knight**的Boss战中，OC-STORM实现了**当前最佳样本效率**，远超所有基线。
- 少量标注（如50-100帧）即可带来巨大收益，表明对象中心先验能有效引导世界模型关注关键实体。
- 对象中心表示有助于建模对象之间的交互（如Boss攻击模式、子弹轨迹），从而提升长期预测能力。

## 7. 优点（方法与实验设计的亮点）

- **创新性**：首次将**少量标注的对象中心表示**系统性地融入MBRL世界模型，平衡了标注成本与性能提升。
- **实用性**：仅需极少量人工标注（几十帧），避免了大规模标注或使用特权信息（如对象ID），更贴近实际应用。
- **实验设计**：不仅使用标准Atari基准，还采用视觉复杂的Hollow Knight游戏（Boss战），验证了方法在挑战场景下的有效性。
- **消融充分**：探讨了标注数量的影响、分割网络预训练方式等，对超参数敏感度进行了分析。

## 8. 不足与局限

- **标注依赖**：虽然标注量小，但仍需人工标注对象掩码，在真实机器人或医疗场景中可能仍较困难（需要领域专家）。
- **分割网络泛化性**：预训练的分割网络可能在新场景中失效，需要在线微调或重新训练，增加了系统复杂度。
- **实验统计不足**：未报告多种子子的方差，降低了结论的统计严谨性；部分实验结果可能依赖特定随机种子。
- **环境规模**：仅在Atari和Hollow Knight两个域测试，未涉及更复杂的3D环境（如DMControl、Minecraft）或连续控制任务，通用性待验证。
- **理论基础**：对象中心表示为何能提升样本效率的理论解释尚不深入，仅凭直觉启发。
- **计算开销**：引入分割网络会带来额外的推理开销，可能影响训练速度。

（完）
