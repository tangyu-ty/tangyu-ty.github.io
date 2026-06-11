---
layout: post
title: JEPA SemCom 第5周及以后合并笔记
date: 2026-06-11 16:25 +0800
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

# JEPA SemCom 第5周及以后合并笔记

> 本文件由两份 Markdown 合并拆分而成，保留原始 8 周计划的论文清单与详细笔记内容。

# 第 5 周：实现第一个 SemCom baseline
使用目录：

`semcom_jepa_starter`

✅❌❓

任务：

- CIFAR-10 image -> semantic encoder -> latent。✅
- latent 经过 AWGN channel。✅
- receiver classifier 直接完成分类。✅
- 不做图像重建，先做 task-oriented SemCom。✅

实验：

- latent_dim = 64, 128, 256。✅
- SNR = -5, 0, 5, 10, 15, 20 dB。✅
- 记录 accuracy。

本周产出：

- 一张 `accuracy vs SNR` 曲线。
- 一张 `accuracy vs latent_dim` 曲线。
- 一个能复现的训练命令和结果表。

# 第 6 周：加入 representation baseline
比较不同 encoder：

- supervised CNN encoder。
- autoencoder encoder。
- DINO/CLIP/MAE feature。
- I-JEPA feature。

如果算力有限，先 frozen pretrained encoder，只训练 channel adapter 和 classifier。

本周产出：

- 不同 representation 在噪声信道下的鲁棒性表格。
- 初步回答：predictive/self-supervised feature 是否更适合 SemCom？

# 第 7 周：引入预测和 residual transmission
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

# 第 8 周：形成论文雏形
整理成 4 页 idea note：

- Problem。
- Motivation。
- Method。
- Experiments。
- Expected contribution。

推荐题目：

`Predictive Latent Semantic Communication with JEPA-style World Models`

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

## 最后梳理：这份计划的主线

这份合并文档可以按“三层递进”理解：

1. **第 1-2 周：先建立语义通信最小闭环。** 重点不是追求图像重建质量，而是理解 `encoder -> channel -> receiver/task head` 这条任务导向链路，并用 CIFAR-10 + AWGN 跑出 `accuracy-SNR-latent_dim` 的基础曲线。
2. **第 3-4 周：引入世界模型与 JEPA 式预测表征。** 世界模型提供“接收端可预测什么”的先验，JEPA 提供“预测 latent / feature 而不是重建 pixel”的表征学习思路。这两者共同支撑后续的 predictive semantic communication。
3. **第 5 周及以后：从 baseline 走向论文雏形。** 先做 CIFAR-10 + AWGN + CNN semantic encoder 的可复现实验，再逐步加入 DINO/CLIP/MAE/I-JEPA 等 representation baseline，最后扩展到视频序列中的 residual transmission 与 uncertainty-aware transmission。

因此，核心研究问题可以凝练为：

> **接收端如果具备 JEPA-style / world-model-style 的预测能力，发送端是否只需要传输任务相关 latent、预测残差、新事件和不确定性信息，而不必重复传完整观测？**

最小可行实验路线建议保持为：

`CIFAR-10 + AWGN + semantic encoder + latent_dim/SNR sweep` → `representation baseline 对比` → `视频/序列 residual transmission` → `4 页 idea note / workshop paper`。

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

## 最后梳理：这份计划的主线

这份合并文档可以按“三层递进”理解：

1. **第 1-2 周：先建立语义通信最小闭环。** 重点不是追求图像重建质量，而是理解 `encoder -> channel -> receiver/task head` 这条任务导向链路，并用 CIFAR-10 + AWGN 跑出 `accuracy-SNR-latent_dim` 的基础曲线。
2. **第 3-4 周：引入世界模型与 JEPA 式预测表征。** 世界模型提供“接收端可预测什么”的先验，JEPA 提供“预测 latent / feature 而不是重建 pixel”的表征学习思路。这两者共同支撑后续的 predictive semantic communication。
3. **第 5 周及以后：从 baseline 走向论文雏形。** 先做 CIFAR-10 + AWGN + CNN semantic encoder 的可复现实验，再逐步加入 DINO/CLIP/MAE/I-JEPA 等 representation baseline，最后扩展到视频序列中的 residual transmission 与 uncertainty-aware transmission。

因此，核心研究问题可以凝练为：

> **接收端如果具备 JEPA-style / world-model-style 的预测能力，发送端是否只需要传输任务相关 latent、预测残差、新事件和不确定性信息，而不必重复传完整观测？**

最小可行实验路线建议保持为：

`CIFAR-10 + AWGN + semantic encoder + latent_dim/SNR sweep` → `representation baseline 对比` → `视频/序列 residual transmission` → `4 页 idea note / workshop paper`。
