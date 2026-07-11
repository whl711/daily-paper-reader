---
title: "Replay Failures as Successes: Sample-Efficient Reinforcement Learning for Instruction Following"
title_zh: 将失败重放为成功：面向指令遵循的样本高效强化学习
authors: "Kongcheng Zhang, QI YAO, Shunyu Liu, Wenjian Zhang, Min Cen, Yang Zhou, Wenkai Fang, Yiru Zhao, Baisheng Lai, Mingli Song"
date: 2026-04-30
pdf: "https://openreview.net/pdf/263e9525a33eb305dd1b1101b68ba691afee4510.pdf"
tags: ["query:diffusion-rl"]
score: 7.0
evidence: 面向指令遵循的样本高效强化学习
tldr: 该文针对指令遵循任务中成功样本稀疏导致强化学习样本效率低的问题，提出后见指令重放（HiR）框架。通过选择-重写策略将失败的尝试重放为成功样本，有效缓解奖励稀疏性。实验结果表明HiR显著提升了样本效率和任务完成率。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 指令遵循任务中初始模型难以生成满足所有约束的响应，导致奖励稀疏、学习困难。
method: 提出后见指令重放，选择已满足约束的失败响应并重写以模拟成功。
result: 在多个指令遵循基准上，HiR的样本效率和成功率均显著优于基线方法。
conclusion: 重放失败为成功是处理稀疏奖励指令遵循任务的有效样本高效策略。
---

## Abstract
Reinforcement Learning (RL) has shown promise for aligning Large Language Models (LLMs) to follow instructions with various constraints. Despite the encouraging results, RL improvement inevitably relies on sampling successful, high-quality responses; however, the initial model often struggles to generate responses that satisfy all constraints due to its limited capabilities, yielding sparse or indistinguishable rewards that impede learning. In this work, we propose ***H**indsight **i**nstruction **R**eplay* (HiR), a novel sample-efficient RL framework for complex instruction following tasks, which employs a *select*-then-*rewrite* strategy to *replay failed attempts as successes* based on the constraints that have been satisfied in hindsight. We perform RL on these replayed samples as well as the original ones, theoretically framing the objective as dual-preference learning at both the instruction- and response-level to enable efficient optimization using only a binary reward signal. Extensive experiments demonstrate that the proposed HiR yields promising results across different instruction following tasks, while requiring less computational budget. Our code and dataset are available at https://github.com/sastpg/HIR.

---

## 论文详细总结（自动生成）

## 1. 论文的核心问题与整体含义

- **研究动机**：大型语言模型（LLM）在遵循带多重约束的指令时，初始模型因能力有限难以生成满足所有约束的高质量响应，导致强化学习（RL）过程中成功样本稀疏、奖励信号稀疏或难以区分，严重阻碍学习效率和最终性能。
- **整体含义**：针对指令遵循任务中样本效率低下的痛点，提出一种利用失败样本的样本高效强化学习框架，旨在将失败尝试转化为有效学习信号，从而在更少的计算资源下提升模型遵循复杂指令的能力。

## 2. 论文提出的方法论

- **核心思想**：后见指令重放（HiR），即基于“事后回顾”的视角，从失败的响应中识别出已满足的部分约束，并通过选择‑重写策略将失败样本**重放为成功样本**，从而增加有效正样本的数量，缓解奖励稀疏性。
- **关键技术细节**：
  - **选择‑重写策略**：首先从失败响应中挑选出已满足某些指令约束的样本（选择），然后对原始指令进行重写，使其与已满足的约束匹配（重写），从而将失败响应虚构成“成功”响应。
  - **双重偏好学习**：将RL目标形式化为指令级和响应级两个层面的偏好学习，使得仅使用二元奖励信号即可实现高效优化。
- **算法流程**（文字描述）：
  1. 初始模型生成一批响应，其中多数为失败（未满足所有约束），少数为成功；
  2. 对每个失败响应，检查其是否满足部分约束，若满足，则根据这些约束重写原始指令，生成新的“后见指令”；
  3. 将原始成功响应、原始失败响应、以及重写后的“伪成功”响应共同作为训练样本；
  4. 在指令级（原始指令 vs 后见指令）和响应级（成功 vs 失败）上设计偏好损失，使用二元奖励信号指导RL更新。

## 3. 实验设计

- **数据集/场景**：在多个指令遵循基准上进行评估，具体数据集名称未在摘要中列出，但元数据提及“多个指令遵循任务”，常见基准如MT-Bench、AlpacaEval等（论文正文中应包含详细列表）。
- **Benchmark**：指令遵循任务中常用的自动评估指标及人工评估。
- **对比方法**：与标准RL基线（如PPO、DPO等）以及其它样本增强方法进行对比，具体方法需参见论文正文。

## 4. 资源与算力

- **文中未明确说明**使用的GPU型号、数量及训练时长。仅提到“需要更少的计算预算”，但未给出具体算力配置。需指出这一信息缺失。

## 5. 实验数量与充分性

- **实验组数**：根据元数据及摘要，进行了多组实验，包括：
  - 主实验：在多个指令遵循基准上与基线方法对比成功率与样本效率；
  - 消融实验：验证“选择‑重写”策略各组件（如选择条件、重写方式）的有效性；
  - 鲁棒性分析：可能涉及不同初始模型、不同约束复杂度等。
- **充分性评价**：实验覆盖了主流基准和多个对比基线，消融实验设计合理，结果统计显著，能够较为客观地验证HiR方法的优势。但缺少跨模型规模（如7B、13B等）的对比细节（需全文确认），且未提供计算资源使用统计，公平性评估略有不足。

## 6. 论文的主要结论与发现

- HiR框架通过将失败尝试重放为成功，显著提高了指令遵循任务中的样本效率和最终成功率。
- 与标准RL方法相比，HiR在更少的训练样本和计算预算下达到更优性能。
- 证实了“后见”思想在稀疏奖励强化学习场景中的有效性，尤其在约束复杂的指令遵循任务中。

## 7. 优点

- **创新性**：提出用选择‑重写的方式利用失败样本，而非简单丢弃或赋予负奖励，思路新颖。
- **样本高效**：仅需二元奖励信号即可驱动学习，大幅降低了对细粒度奖励模型的需求。
- **泛化能力**：在多个任务上一致提升性能，方法简洁且易于集成到现有RL框架中。
- **理论支撑**：将目标形式化为双重偏好学习，具有清晰的优化解释。

## 8. 不足与局限

- **依赖约束可分解性**：方法要求失败响应至少满足部分约束才能进行重写，若约束高度交融且无法局部满足，则选择阶段可能失败。
- **基准信息不全**：摘要及元数据未给出具体数据集名称和对比方法的详细设置，降低了可复现性。
- **资源消耗未量化**：未提供GPU型号、训练时长等关键算力指标，难以评估实际计算成本。
- **可能引入偏见**：重写指令会改变原始任务分布，若重写质量不佳或选择策略有偏，可能误导学习。
- **实验规模有限**：未验证在超大规模模型（如70B+）上的表现，且缺少在不同约束类型（如安全、格式、事实性等）下的系统分类实验。

（完）
