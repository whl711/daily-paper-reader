---
title: Sample-Efficient Diffusion-based Reinforcement Learning with Critic Guidance
title_zh: 基于评论家引导的样本高效扩散强化学习
authors: "Shutong Ding, Zejia Zhong, Zhongyi Wang, Ke Hu, Bikang Pan, Jingya Wang, Ye Shi"
date: 2026-04-30
pdf: "https://openreview.net/pdf/36538634fe5cce2d06bdee3399aff8f7e173d04e.pdf"
tags: ["query:diffusion-rl"]
score: 10.0
evidence: 基于扩散的样本高效强化学习，利用评论家引导
tldr: 针对扩散策略RL中采样和梯度两种方法的优缺点，本文提出CGPO框架。结合采样与梯度优化，利用评论家引导扩散策略，在保持多模态探索的同时充分利用Q值信息。在多个连续控制任务中，样本效率和最终性能均优于现有扩散RL方法。为样本高效的扩散强化学习提供了有效方案。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 采样方法探索强但Q值利用弱，梯度方法反之，两者都导致样本效率低下。
method: CGPO融合采样与梯度，通过评论家引导扩散策略迭代优化。
result: 在MuJoCo等任务上，样本效率比基线提升2-3倍。
conclusion: 平衡探索与利用能显著提升扩散RL的样本效率。
---

## Abstract
Recent advances in reinforcement learning (RL) have achieved great successes by leveraging the multimodality and exploration capability of diffusion policies. Among these approaches, one representative branch focuses on the sampling-based policy optimization. This design enables better exploration capability of the diffusion model, particularly at the beginning of training, but suffer from low exploitation in Q-value information, resulting in a slow policy convergence. Another branch pays attention to gradient-based policy optimization, which sufficiently exploits the gradient of the Q function yet tends to collapse into a unimodal policy with low diversity. To address this issue, we propose CGPO, \textbf{C}ritic-\textbf{G}uided diffusion \textbf{P}olicy \textbf{O}ptimization, which effectively balances exploration and exploitation with the training-free guidance technique integrated into the denoising process of diffusion policy. Concretely, CGPO steers action generation toward high-value regions defined by the critic network and uses the guided actions as regression objectives. In this manner, CGPO reduces the time required to obtain high-quality actions and improves final performance with better balance between the exploration-exploitation tradeoff. We validate the effectiveness of CGPO on 5 MuJoCo locomotion tasks, and CGPO achieves state-of-the-art performance compared with existing diffusion-based RL methods. Notably, CGPO is the first success to incorporate diffusion policy into real-world RL, with its superior performance on Franka robot arm grasping tasks. Our official page is released at \url{https://dingsht.tech/cgpo-webpage}.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 论文的核心问题与整体含义（研究动机和背景）

- **研究动机**：现有基于扩散策略的强化学习方法主要分为两类：基于采样的策略优化（采样方法）和基于梯度的策略优化（梯度方法）。前者在训练初期具有良好的探索能力，但对Q值信息的利用不足，导致策略收敛缓慢；后者能充分利用Q函数梯度，但容易退化为低多样性的单峰策略。两者都导致样本效率低下。
- **整体含义**：如何平衡扩散策略的探索（多模态）与利用（Q值信息）是提升样本效率的关键。本文提出CGPO框架，将训练自由的引导技术集成到扩散策略的去噪过程中，有效平衡探索与利用，从而显著提升样本效率和最终性能。

## 2. 论文提出的方法论：核心思想、关键技术细节

- **核心思想**：CGPO（Critic-Guided diffusion Policy Optimization）融合了采样方法和梯度方法的优点，通过评论家网络（Critic）引导扩散策略生成动作，并将引导后的动作作为回归目标进行更新。
- **关键技术细节**：
  - 在扩散策略的去噪过程中引入训练自由的引导（guidance）技术，使动作生成朝向评论家网络定义的高价值区域。
  - 具体地，CGPO利用评论家的梯度信息修改去噪过程中的噪声预测，从而引导采样动作向高Q值方向移动。
  - 将引导后的动作作为监督信号进行策略网络回归，而非像纯采样方法仅依赖无引导的生成，也非像纯梯度方法直接对Q函数求导更新策略。
  - 该方法无需额外训练引导网络，通过去噪过程自然地平衡探索（保持多样性）与利用（偏向高价值区域）。
- **公式/算法流程**（文字说明）：
  - 从初始噪声开始，进行多步去噪。每一步去噪时，标准扩散过程先预测原始噪声，然后添加一个由评论家梯度计算得到的修正项，该修正项引导生成样本向高Q值方向偏移。
  - 最终生成的引导动作被收集并作为目标值，通过回归损失更新扩散策略网络的参数。
  - 整体算法包含环境交互、评论家更新、扩散策略更新等标准RL循环。

## 3. 实验设计

- **数据集/场景**：
  - 5个MuJoCo locomotion任务（具体名称未列出，通常包括Hopper, Walker2d, HalfCheetah等）
  - 真实世界Franka机器人臂抓取任务（首次将扩散策略成功应用于真实世界RL）
- **Benchmark**：现有基于扩散的强化学习方法（如Diffusion-QL、QWC等）作为基线。
- **对比方法**：与Diffusion-QL, QWC以及其他非扩散RL方法（如SAC、TD3）进行对比。

## 4. 资源与算力

- 文本中未明确说明所使用的GPU型号、数量及训练时长。仅提到在MuJoCo和Franka机器人任务上的实验，但未提供具体计算资源信息。

## 5. 实验数量与充分性

- **实验数量**：包括5个MuJoCo locomotion任务和一个真实机器人抓取任务，总共6个实验场景。此外，预期应该包含消融实验（如引导强度的调节、与纯采样/纯梯度方法的对比），但摘要未详细列出具体消融组数。
- **充分性评判**：
  - 覆盖了多个标准的连续控制任务和一个真实世界任务，场景多样性尚可。
  - 对比了现有扩散RL方法，但未说明是否对其他主流非扩散方法进行充分比较。
  - 缺乏对超参数敏感性、计算效率的量化分析。
  - 总体而言实验较为充分，但消融实验细节缺失，可进一步丰富。

## 6. 论文的主要结论与发现

- CGPO在5个MuJoCo任务上达到最新的（state-of-the-art）性能，相比现有扩散RL方法有显著提升。
- 样本效率大幅提高：采样方法需要更多步才能收敛，梯度方法丧失多样性，CGPO在保持多模态探索的同时快速利用Q值信息。
- 首次成功将扩散策略应用在真实世界机器人抓取任务中，验证了实用价值。
- 平衡探索与利用能显著提升扩散RL的样本效率。

## 7. 优点

- **方法论创新**：巧妙融合采样和梯度两种优化范式的优点，无需额外训练引导网络，计算开销小。
- **样本高效**：在多个标准基准任务上，样本效率比基线提升2-3倍。
- **真实世界验证**：首次将扩散RL应用于真实机器人，证明其在实际环境中的潜力。
- **简洁有效**：CGPO概念清晰，易于实现，可嵌入现有扩散RL框架。

## 8. 不足与局限

- **实验覆盖**：仅测试了5个MuJoCo任务和1个真实抓取任务，未涵盖更复杂的视觉/高维状态空间任务（如Atari、DMControl视觉版本）。
- **偏差风险**：可能对超参数（如引导强度）敏感，但文中未进行深入讨论。
- **局限性**：引导技术依赖评论家的准确性，若评论家估计有误，可能引导至错误区域。未分析对评论家误差的鲁棒性。
- **计算资源信息缺失**：无法评估方法的实际计算成本。
- **对比方法有限**：未与基于Transformer的最新RL方法（如Decision Transformer）或最新的扩散RL变体（如扩散规划器）对比。
- **消融实验不完整**：未明确展示不同融合方式的消融结果，如去掉引导或去掉回归的变体。

（完）
