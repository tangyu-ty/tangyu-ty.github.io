---
layout: post
title: 'Generative AI Meets 6G and Beyond:  Diffusion Models for Semantic Communication(2)'
date: 2026-04-09 18:15 +0800
description: 续接上文
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

续接上文

# 3.扩散模型

DIFFUSION MODELS（扩散模型）


## 前几个模型的不足
将扩散模型与其他可以在通信接收方充当语义解码器的深层生成模型放在一起比较

Energy models的不足：

能量模型由于其难以处理的配分函数partition functions和对复杂采样技术的严重依赖，面临着重大的实际挑战。(
配分Z：由于能量密度函数$p(x) = \frac{exp(-E(x))}{Z}$,所以$Z = \sum exp(-E(x))$...)

自回归模型的不足：

ARM擅长生成具有精确可能性的离散令牌，但对于高维连续数据，其顺序采样的计算成本很高。
(因为图像中逐像素生成很消耗资源。)

变分自编码器的不足：

VAEs通过摊销推理提供快速采样，并构建可处理的近似后验，但由于变分界的局限性，它们通常会产生模糊的重建。
(VAE因变分推断的近似性和潜在空间的信息压缩，导致重构时出现模糊现象，这是为了获得可处理的后验分布和稳定训练所付出的代价。)

GAN的不足:
GAN提供了敏锐的感知质量和快速的单程推理，但存在模式崩溃和训练不稳定的问题，这可能会影响分布覆盖率.



扩散模型通过以更高的推理成本换取更优的分布覆盖率、通过去噪分数匹配目标进行稳定的训练、灵活的条件来规避这些限制通过引导机制进行采样，并且非常适合解决通信场景中的逆问题，这将在后面详细介绍。 并结合了随机后验采样、条件灵活性和对噪声测量的鲁棒性，所有这些都直接符合生成语义通信的要求[9]。

| 模型         | TS<br>训练稳定性 | CT<br>可控性 | GE<br>生成效率 | PQ<br>感知质量 | MD<br>模式多样性 | IP<br>可解释性 | LE<br>似然可评估性 |
|--------------|------------------|--------------|----------------|----------------|------------------|----------------|--------------------|
| EMs [22] | ● ○ ○            | ● ○ ○        | ● ○ ○          | ● ○ ○          | ◐ ◐ ○            | ● ● ●          | ● ○ ○              |
| ARMs [28]| ● ● ●            | ◐ ◐ ○        | ● ○ ○          | ◐ ◐ ○          | ● ● ●            | ● ● ●          | ● ● ●              |
| VAEs [27]| ● ● ●            | ◐ ◐ ○        | ● ● ●          | ● ○ ○          | ◐ ◐ ○            | ● ● ●          | ◐ ◐ ○              |
| GANs [29]| ● ○ ○            | ◐ ◐ ○        | ● ● ●          | ● ● ●          | ● ○ ○            | ● ○ ○          | ● ○ ○              |
| DMs [13] | ● ● ●            | ● ● ●        | ● ● ●          | ● ● ●          | ● ● ●            | ● ● ●          | ◐ ◐ ○              |


## Fundamentals of Diffusion Models

### Score Matching and Langevin Dynamics:

因为配分Z的难以处理，导致学习unnormalized 生成模型具有挑战，所以提出一个关键问题：

**我们如何从高维数据中有效地训练灵活的基于分数的扩散模型？** ，分数匹配/分数模型[21]，[34]，这是一种估计非标准化统计模型的方法。（分数socre指的是对数概率密度的梯度）


- Score Matching 的目标是：让模型学到的分数函数 $ s_\theta(x) $ 尽可能接近真实数据分布的分数函数 $ s_{\text{data}}(x) $。

- 本质是**匹配两个分布的“形状趋势”**，而非直接匹配概率值（因为后者需要归一化常数 $Z_\theta$，通常不可计算）。


按照之前的推理，**分数 $ s_\theta(x) = \nabla_x \log p_\theta(x) = \nabla_x f_\theta(x) $**（如果 $ p_\theta(x) \propto \exp(f_\theta(x)) $），而 $ \nabla_x \log Z_\theta = 0 $（因为 $Z_\theta$ 是常数，与 $x$ 无关），所以**分数天然不依赖 $Z_\theta$**！  
→ 因此，我们无需知道或计算 $Z_\theta$ 就能训练模型。


分数匹配这种方法在统计学的视角上，这种方法与最小化$p_{\text{data}}(x)$和$p_\theta(x)$的Fisher divergence。


- Fisher divergence 是一种衡量两个概率分布差异的度量，定义为：
  $$
  D_F(p_{\text{data}} \parallel p_\theta) := \mathbb{E}_{p_{\text{data}}(x)} \left[ \frac{1}{2} \| s(x) - s_\theta(x) \|^2_2 \right],\quad \text{(4)}
  $$
- 其中 $s(x) = \nabla_x \log p_{\text{data}}(x)$，$s_\theta(x) = \nabla_x \log p_\theta(x)$。
- $s_\theta(x)$不包含之前的$Z_\theta(x)$，虽然目标函数形式简洁，但**真实数据的分数 $s(x)$ 是未知的**（因为我们不知道 $p_{\text{data}}(x)$ 的解析形式）。所以直接优化这个目标仍不可行。
- 最小化 Fisher divergence 等价于最小化**期望下的分数误差平方**，这就是 Score Matching 的优化目标！


Score Matching的落地实现:

- 通过数学技巧（分部积分），可以将原本依赖 $s(x)$ 的目标，转化为一个**仅依赖模型分数 $s_\theta(x)$ 及其梯度**的目标。
- 原 Fisher divergence 可拆成两部分：一个**可优化的损失 $\mathcal{L}(\theta)$** + 一个**与参数 $\theta$ 无关的常数 $C$**。
- 优化时只需最小化 $\mathcal{L}(\theta)$ 即可（因为加常数不影响最优解）。


$$
\mathcal{L}(\theta) := \mathbb{E}_{p_{\text{data}}(x)} \left[ \frac{1}{2} \| s_\theta(x) \|^2 + \operatorname{tr}(\nabla_x s_\theta(x)) \right ],\quad \text{(5)}
$$

这就是**经典 Score Matching 的可计算损失函数**！

- 第一项 $\frac{1}{2} \| s_\theta(x) \|^2$：模型分数的模长平方（鼓励分数不要太大）。
- 第二项 $\operatorname{tr}(\nabla_x s_\theta(x))$：**分数的散度（divergence）**，即 Jacobian(雅可比)矩阵的迹（$\sum_i \partial s_\theta^{(i)} / \partial x_i$）。
- 注意：这里需要计算 **Hessian(海森)的迹**（因为 $ \nabla_x s_\theta(x) = \nabla_x^2 \log p_\theta(x) $ 是 Hessian），即二阶导数。

公式5是公式4推导得到的，详见附录，利用了分部积分和展开平方向

分部积分公式：$\int u \, dv = uv - \int v \, du$。这里令 $u = s_\theta(x)$，$dv = \nabla_x p_{\text{data}}(x) dx$。
那么 $du = \nabla_x s_\theta(x) dx$

实际上因为要计算二阶导数$ \nabla_x s_\theta(x) = \nabla_x^2 \log p_\theta(x) $,所以一般不用，计算太昂贵了。

所以引出了解决方案：**Denoising Score Matching (DSM)** ，它绕过了计算 Hessian 的需求！

---

DSM 不直接学原始数据的分数，而是学“加噪后数据”的分数，通过人为给数据加噪声（如高斯噪声），构造一个“易学”的中间分布。

$ q(\tilde{x}) = \int p_{\text{data}}(x) q(\tilde{x}|x) dx $

- $q(\tilde{x}|x)$ 是噪声过程（例如：$\tilde{x} = x + \epsilon, \epsilon \sim \mathcal{N}(0, \sigma^2 I)$）。
- $q(\tilde{x})$ 是所有原始数据加噪后的混合分布（更平滑、更易建模）。
- 这些带噪图的整体分布 $q(\tilde{x})$ 就是：**对所有可能的真实图 $x$，按其出现概率 $p_{\text{data}}(x)$ 加权，再叠加对应的噪声分布 $q(\tilde{x}\mid x)$**。
- 我们的目标变成：让模型分数 $s_\theta(\tilde{x})$ 去拟合 **噪声数据 $\tilde{x}$ 的真实分数** $ \nabla_{\tilde{x}} \log q(\tilde{x}) $。


由此产生的DSM目标变成

$$
\mathcal{J}(\theta) := \mathbb{E}_{p_{\text{data}}(x) q(\tilde{x}|x)} \left[ \frac{1}{2} \| \nabla_{\tilde{x}} \log q(\tilde{x}|x) - s_\theta(\tilde{x}) \|^2_2 \right]，\quad \text{(6)}
$$


关键点：**$ \nabla_{\tilde{x}} \log q(\tilde{x}|x) $ 是已知的！**  
  例如，若 $ q(\tilde{x}|x) = \mathcal{N}(\tilde{x}; x, \sigma^2 I) $，则：
$$
  \nabla_{\tilde{x}} \log q(\tilde{x}|x) = -\frac{1}{\sigma^2} (\tilde{x} - x)
$$
这是一个**闭式解**！不需要任何导数计算！

为什么要$\nabla_{\tilde{x}}q(\tilde{x}|x)$?因为分数可以理解为梯度，也就是趋势大小，梯度下降的方向。

✅ 所以 DSM 的损失函数**完全可计算**，且**无需二阶导数**，只需：
1. 采样真实数据 $x$；
2. 加噪得 $\tilde{x}$；
3. 计算已知的“噪声分数” $ -(\tilde{x}-x)/\sigma^2 $；
4. 让模型 $s_\theta(\tilde{x})$ 去拟合它。

但是 模型学到的是 $s_\theta(\tilde{x}) \approx \nabla_{\tilde{x}} \log q(\tilde{x})$（整体加噪分布的分数），不是原始 $p_{\text{data}}(x)$ 的分数。

但当我们用 Langevin dynamics 采样时，**从高斯噪声开始（高斯噪声由于其分析可处理性和理想的理论特性），逐步去噪**，最终能收敛到原始数据分布。

$s_\theta(x)$被训练好了后，驱动一个随机微分方程（SDE）或离散（郎之玩动力学）迭代（Langevin dynamics）来生成样本。

：Langevin dynamics（公式7）是一种离散的**马尔可夫链蒙特卡洛（MCMC）采样算法**，用于从目标分布中采样（即使不知道归一化常数）。

朗之万动力学实现了一个离散的MCMC过程，该过程从任意先验分布x0∼π（x）初始化，并迭代更新i=1，2，3...

$$
x_i = x_{i-1} + \zeta \, s_\theta(x_{i-1}) + \sqrt{2\zeta} \, \epsilon, \quad \epsilon \sim \mathcal{N}(0, I),\quad \text{(7)}
$$
这是**离散化的 Langevin 动力学更新规则**：
- $x_{i-1}$：当前状态；
- $\zeta$：步长（learning rate）；
- $s_\theta(x_{i-1})$：模型给出的分数（即 $\nabla_x \log p_\theta(x)$），提供**确定性梯度方向**（向高概率区域移动）；
- $\zeta s_\theta(x_{i-1})$：**去噪步骤**，分数指向概率上升方向，相当于“逆向扩散”；
- $\sqrt{2\zeta} \, \epsilon$：添加的**高斯噪声**，防止陷入局部模式，保证遍历性。
- 在连续时间极限下（步长无穷小、步数无穷多），Langevin dynamics 会**精确收敛到目标分布 $p_{\text{data}}(x)$**。（under mild regularity conditions.） 这是统计物理中著名的结论（Fokker-Planck 方程的稳态解即为目标分布）。

---

### 扩散模型的基于分数的建模管道
#### (a) Score Matching
- **输入**：加噪样本 $\tilde{x} = x + \epsilon$（来自真实数据 $x$）。
- **目标**：用神经网络 $s_\theta(\tilde{x})$ 学习分数  
  $$
  \nabla_{\tilde{x}} \log q(\tilde{x} \mid x) = -\frac{\epsilon}{\sigma^2}
  $$
- **图示**：箭头场 = 分数 $\nabla_x \log p(x)$，指向高概率区；模态中心梯度≈0。

#### (b) Stochastic Sampling
- **输入**：纯噪声 $x_0 \sim \mathcal{N}(0,I)$。
- **过程**：(Annealed) Langevin Dynamics  
  $$
  x_{k+1} = x_k + \frac{\epsilon_k}{2} s_\theta(x_k, \sigma_k) + \sqrt{\epsilon_k} \, z_k
  $$
  （步长 $\epsilon_k$ 和噪声水平 $\sigma_k$ 逐步衰减）
- **输出**：高质量样本（猫/狗等），落在高概率区域。

---


> **先学“梯度方向”（a）再沿梯度+噪声“爬坡生成”（b）分数是扩散模型的导航图。**


### Score-based Modeling with SDEs

基于随机微分方程（SDEs）的分数建模

这是在比标准的分数建模流程基础上发展的（通过训练神经网络估计数据分布的分数函数）


Song et al. [37]引入了一个统一框架， 该框架用 SDE（随机微分方程） 的视角，

将原本离散时间的扩散过程（如 DDPM 的逐步加噪/去噪）推广为连续时间的随机过程； 从而把“分数匹配”（训练目标）和“采样”（生成过程）都纳入同一个数学框架下即：

 - 前向过程：一个 SDE（如 Ornstein–Uhlenbeck 过程），将数据逐渐扰动成噪声；
- 反向过程：另一个 SDE（反向时间 SDE），从噪声还原出数据；
- 而反向 SDE 的漂移项依赖于**分数函数** ∇ₓ log pₜ(x)，因此训练目标就是学习这个分数函数。

该框架考虑了在连续时间内演变的连续中间分布，而不是用有限数量的噪声分布来干扰数据。
这种演变遵循规定的SDE，独立于数据，不包含可训练的参数。
然后，可以通过训练时间依赖的神经网络来估计得分函数，从而通过数值SDE求解器生成样本，从而推导出和近似逆时间SDE。

#### a) Perturbating Data with Forward SDEs:

**Step 1: 离散梯度下降（Gradient Descent）**
$$
\mathbf{x}_{i+1} = \mathbf{x}_i - \beta_i \nabla f(\mathbf{x}_i) \tag{8}
$$



**Step 2: 连续时间极限（Continuous Limit）→ ODE**
$$
\mathbf{x}_{i+1}-\mathbf{x}_i = - \beta_i \nabla f(\mathbf{x}_i)
$$
- $\beta_i$：离散时间步 $i$ 的学习率（步长）。
- $\beta(t)$：连续时间 $t$ 的学习率函数。

然后对t求导
$$
\frac{d\mathbf{x}(t)}{dt} = -\beta(t) \nabla f(\mathbf{x}(t)) \tag{9}
$$

**Step 3: 引入随机扰动（Stochastic Perturbation）**
从生成建模的角度来看，仅凭确定性动力学无法捕捉数据分布的随机性[20]。
将随机扰动纳入常微分方程得出：
$$
\frac{d\mathbf{x}(t)}{dt} = -\beta(t) \nabla f(\mathbf{x}(t)) + \mathbf{n}(t) \tag{10}
$$

**Step 4: 用 Wiener 过程（Brownian Motion）表示噪声**
接下来如何建立噪声？
给定具有独立高斯增量的标准的Wiener process（维纳过程 / 布朗运动）$\mathbf{w}(t + \Delta t) - \mathbf{w}(t) \sim \mathcal{N}(\mathbf{0}, \Delta t \, \mathbf{I})$，噪声可以表示为$\mathbf{n}(t)=\frac{d\mathbf{w}(t)}{dt}$,最后在两边同时乘以一个$dt$.
$$
d\mathbf{x}(t) = -\beta(t) \nabla f(\mathbf{x}(t)) \, dt + d\mathbf{w}(t) \tag{11}
$$

- 这个导数**在经典微积分中不存在**（因为布朗运动路径不可微）！但在**随机微积分（Itô calculus）**中，我们不直接处理$\frac{d\mathbf{w}(t)}{dt}$ ，而是处理**微分形式** $d\mathbf{w}(t)$ ，它是良定义的（作为随机测度）。



**Step 5: 推广为通用前向 SDE（General Forward SDE）**

虽然这个方程提供了梯度下降的直观性，但扩散模型需要更通用的动力学来有效地将数据分布转换为可处理的先验。因此，**时间间隔[0，t]上的正向扩散过程$\{ \mathbf{x}(t) \}_{t \in [0, T]}$被建模为ItoˆSDE的解：**


$$
\underbrace{d\mathbf{x}(t)}_{\text{状态变化}} 
= \underbrace{\big[-\beta(t) \nabla f(\mathbf{x}(t))\big]}_{\mathbf{f}(\mathbf{x}, t)} dt 
+ \underbrace{1}_{g(t)} \, d\mathbf{w}(t)
\quad \xrightarrow{\text{定义}} \quad
d\mathbf{x} = \mathbf{f}(\mathbf{x}, t)\, dt + g(t)\, d\mathbf{w}(t)
$$



$$
d\mathbf{x} = \mathbf{f}(\mathbf{x}, t) \, dt + g(t) \, d\mathbf{w}(t) \tag{12}
$$
- $\mathbf{f}(\mathbf{x}, t)$：**漂移系数**（drift coefficient），定义了状态 $\mathbf{x}$ 在时间 $t$ 的确定性演变趋势。
- $g(t)$：**扩散系数**（diffusion coefficient），标量函数，控制噪声 $d\mathbf{w}(t)$ 的强度。

如果我们暂时“关闭”扩散项（令 $g(t)=0$），方程就变成了一个普通的常微分方程（ODE）$d\mathbf{x} = \mathbf{f}(\mathbf{x}, t) \, dt $：

#### b) Sampling Data with Reverse SDEs:

上面的是前向扩散过程，后面的是反向扩散过程

**基于分数的生成建模（Score-based Generative Modeling）**中，利用**反向随机微分方程（Reverse SDE）**进行数据采样



我们要做的是**生成新样本**，即从噪声 $ \mathbf{x}(T) \sim p_T $ 出发，**逆向演化**回真实数据分布 $ \mathbf{x}(0) \sim p_0 $。

但直接逆转前向SDE不可行（因为SDE是随机的，且信息丢失），因此需要借助**概率流**和**分数函数（score function）**。

分数函数定义为对数概率密度的梯度$\nabla_{\mathbf{x}} \log p_t(\mathbf{x})$,其中 $ p_t(\mathbf{x}) $ 是时间 $ t $ 时的边际分布（即前向过程在时间 $ t $ 的分布）。并通过一个神经网络 $ s_\theta(\mathbf{x}, t) $ 来**近似**它，$s_\theta(\mathbf{x}, t) \approx \nabla_{\mathbf{x}} \log p_t(\mathbf{x})$,这就是所谓的 **score matching** 或 **denoising score matching**。

根据**时间反转SDE理论**（Song et al., 2020），若前向SDE为：
$$
d\mathbf{x} = f(\mathbf{x}, t)\,dt + g(t)\,d\mathbf{w}
$$
则其对应的**反向过程**（时间从 $ T \to 0 $）满足以下SDE：
$$
d\mathbf{x} = \left[ f(\mathbf{x}, t) - g^2(t) \, \nabla_{\mathbf{x}} \log p_t(\mathbf{x}) \right] dt + g(t)\, d\bar{\mathbf{w}}
\tag{13}
$$

其中：
- $ d\bar{\mathbf{w}} $ 是**反向时间下的Wiener过程**（即当时间倒流时，布朗运动仍满足相同统计性质，但方向相反）；
- $ dt $ 是**负无穷小时间步**（因为时间在倒流，所以实际模拟时我们用 $ -dt $ 或从 $ T $ 向 0 积分）；
- 关键项：$ -g^2(t) \nabla_{\mathbf{x}} \log p_t(\mathbf{x}) $ 是**修正的漂移项**，称为 **reverse drift**。

> ✅ 为什么需要减去分数项？
> - 前向漂移 $ f(\mathbf{x}, t) $ 会把数据推向噪声；
> - 要逆转它，必须加上一个“反向力”，而这个力正是由分数函数提供的梯度方向；
> - 本质上，这是**Langevin dynamics**的一种连续形式：系统沿概率密度的梯度上升。

### 
一旦我们训练好分数网络 $ s_\theta(\mathbf{x}, t) $，就可以用数值方法（如Euler-Maruyama）求解反向SDE：

1. **初始化**：从简单分布采样 $ \mathbf{x}(T) \sim p_T $（如 $ \mathcal{N}(0, I) $）；
2. **离散化反向SDE**（时间步 $ t_n = T - n\Delta t $）：
   $$
   \mathbf{x}_{n} = \mathbf{x}_{n+1} - \left[ f(\mathbf{x}_{n+1}, t_{n+1}) - g^2(t_{n+1}) s_\theta(\mathbf{x}_{n+1}, t_{n+1}) \right] \Delta t + g(t_{n+1}) \sqrt{\Delta t} \, {\epsilon}_n
   $$
   其中 $ {\epsilon}_n \sim \mathcal{N}(0, I) $；
3. **迭代至 $ t=0 $**：得到 $ \mathbf{x}(0) $，即生成样本。

> ⚠️ 注意：有些实现（如DDPM）使用**确定性ODE**（概率流ODE）替代SDE，此时去掉随机项 $ g(t)d\bar{\mathbf{w}} $；而SDE版本保留随机性，可提高多样性。

#### c) Unifying Diffusion Models with VE and VP SDEs:

通过将离散的“去噪分数匹配”（Denoising Score Matching, DSM）目标推广到**连续时间**，可将主流扩散模型（如 DDPM、SDE-DDPM、NCSN 等）纳入两个统一的 SDE 框架：

这两种推导都分为四个步骤：（i）将离散噪声调度重新参数化为时间的连续函数；（ii）Taylor展开离散正向递归，取步长∆t→ 0 识别正向SDE的漂移和扩散系数；（iii）将这些系数代入逆时间SDE，以获得相应的逆动力学；以及（iv）对得到的逆SDE进行重新离散化，以验证原始离散采样递归是否已恢复。

---

**表 III：通过 VE 和 VP SDE 对扩散模型的统一概述** 
同时给出了连续时间 SDE（随机微分方程）公式和离散时间递推公式。  

**符号说明**：  
- **w**, **ŵ**：前向与反向维纳过程（Wiener processes）；  
- **ε ∼ 𝒩(0, I)**：标准高斯噪声；  
- **s(x, t) = ∇ₓ log pₜ(x)**：得分函数（score function）；  
- **σ(t), σᵢ**：VE SDE 的噪声调度（noise schedule）；  
- **β(t), βᵢ**：VP SDE 的噪声调度。

| 模型                     | 过程 | 连续时间 SDE                                                 | 离散时间递推                                                 |
| ------------------------ | ---- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **VE SDE**（例如，SMLD） | 前向 | $ \mathrm{d}x = \sqrt{\frac{\mathrm{d}[\sigma^2(t)]}{\mathrm{d}t}} \, \mathrm{d}w $ | $ x_i = x_{i-1} + \sqrt{\sigma_i^2 - \sigma_{i-1}^2} \, \varepsilon $ |
|                          | 反向 | $ \mathrm{d}x = -\frac{\mathrm{d}[\sigma^2(t)]}{\mathrm{d}t} \, s(x,t) \, \mathrm{d}t + \sqrt{\frac{\mathrm{d}[\sigma^2(t)]}{\mathrm{d}t}} \, \mathrm{d}\bar{w} $ | $ x_{i-1} = x_i + (\sigma_i^2 - \sigma_{i-1}^2) \, s(x_i) + \sqrt{\sigma_i^2 - \sigma_{i-1}^2} \, \varepsilon $ |
| **VP SDE**（例如，DDPM） | 前向 | $ \mathrm{d}x = -\frac{1}{2} \beta(t) x \, \mathrm{d}t + \sqrt{\beta(t)} \, \mathrm{d}w $ | $ x_i = \sqrt{1 - \beta_i} \, x_{i-1} + \sqrt{\beta_i} \, \varepsilon $ |
|                          | 反向 | $ \mathrm{d}x = -\beta(t) \left[ \frac{1}{2} x + s(x,t) \right] \mathrm{d}t + \sqrt{\beta(t)} \, \mathrm{d}\bar{w} $ | $ x_{i-1} = \frac{1}{\sqrt{1 - \beta_i}} \left[ x_i + \frac{\beta_i}{2} s(x_i) \right] + \sqrt{\beta_i} \, \varepsilon $ |

---

注：  
- “VE” 表示 *Variance Exploding*（方差爆炸），典型代表为 SMLD（Score Matching with Langevin Dynamics）；  
- “VP” 表示 *Variance Preserving*（方差保持），典型代表为 DDPM（Denoising Diffusion Probabilistic Models）；  
- 离散时间递推中，下标 $ i $ 通常表示时间步（从 $ i=0 $ 到 $ N $，其中 $ x_N $ 是噪声样本，$ x_0 $ 是生成样本）；  
- $ \varepsilon $ 在离散递推中为独立同分布的标准高斯噪声；  
- 反向 SDE 中的 $ \bar{w} $ 是反向时间下的维纳过程（即时间反转的布朗运动）。  



VE和VP SDE均具有仿射漂移系数，因此它们的扰动核 $p(\mathbf{x}(t) | \mathbf{x}(0))$ 是高斯分布，且具有闭式表达式，这使得训练过程特别高效。







## Solving Reverse Process with Probability Flow ODEs

等式（13）中的逆时间SDE通过迭代随机去噪生成样本。虽然随机性确保了样本的多样性，但它引入了计算开销，并限制了需要确定性的任务（例如数据压缩、语义操纵）的可控性。这激发了一种确定性的替代方案

具有相同边际密度 ${p_t(x)}_{t\in [0,T]} $的概率流常微分方程 (PF ODE)：
$$
\mathrm{d}\mathbf{x} = \left[ \boldsymbol{f}(\mathbf{x}, t) - \frac{1}{2} g^2(t) \boldsymbol{s}(\mathbf{x}, t) \right] \mathrm{d}t. \tag{23}
$$
使用学习到的分数 $s_θ{(x, t)}$，方程 (23) 变为一个可通过数值方法求解的神经 ODE [42]。通常使用三种求解器族，在标量 ODE 上说明如下：
$$
\frac{\mathrm{d}x(t)}{\mathrm{d}t} = f(x(t), t), \quad x(t_0) = x_0. \tag{24}
$$
**a) Euler-Maruyama 方法：** 最简单的一阶方法迭代如下：
$$
x_{i+1} = x_i + \eta f(x_i, t_i), \quad i = 0, 1, \ldots, N-1, \tag{25}
$$
其中 $\eta = t_{i+1} - t_i$ 是步长，产生局部误差 $\mathcal{O}(\eta^2)$。对于扩散模型，这对应于 SMLD 和 DDPM 中的祖先采样，提供了计算效率。


---
**b) Runge-Kutta 方法：** 高阶 RK 方法（例如，二阶 RK，也称为 Heun 方法）通过多次中间评估来提高精度。经典的 RK-4 迭代如下：
$$
x_{i+1} = x_i + \frac{\eta}{6} (k_1 + 2k_2 + 2k_3 + k_4), \tag{26}
$$
其中 $k_1,k_2,k_3,k_4$ 是相对于 $\eta$ 的中间斜率。RK-4 实现局部误差 $\mathcal{O}(\eta^5)$，以额外的分数评估为代价提供更高的样本质量。

**c) 预测-校正方法：** 该方法在预测步骤（来自表 III 的任何反向时间 SDE 数值求解器）和校正步骤（任何基于分数的 MCMC 方法）之间交替，平衡效率和质量，如图 7 所示。











# 公式推导

## 公式4->公式5
好的，我们来详细解释公式 (5)：

$$
\mathcal{L}(\theta) := \mathbb{E}_{p_{\text{data}}(x)} \left[ \frac{1}{2} \| s_\theta(x) \|^2 + \operatorname{tr}(\nabla_x s_\theta(x)) \right]
$$

---

**🔍 公式 (5) 是什么？**

这是 **经典 Score Matching 方法** 的**可计算的损失函数**。它的目标是训练一个模型分数函数 $ s_\theta(x) $，使其逼近真实数据分布的分数函数 $ s(x) = \nabla_x \log p_{\text{data}}(x) $。

---

**🧠 它是怎么来的？（从公式 (4) 推导而来）**

回忆一下，原始目标是最小化 Fisher divergence（公式 4）：

$$
D_F(p_{\text{data}} \parallel p_\theta) = \mathbb{E}_{p_{\text{data}}(x)} \left[ \frac{1}{2} \| s(x) - s_\theta(x) \|^2 \right]
$$

展开平方项：

$$
= \mathbb{E}_{p_{\text{data}}} \left[ \frac{1}{2} \| s(x) \|^2 - s(x)^T s_\theta(x) + \frac{1}{2} \| s_\theta(x) \|^2 \right]
$$

$$
= \underbrace{\mathbb{E}_{p_{\text{data}}} \left[ \frac{1}{2} \| s(x) \|^2 \right]}_{C_1} - \underbrace{\mathbb{E}_{p_{\text{data}}} \left[ s(x)^T s_\theta(x) \right]}_{(*)} + \underbrace{\mathbb{E}_{p_{\text{data}}} \left[ \frac{1}{2} \| s_\theta(x) \|^2 \right]}_{(**)}
$$

- $C_1$ 是关于 $\theta$ 的常数，可以忽略。
- $(*)$ 项 $\mathbb{E}_{p_{\text{data}}} \left[ s(x)^T s_\theta(x) \right]$ 包含了**真实分数 $s(x)$**，而 $s(x) = \nabla_x \log p_{\text{data}}(x)$ 是未知的，无法直接计算。

**关键突破：利用分部积分 (Integration by Parts) 来处理 $(*)$ 项。**

---

**⚡ 分部积分的魔法**

考虑 $(*)$ 项：

$$
\mathbb{E}_{p_{\text{data}}(x)} \left[ s(x)^T s_\theta(x) \right] = \int p_{\text{data}}(x) \left( \nabla_x \log p_{\text{data}}(x) \right)^T s_\theta(x) \, dx
$$

由于 $ \nabla_x \log p_{\text{data}}(x) = \frac{\nabla_x p_{\text{data}}(x)}{p_{\text{data}}(x)} $，代入得：

$$
= \int \left( \nabla_x p_{\text{data}}(x) \right)^T s_\theta(x) \, dx
$$

现在应用分部积分公式：$\int u \, dv = uv - \int v \, du$。这里令 $u = s_\theta(x)$，$dv = \nabla_x p_{\text{data}}(x) dx$。

那么 $du = \nabla_x s_\theta(x) dx$（这是一个雅可比矩阵，即 $[\partial s_\theta^{(i)} / \partial x_j]$），$v = p_{\text{data}}(x)$。

$$
\Rightarrow \int \left( \nabla_x p_{\text{data}}(x) \right)^T s_\theta(x) \, dx = \left[ p_{\text{data}}(x) s_\theta(x) \right]_{-\infty}^{+\infty} - \int p_{\text{data}}(x) \left( \nabla_x s_\theta(x) \right) dx
$$

- 边界项 $\left[ p_{\text{data}}(x) s_\theta(x) \right]_{-\infty}^{+\infty}$ 通常假设为零（因为概率密度 $p_{\text{data}}(x)$ 在无穷远处趋于零，或者 $s_\theta(x)$ 的增长不足以抵消它）。
- 因此，上式简化为：

$$
= - \int p_{\text{data}}(x) \left( \nabla_x s_\theta(x) \right) dx
$$

这里的 $\nabla_x s_\theta(x)$ 是一个矩阵（雅可比矩阵），其迹（trace）是 $\operatorname{tr}(\nabla_x s_\theta(x))$。而上面的积分实际上是**迹的期望**：

$$
(*) = \mathbb{E}_{p_{\text{data}}} \left[ s(x)^T s_\theta(x) \right] = - \mathbb{E}_{p_{\text{data}}} \left[ \operatorname{tr}(\nabla_x s_\theta(x)) \right]
$$

---

**🧮 代回原式**

将 $(*)$ 的结果代回展开式：

$$
D_F = C_1 - \left( - \mathbb{E}_{p_{\text{data}}} \left[ \operatorname{tr}(\nabla_x s_\theta(x)) \right] \right) + \mathbb{E}_{p_{\text{data}}} \left[ \frac{1}{2} \| s_\theta(x) \|^2 \right]
$$

$$
= C_1 + \mathbb{E}_{p_{\text{data}}} \left[ \operatorname{tr}(\nabla_x s_\theta(x)) \right] + \mathbb{E}_{p_{\text{data}}} \left[ \frac{1}{2} \| s_\theta(x) \|^2 \right]
$$

忽略常数 $C_1$，并合并期望项，就得到了公式 (5)：

$$
\mathcal{L}(\theta) = \mathbb{E}_{p_{\text{data}}(x)} \left[ \frac{1}{2} \| s_\theta(x) \|^2 + \operatorname{tr}(\nabla_x s_\theta(x)) \right]
$$

---
**📌 公式 (5) 各部分含义**

- $\| s_\theta(x) \|^2$：模型分数的平方模长。这项鼓励模型分数不要太“大”，起到正则化作用。
- $\operatorname{tr}(\nabla_x s_\theta(x))$：模型分数函数 $s_\theta(x)$ 的**散度**（divergence）。这是**分数函数的梯度的迹**，即 $ \sum_{i=1}^d \frac{\partial s_\theta^{(i)}(x)}{\partial x_i} $，其中 $s_\theta^{(i)}$ 是 $s_\theta$ 的第 $i$ 个分量。
- 这个损失函数不再包含未知的真实分数 $s(x)$，只包含模型分数 $s_\theta(x)$ 及其梯度（即一阶和二阶导数）。

---

**⚠️ 为什么公式 (5) 不实用？**

虽然公式 (5) 消除了对真实分数 $s(x)$ 的依赖，但它引入了一个新问题：需要计算**雅可比矩阵的迹 $\operatorname{tr}(\nabla_x s_\theta(x))$**，也就是对模型输出 $s_\theta(x)$ 的每个分量再求一次梯度（$d$ 次反向传播，其中 $d$ 是 $x$ 的维度）。对于图像等高维数据，这在计算上非常昂贵。

因此，后续引入了 **Denoising Score Matching (DSM)**（公式 6）来绕过这个难题，通过加噪的方式避免了计算 Hessian 矩阵（或其迹）。



## 雅可比矩阵

**雅可比矩阵 (Jacobian Matrix)**。

雅可比矩阵是多元向量函数的一阶偏导数矩阵。它描述了**一个向量值函数在某一点的最佳线性逼近**。

---

📌 定义

假设有一个函数 $ f: \mathbb{R}^n \to \mathbb{R}^m $，即输入是一个 $ n $ 维向量 $ x = (x_1, x_2, \dots, x_n)^T $，输出是一个 $ m $ 维向量 $ y = (y_1, y_2, \dots, y_m)^T $，且 $ y = f(x) $。

我们可以把 $ f $ 写成分量形式：

$$
y_1 = f_1(x_1, x_2, \dots, x_n) \\
y_2 = f_2(x_1, x_2, \dots, x_n) \\
\vdots \\
y_m = f_m(x_1, x_2, \dots, x_n)
$$

那么，$ f $ 在点 $ x $ 处的雅可比矩阵 $ J_f(x) $ 是一个 $ m \times n $ 的矩阵，定义如下：

$$
J_f(x) = \begin{bmatrix}
\frac{\partial f_1}{\partial x_1} & \frac{\partial f_1}{\partial x_2} & \cdots & \frac{\partial f_1}{\partial x_n} \\
\frac{\partial f_2}{\partial x_1} & \frac{\partial f_2}{\partial x_2} & \cdots & \frac{\partial f_2}{\partial x_n} \\
\vdots & \vdots & \ddots & \vdots \\
\frac{\partial f_m}{\partial x_1} & \frac{\partial f_m}{\partial x_2} & \cdots & \frac{\partial f_m}{\partial x_n}
\end{bmatrix}
$$

- **行 (Row)**：对应输出向量 $ y $ 的一个分量（$ f_1, f_2, \dots, f_m $）。
- **列 (Column)**：对应输入向量 $ x $ 的一个分量（$ x_1, x_2, \dots, x_n $）。
- **元素 (Element)**：矩阵中的元素 $ (J_f)_{ij} = \frac{\partial f_i}{\partial x_j} $，表示输出 $ y $ 的第 $ i $ 个分量对输入 $ x $ 的第 $ j $ 个分量的偏导数。

---

 🧠 直观理解

- 雅可比矩阵告诉我们，当输入 $ x $ 发生一个微小变化 $ \Delta x $ 时，输出 $ y = f(x) $ 会如何变化。
- 数学上，这个关系可以用线性近似表示：
  $$
  f(x + \Delta x) \approx f(x) + J_f(x) \cdot \Delta x
  $$
  这意味着雅可比矩阵 $ J_f(x) $ 是函数 $ f $ 在点 $ x $ 附近的**最佳线性逼近**。

---

 🧪 举个例子

假设 $ f: \mathbb{R}^2 \to \mathbb{R}^3 $，定义为：

$$
f(x_1, x_2) = \begin{pmatrix}
f_1(x_1, x_2) \\
f_2(x_1, x_2) \\
f_3(x_1, x_2)
\end{pmatrix} =
\begin{pmatrix}
x_1^2 + x_2 \\
\sin(x_1) + x_2^2 \\
e^{x_1} x_2
\end{pmatrix}
$$

它的雅可比矩阵 $ J_f(x_1, x_2) $ 是一个 $ 3 \times 2 $ 矩阵：

$$
J_f(x_1, x_2) = \begin{bmatrix}
\frac{\partial f_1}{\partial x_1} & \frac{\partial f_1}{\partial x_2} \\
\frac{\partial f_2}{\partial x_1} & \frac{\partial f_2}{\partial x_2} \\
\frac{\partial f_3}{\partial x_1} & \frac{\partial f_3}{\partial x_2}
\end{bmatrix} =
\begin{bmatrix}
2x_1 & 1 \\
\cos(x_1) & 2x_2 \\
e^{x_1} x_2 & e^{x_1}
\end{bmatrix}
$$

- 第一行 $[2x_1, 1]$ 表示 $f_1$ 对 $x_1$ 和 $x_2$ 的偏导数。
- 第二行 $[\cos(x_1), 2x_2]$ 表示 $f_2$ 对 $x_1$ 和 $x_2$ 的偏导数。
- 第三行 $[e^{x_1} x_2, e^{x_1}]$ 表示 $f_3$ 对 $x_1$ 和 $x_2$ 的偏导数。

---

 🧮 回到公式 (5)

在公式 (5) 中，我们遇到的是 $ s_\theta(x) $ 的雅可比矩阵，即 $ \nabla_x s_\theta(x) $。

- $ s_\theta(x) $ 是一个**向量值函数**：输入 $ x \in \mathbb{R}^d $（例如，一张图像），输出也是一个 $ d $-维向量（即分数 $ \nabla_x \log p_\theta(x) $，它与 $ x $ 同维度）。
- 所以 $ s_\theta: \mathbb{R}^d \to \mathbb{R}^d $，它的雅可比矩阵 $ \nabla_x s_\theta(x) $ 是一个 $ d \times d $ 的矩阵。

$$
\nabla_x s_\theta(x) = J_{s_\theta}(x) = \begin{bmatrix}
\frac{\partial s_\theta^{(1)}}{\partial x_1} & \frac{\partial s_\theta^{(1)}}{\partial x_2} & \cdots & \frac{\partial s_\theta^{(1)}}{\partial x_d} \\
\frac{\partial s_\theta^{(2)}}{\partial x_1} & \frac{\partial s_\theta^{(2)}}{\partial x_2} & \cdots & \frac{\partial s_\theta^{(2)}}{\partial x_d} \\
\vdots & \vdots & \ddots & \vdots \\
\frac{\partial s_\theta^{(d)}}{\partial x_1} & \frac{\partial s_\theta^{(d)}}{\partial x_2} & \cdots & \frac{\partial s_\theta^{(d)}}{\partial x_d}
\end{bmatrix}
$$

其中 $ s_\theta^{(i)} $ 是 $ s_\theta $ 的第 $ i $ 个分量。

- **公式 (5) 中的 $\operatorname{tr}(\nabla_x s_\theta(x))$**：这是雅可比矩阵的**迹 (Trace)**，即**主对角线元素之和**。
  $$
  \operatorname{tr}(\nabla_x s_\theta(x)) = \sum_{i=1}^d \frac{\partial s_\theta^{(i)}(x)}{\partial x_i}
  $$
  这个量在数学上被称为**向量场 $s_\theta(x)$ 的散度 (Divergence)**。它衡量了 $ s_\theta(x) $ 在点 $ x $ 处的“发散”程度。

🧵 总结

- **雅可比矩阵**：描述一个**向量函数**相对于其输入的**一阶偏导数**的矩阵。
- **在公式 (5) 中**：它是 $ s_\theta(x) $ 的雅可比矩阵，用于计算其迹（散度），以构成无需真实分数的可计算损失函数。
- **计算成本**：对于 $ d $ 维输入/输出，雅可比矩阵是 $ d \times d $ 的。计算其迹（散度）需要对 $ d $ 个对角元素求导，通常需要 $ d $ 次反向传播，对于大 $ d $（如图像）计算量巨大，这就是 DSM 要解决的问题。


## tr 迹


 📌 定义

**迹 (Trace)** 是**方阵 (square matrix)** 的一个重要属性。对于一个 $n \times n$ 的方阵 $A$，它的**迹**被定义为该矩阵**主对角线元素的总和**。

$$
\operatorname{tr}(A) = \sum_{i=1}^n A_{ii}
$$

其中 $A_{ii}$ 是矩阵 $A$ 的第 $i$ 行第 $i$ 列的元素。
🧠 直观理解

- 它是一个**标量 (scalar)**（即一个数值），而不是矩阵。
- 它只对方阵有意义。
- 它是矩阵的**特征值之和**（这是迹的一个重要性质）。
- 它具有**循环性质**：对于矩阵乘积，$\operatorname{tr}(ABC) = \operatorname{tr}(CAB) = \operatorname{tr}(BCA)$。

---

 🧮 回到公式 (5)

在公式 (5) 中：

$$
\mathcal{L}(\theta) := \mathbb{E}_{p_{\text{data}}(x)} \left[ \frac{1}{2} \| s_\theta(x) \|^2 + \operatorname{tr}(\nabla_x s_\theta(x)) \right]
$$

我们之前解释了 $\nabla_x s_\theta(x)$ 是 $s_\theta(x)$ 的**雅可比矩阵**，它是一个 $d \times d$ 的方阵（因为 $s_\theta(x)$ 是一个 $d$ 维向量函数，输入也是 $d$ 维）。

所以，

$$
\operatorname{tr}(\nabla_x s_\theta(x)) = \sum_{i=1}^d \frac{\partial s_\theta^{(i)}(x)}{\partial x_i}
$$

- 这个量在数学上被称为**向量场 $s_\theta(x)$ 的散度 (Divergence)**。
- 它衡量了分数函数 $s_\theta(x)$ 在点 $x$ 处的“发散”程度。

## 漂移

🔍 什么是“漂移”（Drift）

**漂移**是指粒子在 **确定性力场** $\mathbf{f}(\mathbf{x}, t)$ 作用下的 **平均运动趋势**。

- 它是一个 **向量场**：对于空间中的每一个点 $\mathbf{x}$ 和时间 $t$，$\mathbf{f}(\mathbf{x}, t)$ 都会给出一个方向和大小。
- 它是 **确定性的**：如果你知道粒子在某个时刻的位置 $\mathbf{x}(t)$，那么在无穷小时间 $dt$ 内，它会被“推”向 $\mathbf{f}(\mathbf{x}, t) \cdot dt$ 的方向。

🌍 类比：河流中的落叶

想象一片叶子漂浮在河面上：

- **河水的流向和流速**（即水流的向量场）就像 **漂移项 $\mathbf{f}(\mathbf{x}, t)$**。无论叶子本身如何摆动，它都会被水流朝着某个方向推动。
- **水面的波浪和湍流** 就像 **扩散项 $g(t)d\mathbf{w}$**。它会让叶子产生随机的、小幅度的晃动和偏移。

**漂移**决定了“总体趋势”，而**扩散**增加了“不确定性”。

🧠 用公式直观理解

SDE：
$$
d\mathbf{x} = \mathbf{f}(\mathbf{x}, t)\,dt + g(t)\,d\mathbf{w}
$$

如果我们暂时“关闭”扩散项（令 $g(t)=0$），方程就变成了一个普通的常微分方程（ODE）：
$$
d\mathbf{x} = \mathbf{f}(\mathbf{x}, t)\,dt
$$

这时，粒子的运动是**完全确定的**，它会严格按照向量场 $\mathbf{f}$ 的指引移动。这就是“漂移”本身的贡献。



## 备注I（从SMLD推导出连续时间VE SDE）

**SMLD**（Score-based Generative Modeling with Langevin Dynamics）[40] 是一种生成模型方法，通过在多个噪声尺度上训练 score 函数（即对数数据密度的梯度： $\Delta xlogp(x) $，再用 Langevin dynamics 采样生成样本。

核心思想是离散变连续，关于从 SMLD（Score Matching with Langevin Dynamics）推导出**连续时间 VE（Variance Exploding）SDE(随机微分方程)** 的数学过程。

要理解公式(16)的推导，需结合**正向SDE的形式**、**反向SDE的一般理论**以及**原文中的参数代入**，分步骤分析：

**步骤1：明确正向SDE的形式（公式15）**  

连续时间极限下的**正向VE SDE**（Variance Exploding SDE）为：  

[$dw$等等的推导可以看Perturbating Data with Forward SDEs:章节](#a-Perturbating Data with Forward SDEs:)
$$
\mathrm{d}\mathbf{x} = g(t) \, \mathrm{d}\mathbf{w}, \tag{15}
$$


其中：  

- 漂移项（drift term）$ f(\mathbf{x}, t) = 0$（无确定性漂移，原文明确“with zero drift $ f(\mathbf{x}, t) = 0 $”）；  
- 扩散系数 $ g(t) = \sqrt{\frac{\mathrm{d}[\sigma^2(t)]}{\mathrm{d}t}} $（由噪声水平 $ \sigma(t) $ 的导数决定）；  
- $ \mathrm{d}\mathbf{w} $ 是**Wiener过程（布朗运动）的微分**（噪声项）。  

**步骤2：反向SDE的一般推导公式（原文中“Eq.(13)”）**  

对于**正向SDE**的一般形式：  
$$
\mathrm{d}\mathbf{x} = f(\mathbf{x}, t) \, \mathrm{d}t + g(\mathbf{x}, t) \, \mathrm{d}\mathbf{w},
$$
其对应的**反向SDE**（用于从噪声分布生成数据分布，即“去噪”过程）形式为：  
$$
\mathrm{d}\mathbf{x} = \left[ f(\mathbf{x}, t) - g(\mathbf{x}, t)^2 \, \mathbf{s}(\mathbf{x}, t) \right] \mathrm{d}t + g(\mathbf{x}, t) \, \mathrm{d}\tilde{\mathbf{w}}, \tag{13}
$$
其中：  

- $ \mathbf{s}(\mathbf{x}, t) $ 是**分数函数（score function）**，定义为 $ \mathbf{s}(\mathbf{x}, t) = -\nabla_{\mathbf{x}} \log p_t(\mathbf{x}) $（$ p_t(\mathbf{x}) $ 是 $ t $ 时刻的分布密度，分数函数衡量分布的对数梯度，指导去噪）；  
- $ \mathrm{d}\tilde{\mathbf{w}} $ 是**倒向Wiener过程**（时间倒向后的噪声项，与正向 $ \mathrm{d}\mathbf{w} $ 相关，但方向相反）。  

**步骤3：代入正向SDE的参数（公式15）**  

将正向SDE（公式15）的参数 $ f(\mathbf{x}, t) = 0 $ 和 $ g(t) = \sqrt{\frac{\mathrm{d}[\sigma^2(t)]}{\mathrm{d}t}} $ 代入反向SDE的一般公式（Eq.13）：  
1. **漂移项计算**： 
   正向SDE的漂移项 $ f(\mathbf{x}, t) = 0 $，因此反向SDE的漂移项为： 
   $$
   f(\mathbf{x}, t) - g(t)^2 \, \mathbf{s}(\mathbf{x}, t) = 0 - \left( \sqrt{\frac{\mathrm{d}[\sigma^2(t)]}{\mathrm{d}t}} \right)^2 \mathbf{s}(\mathbf{x}, t) = -\frac{\mathrm{d}[\sigma^2(t)]}{\mathrm{d}t} \, \mathbf{s}(\mathbf{x}, t).
   $$
2. **扩散项计算**： 
   正向SDE的扩散系数 $ g(t) = \sqrt{\frac{\mathrm{d}[\sigma^2(t)]}{\mathrm{d}t}} $，因此反向SDE的扩散项保持不变： 
   $$
   g(t) \, \mathrm{d}\tilde{\mathbf{w}} = \sqrt{\frac{\mathrm{d}[\sigma^2(t)]}{\mathrm{d}t}} \, \mathrm{d}\tilde{\mathbf{w}}.
   $$

**步骤4：得到公式(16)**  

将上述漂移项和扩散项代入反向SDE的一般形式（Eq.13），即可得到原文中的**公式(16)**：

$\alpha(t) = \frac{d[\sigma^2(t)]}{dt}\alpha\left(\frac{i}{N}\right) \approx \frac{\sigma*_{i+1}^2 - \sigma_*i^2}{\Delta t} = N(\sigma*_{i+1}^2 - \sigma_*i^2)$
$$
\mathrm{d}\mathbf{x} = -\frac{\mathrm{d}[\sigma^2(t)]}{\mathrm{d}t} \, \mathbf{s}(\mathbf{x}, t) \, \mathrm{d}t + \sqrt{\frac{\mathrm{d}[\sigma^2(t)]}{\mathrm{d}t}} \, \mathrm{d}\tilde{\mathbf{w}}. \tag{16}
$$

最后得到
$$
x_{i-1} = x_i + (\sigma_i^2 - \sigma_{i-1}^2)s(x_i) + \sqrt{\sigma_i^2 - \sigma_{i-1}^2}\epsilon  \tag{17}
$$




**核心逻辑总结**  

公式(16)是**正向VE SDE（公式15）通过反向SDE理论推导得到的**，关键步骤包括：  
1. 确定正向SDE的漂移（$ f=0 $）和扩散（$ g(t)=\sqrt{\mathrm{d}\sigma^2/\mathrm{d}t} $）；  
2. 应用反向SDE的一般公式（包含分数函数 $ \mathbf{s}(\mathbf{x}, t) $ 和倒向噪声 $ \mathrm{d}\tilde{\mathbf{w}} $）；  
3. 代入正向SDE的参数，化简得到反向SDE的具体形式。 
这一推导的本质是：通过反向SDE将“加噪过程”（正向SDE）转化为“去噪过程”（反向SDE），其中分数函数 $ \mathbf{s}(\mathbf{x}, t) $ 指导噪声的去除，而扩散项保持与正向SDE一致（确保过程的连续性）。





## 离散化推导

从公式(17)到最终形式的推导

公式(17)的离散化形式

$$x(t+\Delta t) - x(t) = -\alpha(t)s(x,t)\Delta t - \sqrt{\alpha(t)}\Delta t\epsilon$$

其中 $\alpha(t) = \frac{d[\sigma^2(t)]}{dt}$

步骤1：离散化时间参数

令 $t = \frac{i}{N}$，$\Delta t = \frac{1}{N}$，则：
- $x(t) = x_i$
- $x(t+\Delta t) = x_{i+1}$

步骤2：离散化 $\alpha(t)$

使用前向差分近似：
$$\alpha\left(\frac{i}{N}\right) \approx \frac{\sigma_{i+1}^2 - \sigma_i^2}{\Delta t} = N(\sigma_{i+1}^2 - \sigma_i^2)$$

步骤3：代入公式(17)

$$x_{i+1} - x_i = -N(\sigma_{i+1}^2 - \sigma_i^2)s(x_i)\Delta t - \sqrt{N(\sigma_{i+1}^2 - \sigma_i^2)}\Delta t\epsilon$$

由于 $\Delta t = \frac{1}{N}$，化简得：
$$x_{i+1} - x_i = -(\sigma_{i+1}^2 - \sigma_i^2)s(x_i) - \sqrt{\frac{\sigma_{i+1}^2 - \sigma_i^2}{N}}\epsilon$$

步骤4：重新索引

令 $j = i+1$，则 $i = j-1$：
$$x_j - x_{j-1} = -(\sigma_j^2 - \sigma_{j-1}^2)s(x_{j-1}) - \sqrt{\frac{\sigma_j^2 - \sigma_{j-1}^2}{N}}\epsilon$$

步骤5：整理得到最终形式

$$x_{j-1} = x_j + (\sigma_j^2 - \sigma_{j-1}^2)s(x_j) + \sqrt{\frac{\sigma_j^2 - \sigma_{j-1}^2}{N}}\epsilon$$

将 $j$ 换回 $i$：
$$x_{i-1} = x_i + (\sigma_i^2 - \sigma_{i-1}^2)s(x_i) + \sqrt{\frac{\sigma_i^2 - \sigma_{i-1}^2}{N}}\epsilon$$

SMLD标准形式

在SMLD算法中，通常省略 $\frac{1}{\sqrt{N}}$ 因子，得到：
$$x_{i-1} = x_i + (\sigma_i^2 - \sigma_{i-1}^2)s(x_i) + \sqrt{\sigma_i^2 - \sigma_{i-1}^2}\epsilon  \tag{17}$$



## 备注二（从DDPM推导出连续时间VP SDE）

DDPM[41]学习使用逆分布的函数形式来反转每个噪声扰动步长。

**1. DDPM的离散前向马尔可夫链**  

DDPM的离散前向过程是**带噪声扰动核的马尔可夫链**，定义为： 
$$
\mathbf{x}_i = \sqrt{1 - \beta_i} \, \mathbf{x}_{i-1} + \sqrt{\beta_i} \, \boldsymbol{\epsilon}, \quad \boldsymbol{\epsilon} \sim \mathcal{N}(\mathbf{0}, \mathbf{I}) \tag{18} 
$$

其中 $\{\beta_i\}_{i=1}^N$ 是离散的噪声调度参数，控制每一步的噪声强度。  

**2. 从离散到连续的“连续化”条件**  

为将离散过程推广到连续时间，定义：  
- 时间步长 $\Delta t = 1/N$（$N$ 为总步数，$N \to \infty$ 时 $\Delta t \to 0$）；  
- 辅助噪声调度 $\{\bar{\beta}_i\}_{i=1}^N$，满足 $\beta_i = \bar{\beta}_i \Delta t = \beta(t + \Delta t) \Delta t$。 
当 $N \to \infty$ 时，$\bar{\beta}_i \to \beta(t)$ 成为**连续时间函数**（$t \in [0,1]$）。  

**3. 离散差分到连续微分的近似（泰勒展开）**  

令 $\mathbf{x}_i = \mathbf{x}(t + \Delta t)$（即第 $i$ 步的状态对应连续时间 $t + \Delta t$ 的状态），对 $\sqrt{1 - \beta(t) \Delta t}$ 做**一阶泰勒展开**： 
$$
\sqrt{1 - \beta(t) \Delta t} \approx 1 - \frac{1}{2} \beta(t) \Delta t
$$
将离散前向过程（式18）改写为“差分形式”： 
$$
 \mathbf{x}(t + \Delta t) - \mathbf{x}(t) = \sqrt{1 - \beta(t) \Delta t} \, \mathbf{x}(t) + \sqrt{\beta(t) \Delta t} \, \boldsymbol{\epsilon} 
$$
代入泰勒展开近似，化简得： 
$$
 \mathbf{x}(t + \Delta t) - \mathbf{x}(t) = -\frac{1}{2} \beta(t) \, \mathbf{x}(t) \, \Delta t + \sqrt{\beta(t) \Delta t} \, \boldsymbol{\epsilon} \tag{19} 
$$


**4. 连续时间VP前向SDE（$\boldsymbol{\Delta t \to 0}$ 的极限）**  

当 $\Delta t \to 0$ 时，差分 $\mathbf{x}(t + \Delta t) - \mathbf{x}(t)$ 趋近于微分 $d\mathbf{x}$，且 $\sqrt{\beta(t) \Delta t} \, \boldsymbol{\epsilon}$ 趋近于随机微分项 $\sqrt{\beta(t)} \, d\mathbf{w}$（$d\mathbf{w}$ 是标准维纳过程的微分）。因此得到**VP前向SDE**： 
$$
 d\mathbf{x} = \underbrace{-\frac{1}{2} \beta(t) \, \mathbf{x} \, dt}_{f(\mathbf{x}, t)} + \underbrace{\sqrt{\beta(t)} \, d\mathbf{w}}_{g(t)} \tag{20} 
$$
其中 $f(\mathbf{x}, t) = -\frac{1}{2} \beta(t) \, \mathbf{x}$ 是“漂移项”，$g(t) = \sqrt{\beta(t)}$ 是“扩散项”。  

**5. 反向时间SDE的映射**  

连续时间SDE的反向过程（从噪声恢复数据）可通过**反向时间SDE**描述。将前向SDE（式20）(上面的漂移项和扩散项),映射到反向时间SDE（参考式13形式），得到： 

$d\mathbf{x} = \left[ f(\mathbf{x}, t) - g^2(t) \, \nabla_{\mathbf{x}} \log p_t(\mathbf{x}) \right] dt + g(t)\, d\bar{\mathbf{w}}
\tag{13}$
$$
d\mathbf{x} = -\beta(t) \left[ \frac{1}{2} \mathbf{x} + \mathbf{s}(\mathbf{x}, t) \right] dt + \sqrt{\beta(t)} \, d\bar{\mathbf{w}} \tag{21}
$$
其中 $\mathbf{s}(\mathbf{x}, t)$ 是反向过程的“得分函数”（score function），$d\bar{\mathbf{w}}$ 是反向时间的维纳过程微分。  

**6. 一致性验证（离散反向过程与DDPM的对应）**  

为验证推导的连续SDE与DDPM的离散反向过程一致，对反向SDE做**离散化**：  
- 令 $t - \Delta t$ 对应 $x_{i-1}$，$t$ 对应 $x_i$，且 $\beta(t) \Delta t = \beta_i \ll 1$；  
- 对式21离散化后，通过近似（如 $\frac{1}{\sqrt{1 - \beta_i}} \approx 1 + \frac{\beta_i}{2}$）化简，最终得到： 
  $$ \mathbf{x}_{i-1} \approx \frac{1}{\sqrt{1 - \beta_i}} \left[ \mathbf{x}_i + \frac{\beta_i}{2} \, \mathbf{s}(\mathbf{x}_i) \right] + \sqrt{\beta_i} \, \boldsymbol{\epsilon} $$ 
- 这与表三中列出的DDPM反向递归（祖先采样）相同，说明连续SDE的推导与DDPM的离散过程在极限下是等价的。与VE SDE不同，线性收缩项 $-\frac{1}{2}\beta(t)\mathbf{x}$ 抵消了噪声注入，因此当初始分布具有单位方差且 $t \to \infty$ 时，边际方差保持在1，因此命名为VP（**Variational Predictive**变分预测）。

关键结论  

通过“离散前向过程→连续化条件→泰勒展开近似→微分极限→反向SDE映射→一致性验证”的步骤，成功从DDPM的离散噪声扰动过程推导出连续时间的VP SDE，且反向SDE与DDPM的离散反向过程一致。这种推导为理解扩散模型的连续时间形式提供了数学基础。





## 备注3：关于扩散模型采样与数值求解关系。

核心观点：采样即数值求解

文本的核心观点是：**扩散模型的采样过程本质上就是求解微分方程的数值解**。无论是随机反向SDE（式13）还是确定性PF ODE（式23），每个采样器都相当于一个数值求解器，不同的采样器对应着不同的离散化方案，这些方案在精度和效率上各有取舍。

为什么早期模型需要上千步？

以DDPM为例，其祖先采样实现的是一阶Euler离散化。这种方法的精度较低，因此需要非常小的步长才能控制截断误差。这就是为什么早期模型需要上千步才能生成可接受的结果。

快速采样方法的进步

后续的快速采样方法（如DDIM和DPM-Solver）可以理解为将数值分析中的成熟技术（如高阶求解器和自适应步长）引入扩散模型框架。通过提高每一步的精度，这些方法可以在更少的函数评估次数下达到可比的生成质量，在步数和近似误差之间取得了更好的平衡。

采样步数减少引入的三类误差

从"采样器即求解器"的视角看，减少采样步数会引入三类 jointly govern（共同控制）采样输出质量的误差：
1. **离散化误差**：由有限步长引起，步长越大，误差越大，只能近似连续轨迹
2. **分数估计误差**：神经网络对真实分数的近似不完美，步数越少，自我修正的机会越少，误差放大
3. **随机误差**：来自SDE每步注入的随机噪声，其方差与步长成正比（g(t)√Δt），大步长SDE采样更不稳定

ODE求解器的优势

ODE公式通过消除随机性完全消除了第三类误差，这正是ODE-based采样器能够容忍更大步长并实现有效加速的原因。所有加速技术的目标都是在计算预算减少的情况下控制这三类误差源。

SDE采样中随机性的作用

关于SDE采样中随机性的作用，一个常见的误解是认为每步注入的噪声是样本多样性的主要来源。实际上，多样性的根本来源是随机初始样本x(T) ~ p_T：不同的起点会沿着不同轨迹前进，无论求解器是随机的还是确定性的。SDE采样中的每步噪声实际上起到校正作用，帮助轨迹探索附近的概率质量并补偿累积的分数估计误差。

总结

这段文本将扩散模型采样重新定义为数值求解问题，解释了为什么早期模型需要大量步数，以及后续快速采样方法如何借鉴数值分析技术。它还系统分析了减少采样步数引入的三类误差，并澄清了SDE采样中随机性的真正作用——不是多样性来源，而是校正机制。

## 采样

简单来说，**采样就是利用训练好的扩散模型，从随机噪声中生成符合数据分布的新数据的过程**。这个过程通常通过迭代求解一个反向扩散过程（可以是随机SDE或确定性ODE）来实现。
