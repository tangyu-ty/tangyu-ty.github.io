---
layout: post
title: Stable diffusion LDM
date: 2026-05-21 11:03 +0800
description: demo描述
pin: true
math: true
mermaid: true
category:
- 技术编程
tags:
- LLM
- 扩散模型
- SD
- LDM
published: true
sitemap: false
author: tangyu
---



【Stable diffusion生成大模型——隐扩散模型原理解析（LDM）】 https://www.bilibili.com/video/BV1RD421u7f4/?share_source=copy_web&vd_source=acbb0e4431491792f1e7a2f3956c634a

# 传统扩散模型存在的问题

传统扩散模型中，一上来对图像进行加噪去噪，这种做法一偶一些难以避免的问题--效率。
一方面，如果图像的像素/分辨率很大，计算量就难以想象。
另一方面，VAE在图像始终存在一些冗余部分。

## 隐空间扩散

将图像便阿门成一个维度较小的编码向量/特征图。然后再训练扩散模型。  范德萨
如何进行编码呢？就用VAE进行编码。
VQGAN，离散化的编码器。



# 一、Stable Diffusion 核心概念梳理

Stable Diffusion 和 DDPM 的关系：

| 模型             | 数据空间    | 条件方式                   | 网络                   | 核心特点                                           |
| ---------------- | ----------- | -------------------------- | ---------------------- | -------------------------------------------------- |
| DDPM             | 像素空间    | 无条件                     | UNet                   | 学习噪声预测，前向扩散+反向采样                    |
| Stable Diffusion | latent 空间 | 文本条件（CLIP embedding） | UNet + cross-attention | 先用 VAE 压缩图像到 latent，再扩散；用文本引导去噪 |

核心新元素：

1. VAE（Variational AutoEncoder）

   - 图像压缩到 latent 空间
   - 减少扩散计算量
   - Encoder: $x \rightarrow z$
   - Decoder: $z \rightarrow x$

2. 文本条件（Text Encoder）

   - 使用 CLIP 或类似模型
   - prompt → embedding
   - embedding 注入 UNet（cross-attention）

3. Cross-Attention

   - UNet 每一层可以看到文本条件
   - 控制图像生成内容

4. Classifier-Free Guidance (CFG)

   - 控制生成的多样性 vs 文本匹配程度

   - 核心公式：
     $$
     \epsilon_\theta^{\text{cfg}} = \epsilon_\theta(x_t, c) + w (\epsilon_\theta(x_t, c) - \epsilon_\theta(x_t, \varnothing))
     $$

     - $c$ 为文本条件
     - $\varnothing$ 表示无条件
     - $w$ 为 guidance scale



# 二、代码实践
