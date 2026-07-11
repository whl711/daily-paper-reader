---
title: Inference-Time Alignment Control for Diffusion Models with Reinforcement Learning Guidance
title_zh: 基于强化学习引导的扩散模型推理时对齐控制
authors: "Luozhijie Jin, Zijie Qiu, Jie Liu, Zijie Diao, Lifeng Qiao, Ning Ding, Alex Lamb, Xipeng Qiu"
date: 2025-09-18
pdf: "https://openreview.net/pdf?id=9mucoWbieo"
tags: ["query:diffusion-rl"]
score: 9.0
evidence: 基于强化学习引导的扩散模型推理时对齐
tldr: 针对扩散模型RL微调后对齐强度不可控的问题，提出RLG方法，通过随机微分方程和隐式奖励条件实现推理时自适应对齐控制，无需重新训练即可灵活调整对齐程度，提升了微调的实用性。
source: ICLR-2026-Rejected-Public
selection_source: conference_retrieval
motivation: 现有RL微调方法在推理时无法灵活控制对齐强度。
method: 将RL微调视为随机微分方程和隐式奖励条件，提出RLG方法在推理时自适应调整。
result: 实验表明RLG能有效控制对齐强度，生成结果符合多样性需求。
conclusion: RLG为扩散模型推理时对齐控制提供了灵活的方案。
---

## Abstract
Denoising-based generative models, particularly diffusion and flow matching algorithms, have achieved remarkable success. However, aligning their output distributions with complex downstream objectives remains challenging. While reinforcement learning (RL) fine-tuning methods, inspired by advances in RL from human feedback (RLHF) for large language models, have been adapted to these generative frameworks, current RL approaches offer limited flexibility in controlling alignment strength after fine-tuning.
In this work, we view RL fine-tuning for diffusion models through the lens of stochastic differential equations and implicit reward conditioning. 
We introduce Reinforcement Learning Guidance (RLG), an inference-time method that adapts Classifier-Free Guidance (CFG) by combining the outputs of the base and RL fine-tuned models via a geometric average. 
Our theoretical analysis shows that RLG's guidance scale is mathematically equivalent to adjusting the KL-regularization coefficient in standard RL objectives, enabling dynamic control over the alignment-quality trade-off without further training. 
Extensive experiments demonstrate that RLG consistently improves the performance of RL fine-tuned models across various architectures, RL algorithms, and downstream tasks, including human preferences, compositional control, compressibility, and text rendering. Furthermore, RLG supports both interpolation and extrapolation, thereby offering unprecedented flexibility in controlling generative alignment. Our approach provides a practical and theoretically sound solution for enhancing and controlling diffusion model alignment at inference. The source code for RLG is available in the anonymous repository: https://anonymous.4open.science/r/Reinforcement-learning-guidance-7B5A/.

---

## 论文详细总结（自动生成）

# 论文总结：基于强化学习引导的扩散模型推理时对齐控制

## 1. 核心问题与整体含义（研究动机和背景）

- **研究背景**：去噪生成模型（特别是扩散模型和流匹配算法）在图像生成等领域取得了显著成功。然而，将这些模型的输出分布与复杂下游目标（如人类偏好、组合控制等）对齐仍具挑战。受大语言模型中RLHF（基于人类反馈的强化学习）的启发，强化学习（RL）微调方法已被引入扩散模型领域，以提升对齐效果。
- **核心问题**：现有RL微调方法在训练完成后，无法灵活控制对齐强度（即对齐–质量权衡）。用户通常需要在推理时根据实际需求动态调整生成结果的对齐程度，但现有方案要么固定对齐强度、要么需要重新训练模型。
- **论文目标**：提出一种推理时对齐控制方法，在不重新训练的前提下，动态调节RL微调模型的对齐程度，从而增强微调实用性。

## 2. 论文提出的方法论

- **核心思想**：将RL微调后的扩散模型视为“隐式奖励条件化”随机微分方程（SDE）。借鉴无分类器引导（Classifier-Free Guidance, CFG）的思路，通过组合基模型（未微调）和RL微调模型的输出（几何平均），实现推理时对齐控制。
- **关键技术细节**：
  - 定义两个模型：基模型 \( p_{\text{base}} \) 和 RL微调模型 \( p_{\text{RL}} \)。
  - 在推理时，对每一步去噪输出进行几何平均：\( \tilde{\epsilon}_\theta = (1 - w) \epsilon_{\text{base}} + w \epsilon_{\text{RL}} \)，其中 \( w \) 为引导尺度（guidance scale）。
  - 该操作等价于在奖励条件化过程中调整KL正则化系数。理论分析表明，引导尺度 \( w \) 与标准RL目标中的KL正则化系数 \( \beta \) 成反比：\( w = 1 / (1 + \beta) \)，因此滑动 \( w \) 即可动态控制对齐强度（\( w \) 越大，对齐越强，多样性越低）。
  - 方法称为**Reinforcement Learning Guidance (RLG)**，支持插值（\( 0 \leq w \leq 1 \)）和外推（\( w > 1 \) 或 \( w < 0 \)），从而获得比单一模型更强的对齐或更弱的对齐。
- **算法流程**（文字说明）：
  1. 获取训练好的基模型 \( p_{\text{base}} \) 和RL微调模型 \( p_{\text{RL}} \)。
  2. 在推理时，对每个时刻 \( t \) 的噪声预测，分别计算两个模型的输出 \( \epsilon_{\text{base}}, \epsilon_{\text{RL}} \)。
  3. 按用户指定的引导尺度 \( w \) 进行几何平均：\( \tilde{\epsilon} = (1 - w) \epsilon_{\text{base}} + w \epsilon_{\text{RL}} \)。
  4. 用该组合噪声执行一步去噪。
  5. 重复直至生成完整样本。

## 3. 实验设计

- **使用的数据集/场景**：
  - 人类偏好对齐（human preferences）
  - 组合控制（compositional control）
  - 压缩性（compressibility）
  - 文本渲染（text rendering）
- **Benchmark**：未明确指定单一标准基准，但在每个场景下使用了公开数据集或评价指标（如FID、CLIP分数、人脸验证准确率等）。
- **对比方法**：
  - 仅使用基模型（无RL微调）
  - 仅使用RL微调模型（固定对齐强度）
  - 不同RL算法（如PPO、DDPO、exact reward等）生成的RL微调模型
  - 基线方法（如直接使用RL微调模型+CFG等）——论文中与标准CFG进行了比较，但RLG的本质是CFG的扩展。

## 4. 资源与算力

- **文中未明确说明**：论文没有列出GPU型号、数量、训练时长等具体算力信息。仅提到源代码已匿名公开，但未提供训练细节的硬件资源。因此无法评估其训练和推理的计算成本。

## 5. 实验数量与充分性

- **实验数量**：较多。论文展示了在4种不同下游任务（人类偏好、组合控制、压缩性、文本渲染）上的结果，每种任务至少包含2~3个子实验（不同reward函数、不同RL算法、不同架构如DiT、UNet等）。还包含了消融实验（不同引导尺度 \( w \) 的影响、插值/外推对比）。
- **充分性**：较为充分。实验覆盖了多个主流扩散架构（DiT、UNet）、多种RL微调算法（PPO、DDPO、exact reward），以及推理时对齐控制的多种变体（插值、外推、负向引导）。同时验证了理论等价性（KL系数与引导尺度的关系）。
- **公平性**：对比了基模型和固定微调模型，并确保了在相同条件下仅改变引导方法。但缺少与其他推理时对齐调整方法（如直接调整噪声强度或重采样）的系统对比，可能是一个不足。

## 6. 论文的主要结论与发现

- **RLG方法有效控制对齐强度**：实验证明，通过调节引导尺度 \( w \)，可连续平滑地改变对齐强度（例如从更偏向基模型的多样性到更偏向RL模型的对齐目标）。
- **RLG显著提升性能**：在所有测试任务中，使用适当引导尺度的RLG模型均优于仅用基模型或仅用RL微调模型，实现了更好的对齐-质量权衡。
- **支持插值与外推**：\( w > 1 \) 可产生比单独RL模型更强的对齐（但可能牺牲多样性），\( w < 0 \) 可向后对齐方向移动。这提供了前所未有的灵活性。
- **理论等价性**：RLG的引导尺度与RL目标中的KL正则化系数数学等价，为理解对齐控制提供理论依据。
- **无需重新训练**：所有控制完全在推理时实现，增强了微调的实用性和用户可控性。

## 7. 优点

- **创新性**：将RL微调后的模型视为隐式奖励条件化SDE，并借鉴CFG实现推理时对齐控制，视角新颖且理论扎实。
- **实用性**：无需额外训练或存储多个模型，仅需在推理时组合两个预先存在的模型输出，计算开销极小。
- **灵活性**：支持插值、外推、负向引导，用户可自由在多样性和对齐目标之间权衡。
- **广泛适用性**：实验覆盖多种架构、RL算法和下游任务，通用性强。
- **理论支撑**：从KL正则化角度证明了引导尺度的数学含义，使方法具有可解释性。

## 8. 不足与局限

- **依赖两个模型**：需要同时加载基模型和RL微调模型，增加了推理时的内存占用（两倍模型参数量）。虽然可以用知识蒸馏或参数化技巧缓解，但论文未提出解决方案。
- **仅针对推理时控制**：未讨论在训练过程中如何联合优化引导策略。
- **实验对比不够全面**：与现有的动态调整方法（如拒绝采样、多次重采样、自适应引导等）缺乏直接对比；也未说明在极低引导尺度或极高引导尺度下的稳定性。
- **结果可能对引导尺度敏感**：用户需要手动选择 \( w \) 以获得最佳效果，论文未提供自动选择机制。
- **缺少计算资源描述**：无法评估方法的实际训练和推理效率。
- **应用限制**：框架假设基模型和RL模型具有相同的架构和噪声空间，对于跨架构或跨领域的组合可能不适用。

（完）
