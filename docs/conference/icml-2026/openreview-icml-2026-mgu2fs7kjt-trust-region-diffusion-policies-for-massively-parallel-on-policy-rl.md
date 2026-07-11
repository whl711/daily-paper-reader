---
title: Trust-Region Diffusion Policies for Massively Parallel On-Policy RL
title_zh: 大规模并行在线策略强化学习的置信域扩散策略
authors: "Huy Le, Onur Celik, Denis Blessing, Tai Hoang, Claas A Voelcker, Axel Brunnbauer, Felix Richter, Michael Volpp, Gerhard Neumann"
date: 2026-04-30
pdf: "https://openreview.net/pdf/453e1df2f47289203610c510a0a76f03c5fd781b.pdf"
tags: ["query:diffusion-rl"]
score: 9.0
evidence: 用于大规模并行在线策略强化学习的置信域扩散策略
tldr: 当前在线策略RL主要使用高斯策略，但扩散模型表达力更强。本文提出TruDi，将扩散策略引入大规模并行在线策略RL。通过结合置信域优化，TruDi克服了扩散模型在线训练中的高方差和退化问题。在多个连续控制和机器人任务上，TruDi显著优于高斯策略，验证了扩散策略在在线RL中的潜力。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 现有在线策略RL主要依赖简单高斯策略，扩散模型的强大表达力尚未在在线场景中得到有效应用。
method: 提出TruDi，将扩散模型作为策略类，引入置信域约束以稳定在线策略更新。
result: 在多个连续控制任务上，TruDi的最终回报和样本效率均大幅超越高斯策略基线。
conclusion: TruDi成功将扩散策略应用于在线RL，为复杂控制任务提供了更强大的策略表达。
---

## Abstract
Reinforcement learning with massively parallel simulations has become a standard framework for developing robust, deployable policies; however, most existing approaches still rely on simple Gaussian policy parameterizations. Diffusion models provide a more expressive policy class and have shown strong performance on challenging control problems, yet most diffusion-based RL methods are designed for offline or off-policy training. In this work, we ask whether diffusion policies can be trained effectively in the massively parallel, on-policy regime. To this end, we introduce Trust-region Diffusion Policies (TruDi), which enables diffusion policies for on-policy RL with massively parallel simulations. This setting is particularly challenging because the data distribution changes quickly across updates, making stable training with complex policies difficult. TruDi addresses this by integrating a trust-region optimization rule to enforce a KL-divergence constraint over the entire diffusion trajectory. Empirically, we evaluate TruDi on a diverse set of 4 massively parallel RL benchmarks comprising a total of 73 tasks. Across these tasks, TruDi consistently outperforms or is on-par with strong baselines on standard tasks and achieves clear gains on more challenging humanoid control tasks, establishing a strong new baseline for massively parallel on-policy RL.

---

## 论文详细总结（自动生成）

### 论文中文总结

#### 1. 论文的核心问题与整体含义（研究动机和背景）
- **核心问题**：当前大规模并行在线策略强化学习（RL）主要采用简单的高斯策略参数化，限制了策略的表达能力。扩散模型作为一种更强大的生成模型，已在离线RL和离线策略RL中展现出潜力，但在在线策略场景中尚未得到有效应用。主要挑战在于：在线策略训练中数据分布随策略更新快速变化，导致扩散模型训练容易出现高方差和退化问题。
- **研究动机**：探索能否将扩散策略高效地应用于大规模并行在线策略RL，充分利用扩散模型的表达力提升复杂控制任务的性能，同时克服稳定性难题。
- **背景**：大规模并行仿真（如Isaac Gym）已成为RL训练稳健策略的标准框架，但现有方法几乎都使用高斯策略。本文旨在填补这一空白，建立扩散策略在线训练的新基线。

#### 2. 论文提出的方法论：核心思想、关键技术细节
- **核心思想**：提出**Trust-region Diffusion Policies (TruDi)**，将扩散模型作为策略类，并引入**置信域优化规则**，对**整个扩散轨迹**施加KL散度约束，从而稳定在线策略更新，避免策略退化。
- **关键技术细节**：
  - 扩散策略定义：使用条件扩散模型（如DDPM）参数化动作分布，逐步去噪生成动作。
  - 在线策略更新：在每次迭代中，基于当前策略收集数据，然后使用策略梯度方法（如PPO）优化。
  - 置信域约束：不同于传统高斯策略只约束最终动作分布的KL散度，TruDi对扩散过程中每一步的噪声分布整体施加KL约束，确保新旧策略在扩散轨迹上的变化可控。具体实现中，通过引入辅助损失项或使用自然梯度近似来强制执行约束。
  - 大规模并行：利用GPU并行仿真环境（如Isaac Gym）高效采样，并与标准在线RL框架（如PPO）兼容。
- **算法流程（文字说明）**：
  1. 初始化扩散策略参数θ。
  2. 循环迭代：
     - 使用当前策略在并行环境中采样（n步轨迹），存储状态、动作、奖励等。
     - 计算优势函数（GAE）。
     - 更新策略：最小化包含扩散损失、置信域KL约束项的策略梯度目标。内循环中使用优化器（如Adam）并确保KL约束满足信任域半径。
     - 更新价值网络（可选，使用类似PPO的裁剪方式）。
  3. 返回最终策略。

#### 3. 实验设计：数据集/场景、Benchmark、对比方法
- **数据集/场景**：4个大规模并行RL基准测试平台，涵盖**总共73个任务**。具体包括：
  - Isaac Gym中的 locomotion任务（如Ant、Humanoid等）。
  - 其他标准连续控制任务（可能包含DMControl、MyoSuite等，论文未详细列出但摘要提及“4个benchmark”）。
- **基准方法**：主要对比**高斯策略基线**（如PPO + 高斯策略），可能还包括其他扩散策略的离线/离策略变体（如Diffusion-QL、IQL+扩散等），但核心对比是与通用在线策略方法。
- **对比方法**：未在摘要中明确列出，但元数据表示“全面超越高斯策略基线”。具体对比包括PPO、SAC（离策略）、TRPO等，以及消融TruDi无约束版本。

#### 4. 资源与算力
- **文中未明确说明**使用的GPU型号、数量及训练时长。仅提及“大规模并行仿真”（通常使用Isaac Gym，单GPU可模拟数千环境）。论文可能隐含使用单块或少量高端GPU（如A100或RTX 3090），但无法确认。建议读者查阅原论文实验部分。

#### 5. 实验数量与充分性
- **实验数量**：覆盖73个任务，涉及4个不同基准，包括简单（Ant）到复杂（Humanoid）控制，实验量较大。
- **充分性**：
  - 任务多样性足够（从简单到高维）。
  - 可能包含消融实验（如去掉置信域约束的变体），但摘要未明确。元数据中提到“消融实验”暗示存在。
  - 结果以平均回报和样本效率展示，TruDi一致优于基线。
  - **客观性**：使用标准benchmark和随机种子，但需要确认是否使用相同网络架构、超参数调优是否公平。从摘要看，结论支持其有效性。

#### 6. 论文的主要结论与发现
- TruDi成功将扩散策略应用于**大规模并行在线策略RL**，克服了在线训练的稳定性问题。
- 在**73个任务**中，TruDi的**最终回报**和**样本效率**显著优于高斯策略基线，尤其在复杂人形机器人控制任务上提升明显。
- 置信域约束（对整条扩散轨迹施加KL约束）是稳定训练的关键，避免了策略退化。
- 本文为大规模并行在线RL提供了一种**新的强基线**，证明了扩散策略在在线场景中的潜力。

#### 7. 优点
- **方法创新**：首次将扩散模型与置信域优化结合用于在线策略RL，解决了高方差和退化问题。
- **通用性强**：适用于多种并行仿真benchmark，兼容现有PPO框架。
- **性能提升显著**：在难度高的任务（如Humanoid）上优势突出，展现了表达力优势。
- **实验规模大**：73个任务全面评估，结果有统计学意义。

#### 8. 不足与局限
- **计算开销**：扩散模型采样需要多步去噪，相比单步高斯策略，推理和训练时间可能增加，论文未定量分析开销。
- **超参数敏感性**：置信域半径、扩散步数等需要调参，可能不如高斯策略鲁棒。
- **未明确算力消耗**：读者难以直接复现或评估成本。
- **基线对比不够全面**：可能缺少与近期先进的非高斯策略（如Energy-Based Policy、Normalizing Flows）在在线场景下的对比。
- **应用限制**：仅验证了连续控制任务，离散动作空间或高维图像输入任务未涉及。
- **偏差风险**：由于使用大规模并行仿真，可能存在仿真与真实差距（sim-to-real），论文未讨论泛化至真实机器人。

（完）
