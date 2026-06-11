---
layout: post
title: JEPA SemCom 第3-4周合并笔记
date: 2026-06-10 16:25 +0800
description: 合并后的 JEPA-style predictive semantic representation 学习笔记与 8 周研究计划
pin: true
math: true
mermaid: true
category:
- JEPA
- 世界模型
- SC
tags:
- JEPA
- 世界模型
- SC
published: true
sitemap: false
author: tangyu
---

# JEPA SemCom 第3-4周合并笔记

> 本文件由两份 Markdown 合并拆分而成，保留原始 8 周计划的论文清单与详细笔记内容。

# 第 3 周：世界模型基础
需要掌握：

## 1.latent dynamics。隐空间动力学

传统强化学习直接面对的是高维观测，例如图像：
$$
o_t \in \mathbb{R}^{H \times W \times C}
$$
但图像里有大量无关信息，比如背景、纹理、光照。世界模型的核心思想是先把观测压缩成低维隐变量：
$$
z_t = \text{Encoder}(o_t)
$$
然后不在像素空间预测未来，而是在 latent 空间预测：
$$
p(z_{t+1} \mid z_t, a_t)
$$
这就是 **latent dynamics**。它回答的是：

> 如果当前世界状态是 $z_t$，我执行动作 $a_t$，下一步世界大概会变成什么？

PlaNet 的关键贡献就是学习一个带有**确定性状态**和**随机状态**的 recurrent state-space model，也就是 RSSM，用于从图像中学习可规划的 latent dynamics。论文强调 PlaNet 是一个从图像学习环境动力学、并在 latent 空间快速规划的纯 model-based agent。

## 2.imagination rollout。想象轨迹展开

有了世界模型以后，智能体不一定每次都要和真实环境交互。它可以在模型里面“脑补未来”：
$$
z_t \rightarrow z_{t+1} \rightarrow z_{t+2} \rightarrow \cdots \rightarrow z_{t+H}
$$
同时预测每一步奖励：
$$
\hat r_{t+1}, \hat r_{t+2}, \cdots, \hat r_{t+H}
$$
这就是 **imagination rollout**，也可以理解成：

> 在脑中的虚拟世界里尝试动作序列，看哪条未来轨迹更好。

PlaNet 用 imagined trajectories 做 **CEM planning**，每一步重新搜索动作序列；Dreamer 则更进一步，直接在 imagined latent trajectories 上训练 actor 和 value model。Dreamer 的论文摘要明确说，它通过 compact latent space 中的 imagined trajectories 学习长时域行为，并把 value gradients 反向传播回 imagined trajectories。

## 3. model-based RL。

强化学习可以粗略分为两类：

| 类型           | 核心思想                                 | 典型方法                      |
| -------------- | ---------------------------------------- | ----------------------------- |
| model-free RL  | 不显式学习环境模型，直接学策略或价值函数 | DQN、PPO、SAC                 |
| model-based RL | 先学环境模型，再用模型规划或训练策略     | World Models、PlaNet、Dreamer |

model-based RL 的优势是**样本效率高**。因为一次真实交互得到的数据，可以反复用于训练世界模型；而世界模型又可以生成大量 imagined rollouts 辅助决策。PlaNet 官方项目页强调，它通过从图像学习 dynamics 并在 latent space 中规划，相比强 model-free 方法可以显著减少环境交互。

你可以这样理解：

> model-free RL 是“我亲自试很多次，然后总结经验”；
>  model-based RL 是“我先学会世界怎么运转，然后在脑子里模拟很多次”。

## 4.observation model、reward model、transition model。

一个标准 latent world model 通常包含三个核心模型：

1）transition model：状态转移模型
$$
p(z_{t+1} \mid z_t, a_t)
$$
作用：预测执行动作后 latent state 如何变化。

这是世界模型的核心。没有 transition model，就不能想象未来。

2）observation model：观测模型 / 解码器
$$
p(o_t \mid z_t)
$$
作用：从 latent state 重建图像观测。

注意：在 PlaNet / Dreamer 中，observation model 主要用于训练 world model，让 latent state 保留足够的环境信息；但在规划或策略学习时，不一定需要真的解码成图像。PlaNet 项目页也指出，规划时会从当前 hidden state 预测未来奖励，昂贵的 image decoder 在 planning 阶段并不需要使用。

3）reward model：奖励模型
$$
p(r_t \mid z_t)
$$
作用：预测当前 latent state 对应的奖励。

这非常关键。因为智能体想象未来不是为了生成漂亮图片，而是为了判断：

> 这条未来轨迹的累计奖励高不高？

因此，reward model 决定了 imagined rollout 是否对决策有用。

## 5.prediction error 和 uncertainty

世界模型不是完美的。它预测未来时会犯错，这个错误就是 **prediction error**。

常见 prediction error 包括：
$$
\| o_t - \hat{o}_t \|
$$
但更重要的是 **multi-step prediction error**。一步预测准，不代表连续预测 20 步还准。PlaNet 提出 latent overshooting，就是为了改善多步 latent 预测能力。

uncertainty 可以理解成：

> 模型对未来到底有多不确定？

它主要有两类：

| 类型                  | 含义               | 例子                       |
| --------------------- | ------------------ | -------------------------- |
| aleatoric uncertainty | 环境本身随机       | 同一个动作可能导致多个未来 |
| epistemic uncertainty | 模型没见过、不知道 | 进入训练数据外的新场景     |

PlaNet / Dreamer 这类 RSSM 通过 stochastic latent state 表达一部分不确定性，但如果要更严格地估计 epistemic uncertainty，通常还需要 ensemble、Bayesian model、dropout 或 disagreement model 等方法。

精读论文：

1. `Recurrent World Models Facilitate Policy Evolution`。

这篇是世界模型路线的经典起点。它把智能体拆成三个部分：
$$
\text{VAE} + \text{RNN/MDN-RNN} + \text{Controller}
$$
具体来说：

| 模块       | 作用                                           |
| ---------- | ---------------------------------------------- |
| VAE        | 把高维图像压缩成 latent vector                 |
| MDN-RNN    | 在 latent 空间预测下一个状态                   |
| Controller | 根据 latent state 和 RNN hidden state 输出动作 |

这篇论文的核心思想是：

> 先无监督地学一个可预测环境变化的世界模型，再用一个很小的 controller 在这个内部模型中学习控制。

NeurIPS 页面摘要指出，该方法使用生成式循环神经网络以无监督方式建模强化学习环境，把 world model 提取出的特征输入到简单策略中，并且还可以完全在内部生成环境中训练 agent，再迁移回真实环境。

> World Models 证明了“先学世界，再学控制”是可行的，但它的 representation、dynamics、policy 基本是分阶段训练的，后续 PlaNet / Dreamer 会让这个框架更系统、更强化学习化。

2.`Learning Latent Dynamics for Planning from Pixels (PlaNet)`。

PlaNet 的全称是 **Deep Planning Network**。它的核心贡献是：

> 从像素观测中学习 latent dynamics，然后直接在 latent space 中做 online planning。

PlaNet 不直接训练一个 policy network，而是在每个环境步使用 CEM 搜索未来动作序列。论文明确说，PlaNet 是纯 model-based agent，从图像学习环境动力学，并在 latent space 中快速 online planning。

PlaNet 的关键结构是 RSSM：
$$
h_t = f(h_{t-1}, z_{t-1}, a_{t-1})
$$
其中：

| 状态  | 含义                                              |
| ----- | ------------------------------------------------- |
| $h_t$ | deterministic hidden state，记忆历史              |
| $z_t$ | stochastic latent state，表达不确定性和多模态未来 |

PlaNet 训练时包含：

| 模型              | 作用                        |
| ----------------- | --------------------------- |
| encoder           | 从图像推断 latent posterior |
| transition model  | 预测 latent prior           |
| observation model | 重建图像                    |
| reward model      | 预测奖励                    |

PlaNet 的规划过程是：

1. 用 encoder 把历史观测变成当前 latent state；
2. 在 latent space 中采样多组动作序列；
3. 用 transition model 展开未来；
4. 用 reward model 预测累计奖励；
5. 选择累计奖励最高的动作序列；
6. 只执行第一个动作，下一步重新规划。

> PlaNet 的重点不是学策略，而是学一个足够好的 latent dynamics，然后每一步在这个 latent world model 里搜索最优动作。

3.`Dream to Control: Learning Behaviors by Latent Imagination (Dreamer)`。

Dreamer 是 PlaNet 的重要升级。它保留 latent world model，但不再每一步都用 CEM 做 online planning，而是学习：

| 模型        | 作用                                   |
| ----------- | -------------------------------------- |
| world model | 预测 latent dynamics、reward、discount |
| actor model | 在 latent state 上输出动作             |
| value model | 估计 latent state 的长期价值           |

Dreamer 的核心思想是：

> 不用真实环境轨迹直接训练 actor，而是在 world model 想象出来的 latent trajectories 上训练 actor 和 critic。

Dreamer 官方代码说明中也写到，它先从经验中学习 world model，再在 compact latent space 中学习 action model 和 value model；value model 优化 imagined trajectories 的 Bellman consistency，action model 通过 imagined trajectories 反传 value gradients。

这就解决了 PlaNet 的一个问题：

| PlaNet                     | Dreamer                                   |
| -------------------------- | ----------------------------------------- |
| 每一步在线 CEM 搜索动作    | 训练一个 actor，执行时直接输出动作        |
| planning 成本较高          | 推理更快                                  |
| 不显式学习 policy          | 学 actor 和 value                         |
| 主要是 planning from model | 主要是 learning behavior from imagination |

**一句话总结：**

> Dreamer 把“在世界模型中规划”变成了“在世界模型中训练策略”，因此更接近现代可扩展 model-based RL。

4.`DreamerV3: Mastering Diverse Control Tasks through World Models`。

DreamerV3 的目标是做一个更通用、更稳健的 world-model RL 算法。

它的关键不只是“模型结构更强”，而是让算法在很多任务上使用同一套超参数也能稳定工作。DreamerV3 的 arXiv 摘要称，它在超过 150 个多样任务上用单一配置超过专门方法，并通过 normalization、balancing、transformations 等稳定化技术跨领域学习；项目页也强调了固定超参数和广泛任务泛化能力。

DreamerV3 的代码仓库说明提到，它从经验中学习 world model，并使用 imagined trajectories 训练 actor-critic policy；world model 会把感知输入编码成 categorical representations，并在给定动作后预测未来表示和奖励。

需要注意：你列出的题名 **“DreamerV3: Mastering Diverse Control Tasks through World Models”** 更接近 2025 年 Nature 版本标题；arXiv 版本常见标题是 **“Mastering Diverse Domains through World Models”**。Nature 页面和项目页使用的是 “Mastering Diverse Control Tasks through World Models”。

> DreamerV3 的重点是把 Dreamer 路线从“某类连续控制任务有效”推进到“多领域、固定配置、可扩展”的通用世界模型强化学习框架。

本周产出：

- 画一张图解释 PlaNet/Dreamer 的 latent world model。

![ChatGPT Image 2026年6月11日 10_39_40](./img/ChatGPT Image 2026年6月11日 10_39_40.png)

- 思考：如果 receiver 已经有 world model，哪些内容不需要传？

这个问题非常关键，和你的“世界模型 + 语义通信”研究方向直接相关。

假设发送端是 camera / sensor / edge device，接收端是 receiver / control center。如果 receiver 已经拥有一个足够好的 world model，那么通信不一定需要传完整观测，而只需要传 **world model 无法可靠推断的部分**。

### 5.1 不需要传的内容

1）可预测的背景

例如铁路沿线场景中：

- 天空；
- 固定轨道；
- 站台边缘；
- 电线杆；
- 静态建筑；
- 长时间不变的背景纹理。

如果 receiver 的 world model 已经知道这些静态结构，就不需要每一帧重复传。

对应语义通信表达：

> 不传 full pixels，只传 background residual 或 scene change。

------

2）连续帧中的冗余时间信息

视频帧之间高度相关。假如 receiver 已有 latent dynamics：
$$
\hat z_{t+1} = f_\theta(z_t, a_t)
$$
那么它可以自己预测下一时刻的大部分状态。因此发送端不必传每一帧完整图像，只需要传：
$$
\Delta z_t = z_t - \hat z_t
$$
也就是预测残差。

可以理解成：

> receiver 自己能想象出来的未来，不需要发送端重复传。

------

3）任务无关细节

如果任务是入侵检测，那么很多像素细节并不重要：

- 天空颜色；
- 轨道石子纹理；
- 树叶轻微摆动；
- 光照变化；
- 远处建筑细节。

这些内容对“是否有人/动物/施工机械进入防护区”没有直接帮助，可以不传。

应该传的是：

- 目标类别；
- 目标位置；
- 目标速度；
- 目标轨迹；
- 与轨道/防护区的空间关系；
- 置信度；
- 异常程度。

------

4）receiver 已经掌握的动力学规律

如果 receiver 的 world model 已经知道某些运动规律，就不需要传完整运动过程。

例如：

- 列车沿轨道运动；
- 行人速度范围；
- 摄像机视角固定；
- 目标短时间内位置连续；
- 无人机/机器人 obey dynamics constraints。

发送端只需要传关键状态更新：
$$
z_t, \Delta z_t, \text{event}, \text{uncertainty}
$$
而不是传完整图像序列。

------

5）低不确定性的预测区域

如果 world model 对某个区域预测很确定，例如“空轨道仍然是空轨道”，则不需要高码率传输。

但如果出现：

- 新目标；
- 遮挡；
- 突然运动；
- 低光照；
- 模型预测和观测不一致；
- uncertainty 升高；

则需要提高传输量。

这可以形成一种 **uncertainty-aware semantic transmission**：
$$
\text{Transmit}(x_t) =
\begin{cases}
\text{low rate}, & \text{uncertainty low} \\
\text{high rate}, & \text{uncertainty high}
\end{cases}
$$

------

### 5.2 必须传的内容

如果 receiver 有 world model，也不是所有东西都能省掉。必须传的是：

| 必须传的内容                     | 原因                                   |
| -------------------------------- | -------------------------------------- |
| 初始状态 / 当前 latent state     | receiver 需要知道现在世界处于哪个状态  |
| prediction residual              | 修正 receiver 自己预测错的地方         |
| novel object / unexpected event  | 世界模型无法凭空知道新事件             |
| task-relevant semantics          | 决策任务需要的核心信息                 |
| uncertainty / confidence         | 告诉 receiver 哪些预测可靠，哪些不可靠 |
| goal / reward / task instruction | 不同任务关注的语义不同                 |
| model update signal              | 长期部署时需要适应新环境               |

因此，最理想的语义通信形式不是：
$$
\text{send full image}
$$
而是：
$$
\text{send } (z_t, \Delta z_t, \text{event}, \text{uncertainty}, \text{task semantics})
$$


世界模型的核心思想是让智能体在内部学习一个可预测环境变化的模型。它不直接在高维图像空间中进行控制，而是先把观测编码为紧凑的 latent state，再通过 transition model 预测未来 latent state，并通过 reward model 评估未来轨迹的价值。PlaNet 代表了“学习 latent dynamics 并在 latent space 中规划”的路线，它使用 RSSM 建模部分可观测环境，并通过 CEM 在想象轨迹中选择动作。Dreamer 在 PlaNet 的基础上进一步将在线规划转化为 latent imagination 中的 actor-critic 学习，使策略能够直接从 imagined rollouts 中获得训练信号。DreamerV3 则进一步提升了算法的稳定性和通用性，使 world-model RL 能够在多种任务上使用统一配置进行学习。

从语义通信角度看，如果接收端已经拥有 world model，那么发送端就不必传输完整观测，而应优先传输接收端无法准确预测的部分，例如当前 latent state、预测残差、新出现的目标、任务相关语义以及不确定性信息。换言之，world model 可以作为接收端的“先验知识”和“预测器”，通信系统只需要传输对任务决策有增量价值的信息。这为“世界模型驱动的语义通信”提供了一个重要研究思路：由 full observation transmission 转向 latent state / residual / event / uncertainty transmission。

# 第 4 周：JEPA 和预测式表征
需要掌握：

- self-supervised representation learning。
- masked prediction。
- pixel reconstruction vs feature prediction。
- joint embedding predictive architecture。
- collapse prevention。

精读论文：

1. `Self-Supervised Learning from Images with a Joint-Embedding Predictive Architecture`。
2. `Revisiting Feature Prediction for Learning Visual Representations from Video`。
3. `V-JEPA 2: Self-Supervised Video Models Enable Understanding, Prediction and Planning`。
4. `Point-JEPA` 或 `A-JEPA`，任选一个扩展方向。

本周产出：

- 理解 I-JEPA/V-JEPA 的 encoder、context、target、predictor。
- 写一页笔记：为什么 JEPA 比 pixel reconstruction 更适合语义通信？

## 阶段梳理

第 3-4 周的作用是把“语义通信 baseline”升级为“预测式语义表征”的研究框架：第 3 周用世界模型理解 latent dynamics、imagination rollout、receiver 先验与预测残差；第 4 周用 JEPA 理解为什么应该预测 feature/latent，而不是重建 pixel。
