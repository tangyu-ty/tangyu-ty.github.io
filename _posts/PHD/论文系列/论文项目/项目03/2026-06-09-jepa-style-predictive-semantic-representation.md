---
layout: post
title: JEPA-style predictive semantic representation
date: 2026-06-09 16:25 +0800
description: 从零开始，在 8 周内完成一个可复现实验：`CIFAR-10 task-oriented semantic communication under noisy channels`，并把它扩展成 `JEPA-style predictive semantic representation` 的研究雏形。
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



# JEPA + 世界模型 + 语义通信入门计划

日期：2026-06-09

目标：从零开始，在 8 周内完成一个可复现实验：`CIFAR-10 task-oriented semantic communication under noisy channels`，并把它扩展成 `JEPA-style predictive semantic representation` 的研究雏形。

## 总体路线

前两个月只做三件事：

1. 学会语义通信的最小实验范式：encoder -> channel -> receiver/task head。
2. 学会世界模型和 JEPA 的核心直觉：预测 latent，而不是复原像素。
3. 做出第一个可跑 baseline：在不同 SNR 下比较任务准确率、压缩率和鲁棒性。

推荐第一个研究问题：

`Can JEPA-style predictive representations improve task-oriented semantic communication under noisy channels?`

中文表述：

`JEPA 式预测表征能否提升噪声信道下任务导向语义通信的鲁棒性和传输效率？`

## 8 周计划

### 第 1 周：通信和语义通信基本概念

需要掌握：

- Shannon communication model。
- source coding、channel coding、joint source-channel coding。
- AWGN channel、Rayleigh fading、SNR。
- bit-level fidelity vs task-level effectiveness。
- PSNR/SSIM 与 accuracy/mAP/mIoU 的区别。

精读论文：

1. `A Mathematical Theory of Communication`，Shannon, 1948。只读通信模型和噪声信道直觉。
2. `Deep Joint Source-Channel Coding for Wireless Image Transmission`。
3. `Deep Learning Enabled Semantic Communication Systems`。
4. `A Contemporary Survey on Semantic Communications: Theory of Mind, Generative AI, and Deep Joint Source-Channel Coding`。

本周产出：

- 用自己的话写 1 页笔记：什么是语义通信，为什么它和传统压缩不同。
- 跑通或读懂 `semcom_jepa_starter` 的 AWGN channel。


### 第 2 周：Task-Oriented Semantic Communication

需要掌握：

- semantic encoder/decoder。
- task-oriented loss。
- information bottleneck。
- semantic noise。
- rate-accuracy-SNR 曲线。

精读论文：

1. `U-DeepSC: A Universal Deep Semantic Communication System`。
2. `Task-Oriented Multi-User Semantic Communications`。
3. `Task-oriented Explainable Semantic Communications`。
4. `Robust Semantic Communications Against Semantic Noise`。

本周产出：

- 跑通 CIFAR-10 baseline。
- 画出不同 SNR 下的 accuracy 曲线。
- 记录 latent dimension 对 accuracy 的影响。

### 第 3 周：世界模型基础

需要掌握：

- latent dynamics。
- imagination rollout。
- model-based RL。
- observation model、reward model、transition model。
- prediction error 和 uncertainty。

精读论文：

1. `Recurrent World Models Facilitate Policy Evolution`。
2. `Learning Latent Dynamics for Planning from Pixels (PlaNet)`。
3. `Dream to Control: Learning Behaviors by Latent Imagination (Dreamer)`。
4. `DreamerV3: Mastering Diverse Control Tasks through World Models`。

本周产出：

- 画一张图解释 PlaNet/Dreamer 的 latent world model。
- 思考：如果 receiver 已经有 world model，哪些内容不需要传？

### 第 4 周：JEPA 和预测式表征

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

### 第 5 周：实现第一个 SemCom baseline

使用目录：

`semcom_jepa_starter`

任务：

- CIFAR-10 image -> semantic encoder -> latent。
- latent 经过 AWGN channel。
- receiver classifier 直接完成分类。
- 不做图像重建，先做 task-oriented SemCom。

实验：

- latent_dim = 64, 128, 256。
- SNR = -5, 0, 5, 10, 15, 20 dB。
- 记录 accuracy。

本周产出：

- 一张 `accuracy vs SNR` 曲线。
- 一张 `accuracy vs latent_dim` 曲线。
- 一个能复现的训练命令和结果表。

### 第 6 周：加入 representation baseline

比较不同 encoder：

- supervised CNN encoder。
- autoencoder encoder。
- DINO/CLIP/MAE feature。
- I-JEPA feature。

如果算力有限，先 frozen pretrained encoder，只训练 channel adapter 和 classifier。

本周产出：

- 不同 representation 在噪声信道下的鲁棒性表格。
- 初步回答：predictive/self-supervised feature 是否更适合 SemCom？

### 第 7 周：引入预测和 residual transmission

从图像升级到视频或序列：

- Moving MNIST。
- UCF101 subset。
- Something-Something subset。

基本思想：

1. receiver 用历史 latent 预测未来 latent。
2. sender 只传预测残差或高不确定性 token。
3. receiver 修正 latent。
4. 下游做 action recognition 或 future classification。

本周产出：

- 一个 toy predictive residual experiment。
- 对比 always transmit vs residual transmit。

### 第 8 周：形成论文雏形

整理成 4 页 idea note：

- Problem。
- Motivation。
- Method。
- Experiments。
- Expected contribution。

推荐题目：

`Predictive Latent Semantic Communication with JEPA-style World Models`

基于类JEPA世界模型的预测性潜在语义通信

可以投稿的早期目标：

- IEEE workshop。
- NeurIPS/ICLR/CVPR workshop。
- ICC/Globecom workshop。

## 重点论文清单

### 必读 15 篇

1. Shannon, `A Mathematical Theory of Communication`, 1948。
2. Bourtsoulatze et al., `Deep Joint Source-Channel Coding for Wireless Image Transmission`。
3. Xie et al., `Deep Learning Enabled Semantic Communication Systems`。
4. Zhang et al., `U-DeepSC: A Universal Deep Semantic Communication System`。
5. `Task-Oriented Multi-User Semantic Communications`。
6. `A Contemporary Survey on Semantic Communications`。
7. Ha and Schmidhuber, `Recurrent World Models Facilitate Policy Evolution`。
8. Hafner et al., `Learning Latent Dynamics for Planning from Pixels (PlaNet)`。
9. Hafner et al., `Dream to Control: Learning Behaviors by Latent Imagination`。
10. Hafner et al., `DreamerV3`。
11. Assran et al., `Self-Supervised Learning from Images with a Joint-Embedding Predictive Architecture`。
12. Bardes et al., `Revisiting Feature Prediction for Learning Visual Representations from Video`。
13. Meta, `V-JEPA 2: Self-Supervised Video Models Enable Understanding, Prediction and Planning`。
14. `Semantic Communications with World Models`。
15. `HANSOME: World-Model based Hierarchical Planning with Semantic Communications for Autonomous Driving`。

### 第二批扩展 20 篇

1. `DeepJSCC-f: Deep Joint Source-Channel Coding of Images with Feedback`。
2. `WITT: A Wireless Image Transmission Transformer for Semantic Communications`。
3. `Predictive and Adaptive Deep Coding for Wireless Image Transmission in Semantic Communication`。
4. `Robust Semantic Communications Against Semantic Noise`。
5. `Knowledge Base Enabled Semantic Communication: A Generative Perspective`。
6. `Generative AI-Driven Semantic Communication Networks`。
7. `CDM-JSCC: Conditional Diffusion Model based JSCC for Image Transmission`。
8. `DiffSC: Diffusion-based Semantic Communication`。
9. `LDM-SemCom: Latent Diffusion Model based Semantic Communication for Edge Computing`。
10. `SComCP: Task-Oriented Semantic Communication for Collaborative Perception`。
11. `CALL: Ego-centric Learning of Communicative World Models for Autonomous Driving`。
12. `DayDreamer: World Models for Physical Robot Learning`。
13. `Genie: Generative Interactive Environments`。
14. `iVideoGPT: Interactive VideoGPTs are Scalable World Models`。
15. `Drive-WM: Driving into the Future with World Model`。
16. `Vista: A Generalizable Driving World Model`。
17. `OccWorld: Learning a 3D Occupancy World Model for Autonomous Driving`。
18. `Point-JEPA`。
19. `A-JEPA: Joint-Embedding Predictive Architecture Can Listen`。
20. `Causal-JEPA: Learning World Models through Object-Level Latent Interventions`。

完整目录见：

`paper_catalog_world_model_jepa_semcom.md`

## 代码学习顺序

第一优先级：

1. `semcom_jepa_starter`：你自己的最小 SemCom baseline。
2. `facebookresearch/ijepa`：理解 JEPA 训练目标。
3. `facebookresearch/vjepa2`：理解视频 world model。
4. `zhang-guangyi/t-udeepsc`：理解 task-oriented semantic communication。
5. `danijar/dreamerv3`：理解 latent world model。

## 每篇论文怎么读

第一遍只回答 5 个问题：

1. 它解决什么问题？
2. 输入、输出是什么？
3. 中间 latent 表示是什么？
4. loss 怎么设计？
5. 评价指标是什么？

第二遍再看：

- 训练细节。
- baseline 是否公平。
- 数据集是否适合你复现。
- 能否迁移到 noisy channel。

第三遍才看公式细节。

## 你的第一篇论文的实验矩阵

| Factor         | Values                             |
| -------------- | ---------------------------------- |
| Dataset        | CIFAR-10, CIFAR-100, UCF101 subset |
| Channel        | AWGN, Rayleigh, packet loss        |
| SNR            | -5, 0, 5, 10, 15, 20 dB            |
| Representation | CNN, AE, DINO, CLIP, I-JEPA        |
| Latent dim     | 64, 128, 256, 512                  |
| Metric         | accuracy, rate, latency, FLOPs     |

先完成 CIFAR-10 + AWGN + CNN semantic encoder。不要一开始把所有因素都打开。

