---
title: "DiFFPO: Training Diffusion LLMs to Reason Fast and Furious via Reinforcement Learning"
title_zh: DiFFPO：通过强化学习训练扩散大语言模型以快速且凶狠地推理
authors: "Hanyang Zhao, Dawen Liang, Wenpin Tang, David Yao, Nathan Kallus"
date: 2025-09-18
pdf: "https://openreview.net/pdf?id=ckUU5XySLn"
tags: ["query:diffusion-rl"]
score: 4.0
evidence: 应用于扩散语言模型的强化学习，非标准的扩散强化学习控制任务
tldr: 该方法提出了DiFFPO框架，利用离策略强化学习训练扩散语言模型，通过两阶段似然近似和重要性采样校正提升样本效率与推理速度，主要针对语言推理任务，与机器人或连续控制领域的扩散强化学习不直接相关。
source: ICLR-2026-Rejected-Public
selection_source: conference_retrieval
motivation: 现有扩散语言模型训练缺乏推理效率优化。
method: 提出离策略强化学习框架，结合两阶段似然近似与重要性采样。
result: 在数学推理任务上性能与效率提升。
conclusion: 强化学习可有效提升扩散语言模型的推理能力。
---

## Abstract
We propose **DiFFPO**, **Di**ffusion **F**ast and **F**urious **P**olicy **O**ptimization, a unified framework for training masked diffusion large language models (dLLMs) to reason not only *better (furious)*, but also *faster* via reinforcement learning (RL). We first generalize the existing baseline approach such as d1 by proposing to train surrogate policies via off-policy RL, whose likelihood is much more tractable as an approximation to the true dLLM policy. This naturally motivates a more accurate two-stage likelihood approximation combined with importance sampling correction, which leads to generalized RL algorithms with better sample efficiency and superior task performance. Second, we propose a new direction of training efficient samplers/controllers of dLLMs policy. Via RL, we incentivize dLLMs' natural multi-token prediction capabilities by letting the model learn to adaptively allocate an inference threshold for each prompt, which yields better accuracies with lower number of function evaluations (NFEs) compared to the base model. Finally, we consider joint training of the dLLM policy and the sampler together to obtain the best performance in improving the Pareto frontier of the inference-time compute of dLLMs. We showcase the effectiveness of our pipeline by training open source large diffusion language models over math and planning benchmark tasks.

---

## 论文详细总结（自动生成）

# DiFFPO：通过强化学习训练扩散大语言模型以快速且凶狠地推理——论文总结

## 1. 论文的核心问题与整体含义（研究动机和背景）
- **问题**：现有的掩码扩散大语言模型（dLLMs）在推理时效率较低，缺乏针对推理速度与准确率的联合优化方法。
- **动机**：已有的基线方法（如 d1）仅关注推理质量的提升，未充分利用强化学习（RL）来同时优化推理速度和正确性，且其似然计算困难、样本效率低。
- **整体含义**：作者提出 **DiFFPO**（Diffusion Fast and Furious Policy Optimization）框架，旨在通过离策略强化学习训练 dLLMs，使其不仅能推理得更好（更凶狠），还能推理得更快，实现推理时计算帕累托前沿的改进。

## 2. 论文提出的方法论：核心思想、关键技术细节
- **核心思想**：将扩散语言模型的推理过程视为一个强化学习问题，通过离策略学习提高样本效率，并引入自适应推理步数控制来加速推理。
- **关键技术细节**：
  - **离策略 RL 训练**：首先推广基线方法（如 d1），提出训练代理策略（surrogate policy），其似然计算比真实 dLLM 策略更易处理。
  - **两阶段似然近似与重要性采样校正**：在第一阶段使用近似似然，第二阶段用重要性采样（Importance Sampling）进行校正，从而得到更准确的估计，提升样本效率和任务性能。
  - **高效采样器/控制器训练**：利用 RL 激励 dLLM 的多 token 预测能力，使模型为每个提示自适应地分配推理阈值（inference threshold），在保持准确率的同时降低函数评估次数（NFEs）。
  - **联合训练**：将 dLLM 策略与采样器联合优化，以在推理时计算帕累托前沿上获得最佳表现。

## 3. 实验设计：数据集、基准、对比方法
- **数据集与场景**：在数学推理和规划（math and planning benchmark tasks）基准任务上进行实验。具体数据集名称未在摘要中说明。
- **基准（Benchmark）**：采用开放源码的大型扩散语言模型进行训练和评估，但未列出具体基准名称。
- **对比方法**：主要与基线方法（如 d1）进行对比，但未提供更详细的对比方法列表。

## 4. 资源与算力
- **未明确说明**：摘要及提供的元数据中未提及 GPU 型号、数量、训练时长等算力信息。可能正文中有更详细描述，但当前提取内容不包含。

## 5. 实验数量与充分性
- **实验数量**：仅从摘要无法判断具体实验组数。提到在 math 和 planning 两个任务上进行了实验，但没有给出消融实验、超参数敏感性分析等细节。
- **充分性评估**：由于缺乏详细实验设置（如数据集规模、重复次数、统计显著性），难以判断实验是否充分、客观、公平。元数据中该论文为 ICLR 2026 Rejected，可能审稿人对实验充分性存在质疑。

## 6. 论文的主要结论与发现
- RL 可以有效提升扩散语言模型的推理能力（更好）和推理速度（更快）。
- 提出的两阶段似然近似与重要性采样校正方法相比基线具有更好的样本效率和任务性能。
- 自适应推理阈值分配能够以更少的前向传播次数获得更好的准确率。
- 联合训练策略与采样器可以在推理时计算上获得更优的帕累托前沿。

## 7. 优点
- **方法创新**：首次将离策略强化学习用于扩散语言模型，结合两阶段似然近似与重要性采样，解决了似然计算困难问题。
- **双目标优化**：同时关注推理质量与速度，提出自适应推理控制，具有实际应用价值。
- **通用框架**：可应用于开源大型扩散语言模型，具有一定泛化性。

## 8. 不足与局限
- **实验覆盖不足**：仅提及在 math 和 planning 任务上的实验，缺乏对更多领域（如常识推理、自然语言理解）的验证。
- **偏差风险**：未讨论自适应推理阈值的鲁棒性、不同提示分布下的性能变化。
- **应用限制**：当前结果基于开放源码模型，但未与更强大的自回归 LLM（如 GPT-4）对比，实际推理速度优势可能有限。
- **算力未报告**：缺少训练成本信息，难以评估方法的经济可行性。
- **论文状态为 Rejected**：可能审稿人认为贡献或实验尚存不足。

（完）
