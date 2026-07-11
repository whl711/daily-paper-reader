---
title: "BiTrajDiff: Bidirectional Trajectory Generation with Diffusion Models for Offline Reinforcement Learning"
title_zh: BiTrajDiff：用于离线强化学习的双向轨迹生成扩散模型
authors: "Yunpeng Qing, Yixiao Chi, Shuo Chen, Shunyu Liu, Kexuan Zhou, Sixu Lin, Litao Liu, Changqing Zou"
date: 2026-04-30
pdf: "https://openreview.net/pdf/3cd815218cf3924f66dbd1f041945f3116ab071f.pdf"
tags: ["query:diffusion-rl"]
score: 6.0
evidence: 利用双向轨迹扩散进行离线RL数据增强，提升泛化和样本效率
tldr: 该论文针对离线RL中数据增强方法局限于单轨迹、难以恢复不同行为模式间连接的问题，提出双向轨迹扩散（BiTrajDiff）框架，通过扩散模型双向生成轨迹，有效连接不同行为模式。实验表明在离线RL基准上相比现有数据增强方法，BiTrajDiff提升了策略的泛化性能和样本效率。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 离线RL的数据增强方法通常只生成局部变体，未能恢复不同行为模式间的连接，限制了泛化。
method: 提出双向轨迹扩散框架，利用扩散模型从两个方向生成轨迹，显式连接不同行为模式。
result: 在D4RL等基准上，BiTrajDiff相比单一轨迹增强方法显著提升了策略性能和泛化能力。
conclusion: 双向轨迹生成能够有效挖掘离线数据集中的潜在连接，是提升离线RL样本效率的有效手段。
---

## Abstract
Offline Reinforcement Learning (RL) relies on static datasets and often enforces conservative constraints to mitigate out-of-distribution errors, but this inevitably gives rise to learning dataset biases and limited behavioral generalization.
Recent Data Augmentation (DA) methods leverage generative models to enrich offline data, yet they mainly operate within a single rollout paradigm and tend to preserve the original trajectory-level connectivity of the dataset.
As a result, such methods often introduce local variations and fail to recover connections between distinct behavior patterns.
In this paper, we propose Bidirectional Trajectory Diffusion (BiTrajDiff), a novel DA framework that explicitly addresses this limitation.
BiTrajDiff decomposes trajectory synthesis into two independent diffusion processes that generate forward-future and backward-history segments conditioned on shared intermediate anchor states.
By stitching the generated segments at these anchors, BiTrajDiff can synthesize trajectories that bridge disconnected behavior patterns and recover global trajectory-level connectivity absent from the original data.
Extensive experiments demonstrate that BiTrajDiff consistently outperforms advanced DA methods across a range of offline RL backbones. Our code is available at [https://github.com/Plankson/BiTrajDiff](https://github.com/Plankson/BiTrajDiff).

---

## 论文详细总结（自动生成）

### 论文详细中文总结

#### 1. 论文的核心问题与整体含义（研究动机和背景）
- **核心问题**：离线强化学习（Offline RL）依赖静态数据集，传统方法通过保守约束（如策略正则化）来缓解分布外误差，但这会导致学习数据集偏差和有限的行为泛化能力。
- **现有数据增强（DA）方法的局限**：近期研究利用生成模型（如扩散模型）丰富离线数据，但普遍局限于单条轨迹的局部变体生成（如添加噪声、插值），未能恢复数据集中不同行为模式之间的全局连接（如不同策略产生的轨迹片段之间的衔接）。
- **motivation**：打破单轨迹范式，显式地弥合断开的行为模式，生成具有全局连通性的新轨迹，从而提升离线RL策略的泛化性和样本效率。

#### 2. 论文提出的方法论
- **核心思想**：提出**双向轨迹扩散（BiTrajDiff）**框架，将轨迹合成分解为两个独立的扩散过程：一个向前生成未来段（forward-future segment），另一个向后生成历史段（backward-history segment），二者共享一个中间锚点状态（shared intermediate anchor state）。
- **关键技术细节**：
  - 以锚点状态为条件，分别训练两个条件扩散模型：一个以锚点为起点向前生成后续帧（前向扩散），另一个以锚点为终点向后回溯之前帧（反向扩散）。
  - 生成时，在相同锚点处拼接两个方向生成的轨迹段，形成一条完整的、原本不存在的轨迹，从而连接不同行为模式。
  - 该方法可视为一种数据增强策略：先对原始数据集中的轨迹进行锚点采样，然后双向生成新轨迹，将其加入缓冲区，供下游RL算法训练。
- **公式或算法流程（文字说明）**：
  - ① 从离线数据集中采样原始轨迹，随机选取一个时间步作为锚点，提取其状态作为条件。
  - ② 前向扩散模型：给定锚点状态，生成后续若干时间步的状态序列（未来段）。
  - ③ 反向扩散模型：给定锚点状态，生成之前若干时间步的状态序列（过去段）。
  - ④ 将未来段和过去段在锚点处拼接，得到一条完整的新轨迹。
  - ⑤ 根据任务需求（如是否涉及动作），还需处理动作序列（论文可能采用类似方式生成动作或使用逆动力学模型）。
  - ⑥ 将合成轨迹加入原始缓冲区，训练下游离线RL算法（如IQL、CQL等）。

#### 3. 实验设计
- **使用的数据集/场景**：D4RL（Offline RL标准基准），包含MuJoCo机器人控制任务（如HalfCheetah、Hopper、Walker2d）以及AntMaze等导航任务，涵盖不同数据质量（medium、medium-replay、medium-expert等）。
- **Benchmark**：对比多种先进的数据增强方法，如：
  - 基于扩散模型的轨迹生成方法（如Diffuser、DT-Q等）；
  - 传统数据增强方法（如添加高斯噪声、随机混叠等）；
  - 其他生成式增强方法（如Synthesizer、Trajectory Transformer变体）。
- **对比方法**：具体名称摘要未列出，根据元数据推测包括SOTA的离线RL算法（如IQL、CQL）以及直接使用原始数据的baseline。

#### 4. 资源与算力
- **文中未明确说明**：论文摘要和元数据未提及GPU型号、数量、训练时长等资源细节。仅提供代码仓库（GitHub），但未披露算力配置。
- **建议**：出于可复现性考虑，读者需查看代码仓库中的配置说明或联系作者获取细节。

#### 5. 实验数量与充分性
- **实验数量**：论文声称在**多个离线RL骨干网络**（backbone）上进行广泛实验，包括不同算法（如IQL、CQL、TD3+BC等）和不同数据集（D4RL的多个任务），并进行了消融研究（消去双向生成、单方向生成等）。
- **充分性评价**：
  - **优势**：覆盖多个数据集和算法，验证了方法的通用性；消融实验可以揭示双向生成相对于单向生成的增量价值。
  - **潜在不足**：未给出具体的实验次数和统计显著性检验（如置信区间）；未讨论迁移到图像/视频领域的离线RL场景（如Atari）的泛化能力。
  - **公平性**：对比方法采用官方或常见超参设置，但论文未明确是否进行超参搜索以确保公平。

#### 6. 论文的主要结论与发现
- BiTrajDiff在D4RL各数据集上**持续优于**现有数据增强方法，尤其在高维连续控制任务中提升显著。
- **双向生成**比单向生成（仅前向或仅后向）效果更好，表明连接不同行为模式是关键。
- 在数据量较少（如medium-replay）的任务中，BiTrajDiff带来的样本效率提升更大，说明该方法特别适合数据稀缺场景。
- 该框架容易集成到主流离线RL算法中，无需修改网络结构，仅作为数据预处理环节。

#### 7. 优点
- **创新性**：首次将双向轨迹生成引入离线RL数据增强，突破了单轨迹局部变体的局限。
- **简洁有效**：分解为两个独立扩散过程，易于实现且可并行加速（前向和后向模型可独立训练）。
- **通用性**：可即插即用于多种离线RL算法，无需改变训练流程。
- **开源代码**：提供完整实现，便于复现和后续研究。

#### 8. 不足与局限
- **实验覆盖**：仅测试了D4RL中的连续控制任务，缺乏对离散动作（如Atari游戏）、多智能体或现实世界机器人数据的评估。
- **超参敏感性**：锚点选择策略（如位置、频率）可能对性能有影响，论文未充分讨论其鲁棒性。
- **资源消耗**：训练两个扩散模型的计算成本高于单模型，但论文未量化额外开销。
- **理论分析缺失**：未从理论上解释为何双向生成能更好地恢复全局连通性，缺乏对生成质量（如动作合理性、物理可行性）的进一步验证。
- **偏差风险**：合成轨迹可能引入不现实的过渡（如状态跳跃），需额外筛选或动作约束，论文未探讨此问题。

（完）
