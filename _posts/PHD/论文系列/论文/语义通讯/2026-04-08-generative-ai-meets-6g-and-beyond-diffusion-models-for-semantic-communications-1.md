---
layout: post
title: 'Generative AI Meets 6G and Beyond:  Diffusion Models for Semantic Communications'
date: 2026-04-08 16:32 +0800
description: 这是生成语义通信扩散模型的第一篇教程论文。它为研究人员提供了一个统一的资源，帮助他们高效地开始在这个跨学科领域的工作，而无需分别在生成式人工智能和无线通信领域中翻阅零散的文献。
pin: true
math: true
mermaid: true
category:
- 扩散模型
- 语义通信
tags:
- 扩散模型
- 语义通信
published: true
sitemap: false
author: tangyu
---

# 1.介绍

## Weaver’s three-level communication model

| 层级 | 名称 | 功能说明 |
|------|------|----------|
| Level I: Technical | 技术层 | 传统通信链路：发射机 → 信道 → 接收机，关注比特传输可靠性（如误码率）。 |
| Level II: Semantic | 语义层 | 引入语义处理：语义分析（从原始数据提取意义）→ 知识库 → 语义合成（根据意图重建信息）。核心是“理解”与“生成”。 |
| Level III: Effectiveness | 效果层 | 以任务效果为导向：通过“意图”（Intent）连接发送端与接收端图像，用语义评估（Semantic Evaluation）衡量重构图像是否达成通信目标（如是否准确传达“两只鹦鹉在互动”这一语义）。 |


香农解决了底层比特的技术问题后，研究人员如今将注意力转向了如何有效传达意义，致力于在更高层面实现沟通目标。

语义通信本质上是知识驱动的。语义是一种依赖于上下文的抽象表达，需要借助相关知识才能解读。这些知识可能源于数据模式、特定任务的数据库或领域专家经验，它们指导发送端提取语义特征，并帮助接收端从压缩的信息中完成所需内容的重构。

凭借强大的预训练生成式AI模型，接收端现在能够仅凭少量"语义线索"就重建完整内容，这得益于语义特征对信道干扰所表现出的更强鲁棒性。

生成式AI与语义通信的结合实现了前所未有的高压缩率，同时保持了高质量输出，标志着生成式语义通信时代的开启。


## Motivation

扩散模型能够生成高质量视觉内容的能力，加上稳定的训练动态、灵活的调节机制和严谨的理论基础，使他们成为下一代沉浸式通信系统的理想选择。

扩散模型可以从根本上改变语义通信系统编码、传输和重建信息的方式。

## Contribution

**综合技术基础:** 为语义通信研究人员提供了第一个专门针对扩散模型的系统教程。从基本原理出发，开发了三个关键的技术线程（即可控生成的条件扩散、加速采样的有效扩散和任务自适应的广义扩散）(i.e., conditional diffusion for controllable generation, efficient diffusion for accelerated sampling, and generalized diffusion for task adaptation)

**统一理论框架:** 通过将语义解码表述为逆问题（verse problem），在语义通信和计算成像等成熟领域之间建立联系，引入了一种新的视角。该框架从逆问题理论中解锁了强大的数学工具，同时为**不确定性**和**不完美信道条件**下语义重建的基本性质（fundamental nature）提供了新的见解（fresh insights）。

**实用实施资源:** 提供广泛的实用资源，包括精心策划的开源实施、教育材料和部署指南。通过对以人为中心、以机器为中心和以代理为中心的语义通信场景的详细分析，我们确定了关键的研究挑战，并为未来的研究制定了有前景的方向，弥合了理论进步和实际应用之间的差距。



本文的其余部分结构如下。第二节介绍了理解基于分数的扩散模型所必需的生成建模基础。第三节系统地介绍了扩散模型的基本原理，包括调节机制、加速技术和泛化策略，并附有全面的文献综述。第四节将语义解码表述为一个逆问题，并探讨了基于扩散的求解方法。第五节考察了扩散模型在以人为中心、以机器为中心和以代理为中心的语义通信场景中的应用。第六节确定了理论局限性、技术挑战和有前景的未来研究方向。最后，第七节提供结论性意见。图3显示了文章结构的视觉概述，而表一总结了整个文章中使用的主要学术术语和数学符号。

- **Section I**: 介绍背景、动机、贡献和组织架构，特别关注领域特定挑战和贝叶斯公式化
- **Section II**: 预备知识，涵盖深度生成建模的基础概念
- **Section III**: 扩散模型核心技术，包括基础原理、条件化、效率优化和泛化能力
- **Section IV**: 从逆问题角度审视生成式语义通信的数学特性
- **Section V**: 扩散模型在语义通信中的具体应用，按通信对象分类
- **Section VI**: 开放性问题，涵盖理论、技术和未来发展方向
- **Section VII**: 结论总结

# 2.预备知识

本文中使用的学术术语和数学符号。所有向量、矩阵和张量都以黑体设置；除非另有说明，否则非粗体符号表示标量。未明确列出的符号在首次使用时在上下文中定义。

| Acronym    | Definition (Academic Terminology)                            | Symbol         | Meaning (Mathematical Notation)                              |
| ---------- | ------------------------------------------------------------ | -------------- | ------------------------------------------------------------ |
| **DNN**    | deep neural network（深度神经网络）                          | **𝒳**          | data space for source and reconstructions（源数据与重建数据空间） |
| **AIGC**   | AI-generated content（AI生成内容）                           | **𝒴**          | measurement space at the receiver（接收端测量空间）          |
| **ARM**    | autoregressive model（自回归模型）                           | **𝒵**          | latent space for semantic representation（语义表征的潜在空间） |
| **VAE**    | variational autoencoder（变分自编码器）                      | **𝐱 ∈ 𝒳**      | raw data variable（原始数据变量）                            |
| **GAN**    | generative adversarial network（生成对抗网络）               | **𝐲 ∈ 𝒴**      | degraded measurement variable（退化测量变量）                |
| **LDM**    | latent diffusion model（潜在扩散模型）                       | **𝐳 ∈ 𝒵**      | low-dimensional latent variable（低维潜在变量）              |
| **MCMC**   | Markov chain Monte Carlo（马尔可夫链蒙特卡洛）               | **𝐱̂₀∣ₜ**       | posterior mean of clean data at time step *t*（时刻 *t* 下干净数据的后验均值） |
| **DSM**    | denoising score matching（去噪分数匹配）                     | **𝜃, 𝜙, 𝜓, 𝜗** | model parameters（模型参数）                                 |
| **SMLD**   | score matching with Langevin dynamics（基于朗之万动力学的分数匹配） | **𝑝(𝐱)**       | data distribution or density（数据分布或概率密度）           |
| **DDPM**   | denoising diffusion probabilistic model（去噪扩散概率模型）  | **𝐬_𝜃(·, 𝑡)**  | learned score ∇ₓ log 𝑝ₜ(𝐱) driving reverse sampling（学习到的分数 ∇ₓ log 𝑝ₜ(𝐱)，驱动反向采样） |
| **VE SDE** | variance exploding stochastic differential equation（方差爆炸型随机微分方程） | **𝜖_𝜃(·, 𝑡)**  | denoising network predicting noise; 𝜖_𝜃 = −√(1−ᾱₜ) 𝐬_𝜃（去噪网络预测噪声；𝜖_𝜃 = −√(1−ᾱₜ) 𝐬_𝜃） |
| **VP SDE** | variance preserving stochastic differential equation（方差保持型随机微分方程） | **𝐯(·, 𝑡)**    | velocity field governing deterministic trajectory evolution（控制确定性轨迹演化的速度场） |
| **PF ODE** | probability flow ordinary differential equation（概率流常微分方程） | **𝝍(·, 𝑡)**    | flow map transporting prior to data distribution（将先验映射至数据分布的流映射） |
| **CG**     | classifier guidance（分类器引导）                            | **𝐟(·, 𝑡)**    | vector-valued drift coefficient（向量值漂移系数）            |
| **DPS**    | diffusion posterior sampling（扩散后验采样）                 | **𝑔(𝑡)**       | scalar-valued diffusion coefficient（标量扩散系数）          |
| **CFG**    | classifier-free guidance（无分类器引导）                     | **𝒜(·)**       | general forward operator for inverse problems（逆问题中的通用前向算子） |



## Deep Generative Modeling

生成模型模拟了真实世界的数据生成过程，并提供了两个关键优势。

1. 支持人工智能生成内容（AIGC）应用程序和无监督表示学习，可以提取不纠缠、语义上有意义和统计上独立的变异因素。
2. 结合物理定律和约束，同时将未知细节视为噪声，使其直观且可解释，以便通过预测观测比较来验证关于现实世界机制的理论。
3. 设 $\mathcal{X} \subset \mathbb{R}^D$ 表示具有维度 $D \in \mathbb{N}^+$ 的数据空间。




- $\mathcal{X}$：数据所在的集合（例如所有可能的图像、声音波形等）。
- $\mathbb{R}^D$：$D$ 维实数向量空间。
- $D$：数据的维度（例如一张 28×28 的灰度图，$D = 784$）。
- $\subset$：表示 $\mathcal{X}$ 是 $\mathbb{R}^D$ 的子集（如图像像素值在 $[0,1]$ 范围内，而不是整个 $\mathbb{R}^D$）。



2. **真实数据分布 $p_{\text{data}}(\mathbf{x}) : \mathbb{R}^D \to \mathbb{R}_{\ge 0}$ 满足 $\int_{\mathbb{R}^D} p_{\text{data}}(\mathbf{x}) \, d\mathbf{x} = 1$，其中 $\mathbf{x} = (x_1, \dots, x_D)^\top \in \mathbb{R}^D$ 是一个数据点。**

- $p_{\text{data}}(\mathbf{x})$：这是一个理想中的函数，它告诉我们真实世界中某个数据点 $\mathbf{x}$ 出现的可能性有多大。
- $\mathbb{R}_{\ge 0}$：表示这个函数的输出值是非负的（概率不能是负数）。
- $\int_{\mathbb{R}^D} p_{\text{data}}(\mathbf{x}) \, d\mathbf{x} = 1$：这是概率分布的核心约束 ，所有可能的数据点的概率之“和”（在连续空间中是积分）必须等于 1。
- $\mathbf{x} = (x_1, \dots, x_D)^\top$：一个数据点是一个 $D$ 维列向量（例如一张图像的所有像素值排成一列）。

3. **生成建模旨在从数据集 $\{\mathbf{x}_i\}_{i=1}^N$ 中估计 $p_{\text{data}}(\mathbf{x})$，以实现采样和概率评估。**

- 数据集 $\{\mathbf{x}_i\}_{i=1}^N$：我们拥有的有限样本（如 10000 张猫的图片）。
- 估计 $p_{\text{data}}(\mathbf{x})$：我们的任务是用这些样本来构建一个模型，让它能够模仿真实数据的分布。
- 实现采样：根据学到的模型，生成新的、类似真实数据的新样本（如生成一张新的猫的图片）。
- 概率评估：给定一个数据点，模型能告诉我们它属于真实数据分布的可能性有多大（可用于判断是否是异常点）。

4. **一个具有 $P \in \mathbb{N}^+$ 个参数 $\theta \in \Theta \subset \mathbb{R}^P$ 的参数化模型 $p_\theta(\mathbf{x}) : \mathbb{R}^D \to \mathbb{R}_{\ge 0}$ 作为 $p_{\text{data}}(\mathbf{x})$ 的代理。**

- 参数化模型 $p_\theta(\mathbf{x})$：这是一个由函数形式定义的模型（例如一个神经网络），它的行为由一组参数 $\theta$ 控制。
- $P$：模型参数的总数（例如一个神经网络有 1000 万个权重）。
- $\theta \in \Theta \subset \mathbb{R}^P$：参数 $\theta$ 是一个 $P$ 维向量，它属于参数空间 $\Theta$（例如每个权重不能是无穷大）。
- 作为代理：我们无法直接知道 $p_{\text{data}}$，所以用 $p_\theta$ 来近似它。

5. **目标是找到最优参数 $\theta^*$，使得 $p_{\theta^*}(\mathbf{x}) \approx p_{\text{data}}(\mathbf{x})$。**

- 通过训练（如梯度下降），调整参数 $\theta$，让模型 $p_\theta(\mathbf{x})$ 尽可能接近真实的 $p_{\text{data}}(\mathbf{x})$。
- $\theta^*$ 代表训练完成后得到的最佳参数。

6. **有效的概率分布要求 $p_\theta(\mathbf{x})$ 满足：(1)（非负性）对于所有 $\mathbf{x} \in \mathbb{R}^D$，有 $p_\theta(\mathbf{x}) \ge 0$；(2)（归一化）$\int_{\mathbb{R}^D} p_\theta(\mathbf{x}) \, d\mathbf{x} = 1$。**

- 这是概率分布的两条基本公理。
- (1) 非负性：模型输出的概率不能是负数。
- (2) 归一化：模型预测的所有可能性加起来必须等于 1。

7. **虽然非负性很容易满足，但归一化却很困难：它需要在整个高维数据空间上进行积分，对于复杂模型来说通常是不可行的，这促使了现代深度生成模型中的专门策略。**

- **非负性容易满足**：可以通过激活函数（如 ReLU、Softplus、Sigmoid 后乘以正数）轻松保证模型输出非负。

- **归一化非常困难**：要计算 $\int_{\mathbb{R}^D} p_\theta(\mathbf{x}) \, d\mathbf{x}$，意味着要在整个 $D$ 维空间上对模型的输出求积分。在 $D$ 很大（如图像 D=784 或更高）时，这个积分几乎不可能计算。

- **现代模型的应对策略**：由于这个计算障碍，不同的生成模型采用了不同技巧来绕开显式归一化：

  - **GANs**：不直接建模 $p_\theta(\mathbf{x})$，而是通过对抗训练让生成器逼近真实数据分布，完全避开了归一化问题。
  - **VAEs**：通过引入一个潜在变量 $z$ 和变分推断，优化一个可计算的下界（ELBO），不需要计算复杂的归一化常数。
  - **Normalizing Flows**：通过一系列可逆变换，使得归一化常数可以通过雅可比行列式的计算来精确追踪，从而保证归一化。
  - **Diffusion Models**：通过逐步添加噪声和去噪的过程，间接学习数据分布，不直接建模密度函数本身，从而规避了归一化积分。



## Deep Generative Models

### 1）能量模型（Energy Models）

当精确计算归一化不行，就转向近似计算。能量模型 [22] 借鉴统计物理中的玻尔兹曼分布 [23]，利用**玻尔兹曼机（Boltzmann Machines）** 对分布进行参数化：（直接建模一个满足归一化条件 $\int p_\theta(\mathbf{x}) d\mathbf{x} = 1$ 的高维概率密度函数 $p_\theta(\mathbf{x})$ 是非常困难的。能量模型提供了一种绕开直接归一化计算的建模思路。玻尔兹曼分布在物理学中描述了系统处于不同能量状态的概率。能量模型借用了这一概念，用一个“能量函数”来间接定义概率分布。）

$$
p_\theta(\mathbf{x}) = \frac{\exp(-\beta E_\theta(\mathbf{x}))}{Z_\theta}, \quad \text{(1)}
$$
其中 $E_\theta(\mathbf{x})$ 是一个以 $\theta$ 为参数的能量函数。例如，它可以被解释为势能 $-\log p_\theta(\mathbf{x})$。$\beta$ 是一个类似逆温度的任意正常数，而 $Z_\theta = \int_{\mathbb{R}^D} \exp(-\beta E_\theta(\mathbf{x})) \, d\mathbf{x}$ 是配分函数，用于确保 $p_\theta(\mathbf{x})$ 的归一化。

**解释**：

- **公式解读**：
  - $p_\theta(\mathbf{x})$：这是模型定义的、关于数据点 $\mathbf{x}$ 的概率密度。
  - $E_\theta(\mathbf{x})$：**能量函数（Energy Function）**，这是一个由参数 $\theta$（通常是神经网络的权重）决定的函数。它衡量了数据点 $\mathbf{x}$ 的“能量”或“不自然程度”。能量越低，该点被认为越可能来自真实数据分布。
  - $\beta > 0$：**逆温度（inverse temperature）** 参数。它类似于物理中的 $\beta = 1/(k_B T)$,。$\beta$ 控制着分布的“锐利度”——$\beta$ 越大，模型越倾向于分配高概率给那些能量非常低的点；$\beta$ 越小，概率分布越平缓。$k_B$代表玻尔兹曼常数，$T$代表温度。
  - $\frac{\exp(-\beta E_A)}{\exp(-\beta E_B)} = \exp(-\beta E_A - (-\beta E_B)) = \exp(\beta (E_B - E_A))$,如果 $E_A < E_B$，那么 $\beta (E_B - E_A)$ 是一个正数。这个正数越大（意味着 $E_A$ 比 $E_B$ 低得越多，或者 $\beta$ 越大），$\exp(\beta (E_B - E_A))$ 的值就越大。**意思就是用指数来衡量x1与x2点的能量比大小，相对的**
  - $Z_\theta$：**配分函数（Partition Function）**，定义为 $Z_\theta = \int_{\mathbb{R}^D} \exp(-\beta E_\theta(\mathbf{x})) \, d\mathbf{x}$。它的作用是**归一化**，确保 $p_\theta(\mathbf{x})$ 是一个合法的概率分布（即整个空间上的积分为 1）。这是能量模型最大的计算瓶颈。它需要对整个 $D$ 维空间进行积分，对于高维、复杂的 $E_\theta(\mathbf{x})$，这个积分通常是无法解析计算的。
- **核心思想**：这个公式表明，一个点的概率与其能量呈负指数关系。低能量区域对应高概率密度，高能量区域对应低概率密度。用**能量去推算概率分布**。

- **势能解释**：如果我们将公式 (1) 取对数，得到 $\log p_\theta(\mathbf{x}) = -\beta E_\theta(\mathbf{x}) - \log Z_\theta$。忽略常数项 $\log Z_\theta$，可以看出 $E_\theta(\mathbf{x})$ 与 $-\log p_\theta(\mathbf{x})$ 成正比。因此，能量函数确实可以看作是（负的）未归一化的对数概率，也就是一种“势能”。

能量函数可以通过深度神经网络（DNNs）进行灵活的参数化，而无需归一化约束，但评估 $Z_\theta$ 需要高维积分，使其难以处理。幸运的是，马尔可夫链蒙特卡洛（MCMC）[24] 能够在不显式计算 $Z_\theta$ 的情况下实现近似。

- **Markov chain Monte Carlo（MCMC）**：MCMC 是一类采样算法。它可以从分布 $p_\theta(\mathbf{x})$ 中抽取样本，**仅需要知道未归一化的概率密度 $\exp(-\beta E_\theta(\mathbf{x}))$**。因为 MCMC 的转移概率（如 Metropolis-Hastings 准则）是两个概率密度的比值，归一化常数 $Z_\theta$ 会被约掉，所以不需要知道它的值。

由于 $\nabla_\theta \log Z_\theta = -\mathbb{E}_{p_\theta(\mathbf{x})} [\nabla_\theta E_\theta(\mathbf{x})]$，可以通过 MCMC 采样 $\hat{\mathbf{x}} \sim p_\theta(\mathbf{x})$ 并近似 $\nabla_\theta \log Z_\theta \approx -\nabla_\theta E_\theta(\hat{\mathbf{x}})$，从而使训练成为可能。**（$Z_\theta$由定义得到$Z_\theta = \int_{\mathbb{R}^D} \exp(-\beta E_\theta(\mathbf{x})) \, d\mathbf{x}$）积分为1。**

**解释**：

- **数学恒等式**：这个等式连接了**配分函数**的梯度和能量函数梯度的期望。左边是关于 $\theta$ 对 $\log Z_\theta$ 求导，右边是能量函数梯度在分布 $p_\theta(\mathbf{x})$ 下的期望。
- **训练的关键**：在训练能量模型时，我们需要计算损失函数（如最大似然）相对于 $\theta$ 的梯度。这个梯度通常包含 $\nabla_\theta \log Z_\theta$ 这一项。
- **近似方案**：既然无法直接计算期望 $\mathbb{E}_{p_\theta(\mathbf{x})} [\nabla_\theta E_\theta(\mathbf{x})]$，可以用 **蒙特卡洛方法** 来近似它。通过 MCMC 从 $p_\theta(\mathbf{x})$ 中采样一个样本 $\hat{\mathbf{x}}$，然后用 $\nabla_\theta E_\theta(\hat{\mathbf{x}})$ 来近似整个期望。因此，$\nabla_\theta \log Z_\theta$ 可以近似为 $-\nabla_\theta E_\theta(\hat{\mathbf{x}})$，**近似方案用采样的数据->近似真实的x；神经网络->近似真实的能量，得到微分，从而推理可更新梯度**。
- **结果**：这使得我们可以计算出损失函数的梯度，并使用梯度下降法来更新参数 $\theta$。这是能量模型能够被成功训练的核心技术。

虽然 MCMC 采样避免了显式计算 $Z_\theta$，但概率评估仍需要估计 $Z_\theta$。像退火重要性采样 [25] 这样的方法存在，但会产生不可避免的估计误差，限制了准确的概率计算。

- **解释**：
  - **MCMC 的局限**：MCMC 主要解决了**采样**问题（生成新样本），因为它不需要 $Z_\theta$。但它并没有解决**概率评估**问题（计算给定样本的概率 $p_\theta(\mathbf{x})$）。
  - **概率评估的需求**：在某些任务中，如异常检测、模型比较、精确推断等，我们需要知道 $p_\theta(\mathbf{x})$ 的具体数值。
  - **$Z_\theta$ 的估计**：要计算 $p_\theta(\mathbf{x}) = \exp(-\beta E_\theta(\mathbf{x})) / Z_\theta$，必须知道 $Z_\theta$。
  - **退火重要性采样（AIS）**：这是一种用于估计配分函数 $Z_\theta$ 的高级蒙特卡洛方法。它通过构建一系列“桥梁”分布，从一个已知配分函数（如简单高斯）的分布逐步过渡到目标分布 $p_\theta(\mathbf{x})$，从而估计出 $Z_\theta$ 的比值。
  - **固有误差**：AIS 等方法仍然是基于采样的估计，因此总会存在统计噪声和估计误差。在高维空间中，这种误差可能很大，导致计算出的概率不够准确。
  - **结论**：能量模型在**生成能力**方面表现优异，但在需要**精确概率值**的应用中存在局限性。

总结：能量模型是一种强大的建模框架，它通过能量函数 $E_\theta(\mathbf{x})$ 间接定义概率分布，绕过了直接建模和归一化的困难。MCMC 采样和基于采样的梯度估计使得模型可以被有效训练。然而，其核心的配分函数 $Z_\theta$ 使得精确的概率评估成为一个持续存在的挑战。

### 2）显式模型（Explicit Models）

显式模型：在生成建模中，除了使用特定的采样方法进行近似以解决归一化挑战外，还可以直接使用显式公式来实现这种近似。显式深度生成模型的两个代表性例子是自回归模型[26]和变分自编码器[27]，它们说明了强制归一化的不同公式化方法。

#### Autoregressive Models自回归模型 (ARMs):

ARM利用概率链规则将高维分布分解为单变量条件的乘积，这些乘积更容易参数化和归一化。具体而言，ARM定义了:

$$
p_\theta(\mathbf{x}) = \prod_{i=1}^{D} p_\theta(x_i \mid \mathbf{x}_{\lt i}), \quad \text{(2)}
$$

其中:
$D$ 是数据 $\mathbf{x}$ 的维度；
$x_i$ 是 $\mathbf{x}$ 的第 $i$ 个分量；
$\mathbf{x}_{\lt i} = (x_1, x_2, \dots, x_{i-1})^\top$ 表示前 $i-1$ 个分量组成的子向量；
特别地，$\mathbf{x}_{\lt 1} := \varnothing$（空条件），因此 $p_\theta(x_1 \mid \mathbf{x}_{\lt 1}) = p_\theta(x_1)$。
链式法则（Chain Rule of Probability），对任意联合分布恒成立。 若每个条件分布 $p_\theta(x_i \mid \mathbf{x}_{\lt i})$ 均为合法概率分布（即 $\int p_\theta(x_i \mid \mathbf{x}_{\lt i}) \, dx_i = 1$），则整个联合分布 $p_\theta(\mathbf{x})$ 自动精确归一化：

$$
\int_{\mathbb{R}^D} p_\theta(\mathbf{x}) \, d\mathbf{x} = 1.
$$

然而，自回归因子分解需要数据维度的顺序排序。虽然对于顺序数据（例如文本、音频）来说很自然，但许多域缺乏固有的顺序。例如，图像中的像素排列。这种约束限制了架构的灵活性，通常需要专门的构造，如掩码卷积[28]。因此，ARM在顺序数据生成方面表现出色，但在超高分辨率图像或视频方面面临挑战。

#### Variational Autoencoders变分自编码器 (VAEs):

变分自编码器（Variational Autoencoders, VAEs）是一种概率生成模型，其设计思想源于自编码器（AEs）在数据压缩中的应用，但通过引入一个辅助隐变量 $z \sim p(z)$ 来对数据分布 $p(x)$ 进行显式建模，并假设数据 $x$ 由 $z$ 生成：$x \sim p_\theta(x|z)$。VAEs 的框架由三个核心组件构成：一个**可处理的先验分布** $p(z)$（通常取标准正态分布 $\mathcal{N}(0,I)$）、一个以参数 $\phi \in \Phi \subset \mathbb{R}^P$ 表征的**编码器** $q_\phi(z|x)$，以及一个以参数 $\theta$ 表征的**解码器** $p_\theta(x|z)$；三者共同定义了边缘分布（即模型对数据 $x$ 的预测分布）：
$$
p_\theta(x) = \int_{\mathbb{R}^d} p(z) p_\theta(x|z) \, dz. \tag{3}
$$
该式表明，$p_\theta(x)$ 是对隐变量空间$z$上的积分，因此可被解释为一个**无限混合模型**［20］：其中 $p(z)$ 提供混合系数（mixing coefficients），而 $p_\theta(x|z)$ 定义了各混合成分（mixture components）。当 $p(z)$ 与 $p_\theta(x|z)$ 均为归一化分布时，$p_\theta(x)$ 的归一化性质得以保证，这是 VAE 区别于许多其他生成模型的重要特征（类似 ARMs，VAEs 通过施加**架构约束**（architectural constraints）实现精确归一化）。

在实践中，真实后验 $p(z|x) = \frac{p(z)p_\theta(x|z)}{p_\theta(x)}$ 通常不可解析计算，因此 VAE 采用**变分推断**策略：用编码器 $q_\phi(z|x)$ 参数化一个近似后验分布，而非输出确定性嵌入；该近似分布常取指数族形式（如高斯分布，便于后续的KL计算，让训练可收敛），并以神经网络为骨架输出其参数（例如均值与方差）。训练过程旨在最大化**证据下界**（Evidence Lower Bound, ELBO）［27］，即对数似然 $\log p_\theta(x)$ 的可计算下界；采样则遵循**祖先生成**（ancestral generation）流程：先从先验中抽取 $\hat{z} \sim p(z)$，再根据 $\hat{x} \sim p_\theta(x|\hat{z})$ 生成观测样本。尽管 VAE 在模型定义层面构造了**严格归一化的分布**，但由于依赖变分近似与特定架构约束，其实际估计出的概率值（如 $\log p_\theta(x)$）仅为近似结果，无法获得精确概率密度。

### 3）隐式模型（Implicit Models）

归一化的挑战源于对概率密度或质量函数的建模，隐式表示概率分布，则可以避免归一化。

- **“Normalization challenge”** 指的是：显式概率模型（如 VAE、Flow、Autoregressive models）必须保证输出分布 $p_\theta(x)$ 是一个**归一化的概率密度函数**（即 $\int p_\theta(x)dx = 1$)，这在高维空间中计算困难（需积分或求和），尤其当模型复杂时。
- **“Implicit representation”** 是解决方案：不显式定义$p_\theta(x)$，而是通过**采样过程**来“隐式”定义分布，即我们只关心“如何生成样本”，而不关心“该样本的概率值是多少”

生成对抗网络（GAN）[29]，这是一个具有代表性的隐式深度生成模型家族，直接对概率分布的采样过程进行建模，从而完全避开了归一化的困难。

1. **两步生成流程**：
  - Step 1：从简单先验采样 $z \sim p(z)$（如 $z \sim \mathcal{N}(0, I)$ 或均匀分布 $\mathcal{U}(-1,1)^d$）；
  - Step 2：通过**确定性神经网络** $G_\theta: \mathbb{R}^d \to \mathbb{R}^D$ 映射：$x = G_\theta(z)$。
  - 注意：**生成器是确定性的**（无随机性），所有随机性来自输入 $z$。
2. **规避归一化**：因不显式建模密度$p_\theta(x) $，而是用神经网络拟合该映射函数$G_\theta:$，无需计算或保证 $\int p_\theta(x)dx = 1$ 这是 GAN 相对于 VAE/Flow 的根本优势之一。

训练过程由判别器，生成器组成：

- **判别器 $D_\phi$**：二分类器，输入数据 $x$，输出标量 $D_\phi(x) \in [0,1]$，表示“该样本是真实数据”的概率。
- **生成器 $G_\theta$**：目标是让 $D_\phi(G_\theta(z)) \approx 1$（即骗过判别器）。
- **对抗博弈**：

  - 判别器最小化损失：$\mathbb{E}_{x \sim p_{\text{data}}}[ \log D_\phi(x) ] + \mathbb{E}_{z \sim p(z)}[ \log(1 - D_\phi(G_\theta(z))) ]$  ，真假判断的误差最小
  - 生成器最小化：$\mathbb{E}_{z \sim p(z)}[ \log(1 - D_\phi(G_\theta(z))) ]$（或等价地最大化 $\log D_\phi(G_\theta(z))$） ，生成的让判别器判别错误。
    → 这构成一个**极小极大博弈**（minimax game）：

  $$
  \min_\theta \max_\phi V(D_\phi, G_\theta) = \mathbb{E}_{x \sim p_{\text{data}}}[\log D_\phi(x)] + \mathbb{E}_{z \sim p(z)}[\log(1 - D_\phi(G_\theta(z)))]
  $$

经典结论：
当判别器最优时，生成器的目标等价于最小化 **Jensen-Shannon 散度**（JSD）：
$$
\min_G \mathrm{JSD}(p_{\text{data}} \parallel p_\theta)
$$
理论上，当 JSD=0 时，$p_\theta = p_{\text{data}}$。

**GANs的优劣**

- 不需要像 VAE 那样限制解码器为指数族、或像 Flow 要求可逆/雅可比易算；
- 可自由使用任意强大 DNN 架构（ResNet, Transformer, etc.），表达能力极强；
- 生成样本质量高（尤其在图像领域），细节逼真。

**理论劣势**

- **无法计算或估计 $p_\theta(x)$**（即无 likelihood）；
- 导致不能用于：
  - 异常检测（需低概率识别异常）；
  - 似然导向的贝叶斯推断；
  - 模型比较（如用 BIC/AIC）；
  - 重要性采样、变分推断等依赖概率密度的任务。

**训练挑战**：

1. **训练不稳定**：

- 判别器太强 → 生成器梯度消失；
- 判别器太弱 → 生成器学不到有用信号；
- 需精细调学习率、架构、正则（如梯度惩罚、谱归一化）。

2. **模式崩溃（Mode Collapse）**：

- 生成器发现某些“安全模式”（如只生成某类猫脸）就能骗过判别器，于是放弃探索其他模式；
- 结果：生成样本多样性严重不足（如所有生成图都像同一张脸）；
- 根本原因：GAN 优化的是 **JSD**，而 JSD 在支撑集不重叠时恒为 $\log 2$，导致梯度为 0（无法指导生成器修正）。

### 4）得分模型（Score Models）

前面几个DNN方法的缺陷

- VAE 的模糊生成 + 归一化约束；
- GAN 的无 likelihood + 训练不稳定；

得分模型是学习评分函数$\boxed{s(x) := \nabla_x \log p(x)}$(对数密度的梯度，用于找到更多相似点的方向)，用用参数为$\theta$的神经网络 $s_\theta(x)$ 来拟合真实得分 $s(x)$。

**得分模型（score model）** 本质上是一个“有方向的箭头场”（向量场），但它有个特殊性质： 它**必须**是某个**标量函数**（比如高度图）的梯度。 （数学上叫 *conservative vector field*，即“保守场”，意味着路径无关，可积分）

所以实际实现时，**不直接学 $s_\theta(x)$**，而是：

1. 用神经网络学一个**能量函数** $E_\theta(x)$（标量，输出一个数）；
2. 然后让得分模型等于它的**负梯度**：  
   $$
   s_\theta(x) = -\nabla_x E_\theta(x)
   $$

> 💡 为什么加负号？因为通常定义能量越低越稳定（如物理中势能最小），而概率越高越好 → 所以高概率对应低能量。

✅ **类比**：
想象一座山，$E_\theta(x)$ 是“海拔高度”，那么 $-\nabla E$ 就是指向“下山最快”的方向，而数据点喜欢待在“山谷”（低能量/高概率处）。得分函数 $s(x)$ 就是引导你往山谷走的力。



真实概率分布 $p(x)$ 必须满足 $\int p(x)dx = 1$（归一化），但很多模型只能写出 **未归一化的密度** $\tilde{p}(x)$（比如 $\tilde{p}(x) = e^{-\|x\|^2}$，但积分不是1）

但对于未归一化的密度函数 $\tilde{p}(\mathbf{x})$，满足：$$\int_{\mathbb{R}^D} \tilde{p}(\mathbf{x}) \, d\mathbf{x} = Z \ne 1 $$,若根据蒙特卡洛采样，得到 $p(x) \approx \frac{1}{Z} \tilde{p}(x)$，则  
$$
s(x) =-\nabla_x E_\theta(x)= \nabla_x \log \frac{\tilde{p}(x)}{Z} = \nabla_x \log \tilde{p}(x) - \underbrace{\nabla_x \log Z}_{=0} = \nabla_x \log \tilde{p}(x)
$$




上面讲的这些理论，**最成功的应用就是扩散模型（Diffusion Models）**，也就是用神经网络学一个“能量函数” $E_\theta(x)$；它的负梯度就是得分函数 $s_\theta(x)$；而这个得分函数**完全不用关心归一化常数**，所以特别适合高维数据建模，这就是扩散模型的理论基础。





#  一些基础概念：

## 期望

**期望 $\mathbb{E}_{p_\theta(\mathbf{x})} [\nabla_\theta E_\theta(\mathbf{x})]$ 的定义是：**

$$
\mathbb{E}_{p_\theta(\mathbf{x})} [\nabla_\theta E_\theta(\mathbf{x})] = \int_{\mathbb{R}^D} \left( \nabla_\theta E_\theta(\mathbf{x}) \right) p_\theta(\mathbf{x}) \, d\mathbf{x}
$$

指在$p_\theta(\mathbf{x})$概率下的期望，等于按照 $p_\theta(\mathbf{x})$定义的概率来对括号内的函数进行加权平均。这是一个对整个 $D$ 维空间的积分，对于复杂的 $E_\theta(\mathbf{x})$ 和 $p_\theta(\mathbf{x})$，这个积分通常是**不可计算的（intractable）**。



## 蒙特卡洛方法

蒙特卡洛方法的核心思想是：**一个随机变量的期望值，可以用大量独立同分布（i.i.d.）样本的平均值来近似**。

如果 $X$ 是一个服从概率分布 $p(x)$ 的随机变量，那么对于函数 $f(X)$，其期望值为：
$$
\mathbb{E}[f(X)] = \sum_x f(x) p(x) \quad \text{（离散情况）或} \quad \int f(x) p(x) dx \quad \text{（连续情况）}
$$
根据**大数定律（Law of Large Numbers）**，如果我们从 $p(x)$ 中抽取 $N$ 个 i.i.d. 样本 $x_1, x_2, ..., x_N$，那么当 $N \to \infty$ 时，样本均值收敛于期望：
$$
\mathbb{E}[f(X)] \approx \frac{1}{N} \sum_{i=1}^N f(x_i)
$$
当 $N$ 足够大时，这个近似就相当准确。

## 边际分布

在概率论和统计学中，“边际分布”（Marginal Distribution）指的是在一个多变量的概率分布中，只包含部分变量的分布。当我们有一个联合分布（Joint Distribution），即描述两个或多个随机变量一起的概率分布时，边际分布就是通过对其他不关心的变量进行积分（连续情况）或求和（离散情况）来获得的单一变量的分布。

例如，如果我们有两个随机变量 $X$ 和 $Y$，它们的联合分布是 $P(X, Y)$，那么 $X$ 的边际分布可以表示为：

$$ P(X) = \sum_y P(X, y) $$

对于连续变量，则是：

$$ P(X) = \int P(X, y) dy $$

这里，$P(X)$ 就是从联合分布 $P(X, Y)$ 中“边缘化”掉 $Y$ 后得到的 $X$ 的分布。同样的逻辑也适用于从 $P(X, Y)$ 中得到 $Y$ 的边际分布 $P(Y)$。

在变分自编码器（VAEs）的例子中，$p_\theta(x)$ 实际上就是关于数据 $x$ 的边际分布，它是通过对所有可能的隐变量 $z$ 进行积分得到的：

$$ p_\theta(x) = \int p(z) p_\theta(x|z) dz $$

这个积分消除了隐变量 $z$ 的影响，从而得到了仅依赖于观测数据 $x$ 的概率分布。

## 无限混合模型

“**无限混合模型**”（Infinite Mixture Model, IMM）是一个**概率建模概念**，指混合成分（mixture components）数量为**无穷多**（通常是不可数无穷）的混合模型。它不是指“模型参数无限多”，而是指**混合权重与组件按连续变量索引**，从而在数学上构成一个积分形式的混合分布。

一个标准的**有限混合模型**（Finite Mixture Model, FMM）形式为：
$$
p(x) = \sum_{k=1}^K \pi_k \cdot p_k(x), \quad \text{其中 } \pi_k \ge 0,\ \sum_{k=1}^K \pi_k = 1
$$
- $K$ 是有限整数（如高斯混合模型 GMM 中 $K=10$）；
- $\pi_k$ 是离散混合系数；
- $p_k(x)$ 是第 $k$ 个组件（如 $\mathcal{N}(x; \mu_k, \Sigma_k)$）。

而一个**无限混合模型**则将求和替换为**对连续隐变量 $z$ 的积分**：
$$
p(x) = \int_{\mathcal{Z}} p(z) \cdot p(x \mid z) \, dz
\tag{★}
$$
其中：
- $\mathcal{Z} \subseteq \mathbb{R}^d$ 是隐变量空间（连续）；
- $p(z)$ 是**先验密度**（提供“连续混合权重”）；
- $p(x \mid z)$ 是给定 $z$ 下的**条件生成分布**（即“混合组件”，每个 $z$ 对应一个组件）；
- 因 $z$ 连续，积分覆盖不可数无穷多个 $z$ → 故称“无限”。

无限混合模型 = 用连续隐变量 $z$ 作为“混合索引”，以先验密度 $p(z)$ 为权重、条件分布 $p(x|z)$ 为组件，通过积分 $\int p(z)p(x|z)dz$ 构建数据分布；VAE 正是这一思想的深度神经网络实现。

## 先验

“a simple prior” 就是 GAN 的“随机种子包”——它本身毫无智能，但提供了生成多样性的源头；它的“简单”是为了把复杂性全部甩给生成器神经网络，从而绕过概率建模的归一化地狱，实现高效、高质量的样本生成。



## 得分函数

**得分函数** = 概率分布对数的梯度
$$s(x) = \nabla_x \log p(x)$$

- 是统计学中经典概念（Fisher information 的基础）；

- 物理意义：指向**概率密度增长最快的方向**（即“上坡方向”）；

- 重要性质：**与归一化常数无关**！若根据蒙特卡洛采样，得到 $p(x) \approx \frac{1}{Z} \tilde{p}(x)$，则  
  $$
  \nabla_x \log p(x) = \nabla_x \log \tilde{p}(x) - \underbrace{\nabla_x \log Z}_{=0} = \nabla_x \log \tilde{p}(x)
  $$
  因 Z 是常数（不依赖 x），其梯度为 0！   **这就是“规避归一化”的数学根源！**

**实际意义**

- **指向性**：在点x处，得分函数指向**概率密度增加最快的方向**
- **大小性**：得分函数越大，说明该点离高概率区域越远
- **实用性**：可以用来"引导"样本从低概率区域走向高概率区域

**一句话总结**：得分函数就像是一个"概率指南针"，告诉你往哪个方向走能找到更多相似的数据点。

# 不错的句子

sparked renewed interest in generative modeling across diverse domains.

The remainder of this article is structured as follows.

including curated open-source implementations, educational materials, and deployment guidelines
