---
layout: post
title: 'Semantic communications for future internet: fundamentals, applications, and challenges'
date: 2025-09-20 11:05 +0800
description: 语义通信综述
pin: true
math: true
mermaid: true
category: [语义通信,综述]
tags: [zotero,语义通信综述]
published: true
sitemap: false
author: tangyu
---


## (2023) Semantic communications for future internet: fundamentals, applications, and challenges（）

<table><tbody><tr><td><p><strong>期刊: <span style="color: rgb(255, 0, 0)">IEEE Communications Surveys and Tutorials</span></strong>（发表日期: <strong>2023</strong>）<br><strong>作者:</strong> Wanting Yang; Hongyang Du; Zi Qin Liew; Wei Yang Bryan Lim; Zehui Xiong; Dusit Niyato; Xuefen Chi; Xuemin Shen; Chunyan Miao</p></td></tr><tr><td><p><strong>摘要翻译: </strong><em>随着对智能服务需求的增加，第六代（6G）无线网络将从只关注高传输速率的传统架构转变为基于万物智能连接的新架构。语义通信（SemCom）是一种革命性的架构，它将用户和应用程序的需求以及信息的意义整合到数据处理和传输中，预计将成为6G的新核心范式。虽然预计SemCom将超越经典的香农范式，但在实现支持SemCom的智能互联网的道路上需要克服几个障碍。在本文中，我们首先强调了SemCom在6G中的动机和令人信服的原因。然后，我们概述了SemCom相关理论的发展。之后，我们介绍了三种类型的SemCom，即语义导向沟通、目标导向沟通和语义感知沟通。接下来，我们将通信系统的设计分为三个维度，即语义信息（SI）提取、SI传输和SI度量。对于每个维度，我们回顾了现有的技术，讨论了它们的优点和局限性，以及剩下的挑战。然后，我们介绍了SemCom在6G中的潜在应用，并描绘了未来SemCom授权网络架构的愿景。最后，我们概述了未来的研究机会。简而言之，本文对SemCom的基本原理、其在6G网络中的应用以及现有的挑战和悬而未决的问题进行了全面回顾，并为进一步深入研究提供了见解。</em></p></td></tr><tr><td><p><strong>期刊分区: </strong><span style="color: rgb(204, 31, 0)"><span style="background-color: rgb(255, 226, 221)">ㅤㅤ ㅤㅤIF 46.7 ㅤㅤ ㅤㅤ</span></span> ㅤㅤ ㅤㅤ<span style="color: rgb(232,50,19)"><span style="background-color: rgb(255, 226, 221)"> ㅤㅤ ㅤㅤIF(5) 42.8 ㅤㅤ ㅤㅤ</span></span> ㅤㅤ ㅤㅤ<span style="color: rgb(204, 31, 0)"><span style="background-color: rgb(255, 226, 221)"> ㅤㅤ ㅤㅤJCR Q1 ㅤㅤ ㅤㅤ</span></span> ㅤㅤ ㅤㅤ<span style="color: rgb(204, 31, 0)"><span style="background-color: rgb(255, 226, 221)"> ㅤㅤ ㅤㅤSCI基础版 工程技术1区 ㅤㅤ ㅤㅤ</span></span> ㅤㅤ ㅤㅤ<span style="color: rgb(204, 31, 0)"><span style="background-color: rgb(255, 226, 221)"> ㅤㅤ ㅤㅤSCI升级版 计算机科学1区 ㅤㅤ ㅤㅤ</span></span> ㅤㅤ ㅤㅤ<span style="color: rgb(204, 31, 0)"><span style="background-color: rgb(255, 226, 221)"> ㅤㅤ ㅤㅤsciUpTop 计算机科学TOP ㅤㅤ ㅤㅤ</span></span> ㅤㅤ ㅤㅤ<span style="color: rgb(111, 69, 135)"><span style="background-color: rgb(232, 222, 238)"> ㅤㅤ ㅤㅤEI ㅤㅤ ㅤㅤ</span></span></p></td></tr><tr><td><p><strong>Local Link: </strong><a href="zotero://open-pdf/0_QGQ8KE4B" rel="noopener noreferrer nofollow">Yang 等 - 2023 - Semantic Communications for Future Internet Fundamentals, Applications, and Challenges.pdf</a></p></td></tr></tbody></table>

### 💡创新点

> 然而，尽管6G和SemCom中相辅相成的融合特性引起了学术界的关注，但目前还没有一份全面的调查报告能够全面概述支持SemCom的6G和Beyond网络的发展、挑战和未来趋势。

### 📚前言及文献综述

> `“II.B SemCom System Design”章节根据语义通信的层次和作用，将语义通信（SemCom）系统设计分为三种主要类型：语义导向通信、目标导向通信和语义感知通信 。与传统通信系统不同，SemCom旨在将语义层面和有效性层面整合到系统设计中。`
>
> 具体分类如下：
>
> - **语义导向通信 (Semantic-Oriented Communications)**：
    >
    >   - 关注源数据语义内容的准确性，而非传统通信中源数据可能发出的平均信息量 。
>   - 在编码前引入语义表示模块，负责捕获源数据中的核心信息并过滤掉不必要的冗余信息 。
>   - 语义表示和语义编码功能通常整合为一个模块，共同发挥类似传统通信中源编码的作用 。
>   - 语义推理和语义解码的组合作用等同于源解码 。
>   - 目标是使接收方成功推断语义信息（SI），因此将联合语义编码和解码过程视为语义提取（SE） 。
>   - 通信双方的本地知识需要实时共享，以确保理解和推理过程的匹配，否则会产生语义噪声 。
>   - 系统输出是恢复的消息含义 。
> - **目标导向通信 (Goal-Oriented Communication)**：
    >
    >   - 除了语义信息（SI）外，还需要捕获语用信息（pragmatic information），即与特定通信目标相关的SI的一部分 。
>   - 通信任务的目标在语义提取（SE）中扮演重要角色，这是其与语义导向通信的主要区别 。
>   - 通信目标也必须包含在通信双方的本地知识中，以进一步过滤掉每次传输中不相关的SI，尤其当通信目标频繁变化时 。
>   - 系统输出是直接执行的动作指令，而非仅仅是恢复的消息含义 。
>   - 关注有效性层面，旨在在有限网络资源下以期望的方式完成任务，而非语义导向通信中关注的SI准确性 。
>   - 同样需要保持通信双方本地知识和通信目标的一致性，以避免语义噪声导致任务失败 。
> - **语义感知通信 (Semantic-Aware Communication)**：
    >
    >   - 主要应用于任务导向通信，例如自动驾驶和无人机群，不局限于两个特定代理之间的连接。
>   - 通过主动或被动方式在不同终端之间建立多重显式或隐式连接，以增强代理之间的知识。
>   - 语义信息（SI）是通过分析代理行为和执行任务时的当前环境获得的，而不是从数据源中提取。
>   - 目前还没有通用的系统模型，因为它可能没有明确的收发器或完整的成对语义编码和解码过程。

### 🧩数据

### 🔬方法

> 作者解决问题的方法/算法是什么？是否基于前人的方法？基于了哪些？

### 📜结论

### 🤔思考

> Tips: 本文有什么优缺点？你是否对某些内容产生了疑问？  
> 作者给出了哪些结论？哪些是strong conclusions, 哪些又是weak的conclusions（即作者并没有通过实验提供evidence，只在discussion中提到；或实验的数据并没有给出充分的evidence）?  
> 你是否认为某些研究方式可以改进，如何改进？
