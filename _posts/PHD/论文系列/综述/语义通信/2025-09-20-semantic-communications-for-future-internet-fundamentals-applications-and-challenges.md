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



* * *

## (2023) Semantic communications for future internet: fundamentals, applications, and challenges（）

<table><tbody><tr><td><p><strong>期刊: <span style="color: rgb(255, 0, 0)">IEEE Communications Surveys and Tutorials</span></strong>（发表日期: <strong>2023</strong>）<br><strong>作者:</strong> Wanting Yang; Hongyang Du; Zi Qin Liew; Wei Yang Bryan Lim; Zehui Xiong; Dusit Niyato; Xuefen Chi; Xuemin Shen; Chunyan Miao</p></td></tr><tr><td><p><strong>期刊分区: </strong><span style="color: rgb(204, 31, 0)"><span style="background-color: rgb(255, 226, 221)">ㅤㅤ ㅤㅤIF 46.7 ㅤㅤ ㅤㅤ</span></span> ㅤㅤ ㅤㅤ<span style="color: rgb(204, 31, 0)"><span style="background-color: rgb(255, 226, 221)"> ㅤㅤ ㅤㅤIF(5) 42.8 ㅤㅤ ㅤㅤ</span></span> ㅤㅤ ㅤㅤ<span style="color: rgb(204, 31, 0)"><span style="background-color: rgb(255, 226, 221)"> ㅤㅤ ㅤㅤJCR Q1 ㅤㅤ ㅤㅤ</span></span> ㅤㅤ ㅤㅤ<span style="color: rgb(204, 31, 0)"><span style="background-color: rgb(255, 226, 221)"> ㅤㅤ ㅤㅤSCI基础版 工程技术1区 ㅤㅤ ㅤㅤ</span></span> ㅤㅤ ㅤㅤ<span style="color: rgb(204, 31, 0)"><span style="background-color: rgb(255, 226, 221)"> ㅤㅤ ㅤㅤSCI升级版 计算机科学1区 ㅤㅤ ㅤㅤ</span></span> ㅤㅤ ㅤㅤ<span style="color: rgb(204, 31, 0)"><span style="background-color: rgb(255, 226, 221)"> ㅤㅤ ㅤㅤsciUpTop 计算机科学TOP ㅤㅤ ㅤㅤ</span></span> ㅤㅤ ㅤㅤ<span style="color: rgb(111, 69, 135)"><span style="background-color: rgb(232, 222, 238)"> ㅤㅤ ㅤㅤEI ㅤㅤ ㅤㅤ</span></span></p></td></tr><tr><td><p><strong>Local Link: </strong><a href="zotero://open-pdf/0_QGQ8KE4B" rel="noopener noreferrer nofollow">Yang 等 - 2023 - Semantic Communications for Future Internet Fundamentals, Applications, and Challenges.pdf</a></p></td></tr></tbody></table>

**摘要翻译:** *随着对智能服务需求的增加，第六代（6G）无线网络将从只关注高传输速率的传统架构转变为基于万物智能连接的新架构。语义通信（SemCom）是一种革命性的架构，它将用户和应用程序的需求以及信息的意义整合到数据处理和传输中，预计将成为6G的新核心范式。虽然预计SemCom将超越经典的香农范式，但在实现支持SemCom的智能互联网的道路上需要克服几个障碍。在本文中，我们首先强调了SemCom在6G中的动机和令人信服的原因。然后，我们概述了SemCom相关理论的发展。之后，我们介绍了三种类型的SemCom，即语义导向沟通、目标导向沟通和语义感知沟通。接下来，我们将通信系统的设计分为三个维度，即语义信息（SI）提取、SI传输和SI度量。对于每个维度，我们回顾了现有的技术，讨论了它们的优点和局限性，以及剩下的挑战。然后，我们介绍了SemCom在6G中的潜在应用，并描绘了未来SemCom授权网络架构的愿景。最后，我们概述了未来的研究机会。简而言之，本文对SemCom的基本原理、其在6G网络中的应用以及现有的挑战和悬而未决的问题进行了全面回顾，并为进一步深入研究提供了见解。*


### 💡创新点

> 然而，尽管6G和SemCom中相辅相成的融合特性引起了学术界的关注，但目前还没有一份全面的调查报告能够全面概述支持SemCom的6G和Beyond网络的发展、挑战和未来趋势。

### 📚前言及文献综述

> `“II.B SemCom System Design”章节根据语义通信的层次和作用，将语义通信（SemCom）系统设计分为三种主要类型：语义导向通信、目标导向通信和语义感知通信 。与传统通信系统不同，SemCom旨在将语义层面和有效性层面整合到系统设计中。`
>
> 具体分类如下：
>
> - **语义导向通信 (Semantic-Oriented Communications)**：
    >
    >     - 关注源数据语义内容的准确性，而非传统通信中源数据可能发出的平均信息量 。
>
>     - 在编码前引入语义表示模块，负责捕获源数据中的核心信息并过滤掉不必要的冗余信息 。
>
>     - 语义表示和语义编码功能通常整合为一个模块，共同发挥类似传统通信中源编码的作用 。
>
>     - 语义推理和语义解码的组合作用等同于源解码 。
>
>     - 目标是使接收方成功推断语义信息（SI），因此将联合语义编码和解码过程视为语义提取（SE） 。
>
>     - 通信双方的本地知识需要实时共享，以确保理解和推理过程的匹配，否则会产生语义噪声 。
>
>     - 系统输出是恢复的消息含义 。
>
> - **目标导向通信 (Goal-Oriented Communication)**：
    >
    >     - 除了语义信息（SI）外，还需要捕获语用信息（pragmatic information），即与特定通信目标相关的SI的一部分 。
>
>     - 通信任务的目标在语义提取（SE）中扮演重要角色，这是其与语义导向通信的主要区别 。
>
>     - 通信目标也必须包含在通信双方的本地知识中，以进一步过滤掉每次传输中不相关的SI，尤其当通信目标频繁变化时 。
>
>     - 系统输出是直接执行的动作指令，而非仅仅是恢复的消息含义 。
>
>     - 关注有效性层面，旨在在有限网络资源下以期望的方式完成任务，而非语义导向通信中关注的SI准确性 。
>
>     - 同样需要保持通信双方本地知识和通信目标的一致性，以避免语义噪声导致任务失败 。
>
> - **语义感知通信 (Semantic-Aware Communication)**：
    >
    >     - 主要应用于任务导向通信，例如自动驾驶和无人机群，不局限于两个特定代理之间的连接。
>
>     - 通过主动或被动方式在不同终端之间建立多重显式或隐式连接，以增强代理之间的知识。
>
>     - 语义信息（SI）是通过分析代理行为和执行任务时的当前环境获得的，而不是从数据源中提取。
>
>     - 目前还没有通用的系统模型，因为它可能没有明确的收发器或完整的成对语义编码和解码过程。
>
>
> DL-based：更令人鼓舞的是，由于在调制和传输之前缺乏量化和比特转换过程，JTRS（“a DL-constructed joint transmission-recognition scheme” ([Yang 等, 2023, p. 221](zotero://select/library/items/W9A2Y2H8)) ([pdf](zotero://open-pdf/library/items/QGQ8KE4B?page=9)) ）在模拟传输中的运行时间远低于其他方法，这意味着基于DL的SemCom在低延迟通信方面具有固有的优势。

### 🧩数据

> 作者解决问题的方法/算法是什么？是否基于前人的方法？基于了哪些？

### 🔬方法 of text Data

> “LSTM-based SE achieves the lowest word error rate for a given coding length with a high bit-drop rate.” ([Yang 等, 2023, p. 223](zotero://select/library/items/W9A2Y2H8)) ([pdf](zotero://open-pdf/library/items/QGQ8KE4B?page=11))

> “Compared with Gzip and Huffman, LSTM-based SE achieves the lowest word error rate for a given coding length with a high bit-drop rate.” ([Yang 等, 2023, p. 223](zotero://select/library/items/W9A2Y2H8)) ([pdf](zotero://open-pdf/library/items/QGQ8KE4B?page=11)) 与Gzip和Huffman相比，基于LSTM的SE在给定的编码长度下实现了最低的字错误率和高比特丢失率。
>
> “Therefore, the proposed method can only describe the probability of a certain word coming after another in a sentence, which makes it hard to deal with complex sentences.” ([Yang 等, 2023, p. 223](zotero://select/library/items/W9A2Y2H8)) ([pdf](zotero://open-pdf/library/items/QGQ8KE4B?page=11)) 因此，所提出的方法只能描述句子中某个单词连续出现的概率，这使得处理复杂句子变得困难。（LSTM的不足，引出下面的Transformer）
>
> “In fact, in a sentence processing system, some words or phrases are more likely to cause semantic ambiguity due to polysemy or noise interference.” ([Yang 等, 2023, p. 223](zotero://select/library/items/W9A2Y2H8)) ([pdf](zotero://open-pdf/library/items/QGQ8KE4B?page=11)) 事实上，在句子处理系统中，由于多义词或噪声干扰，一些单词或短语更有可能引起语义歧义。
>
> “With this in mind, the authors in [75] propose a flexible SE approach based on Universal Transformer (UT) [76], by introducing an adaptive circulation mechanism in the Transformer to break the original fixed structure.” ([Yang 等, 2023, p. 223](zotero://select/library/items/W9A2Y2H8)) ([pdf](zotero://open-pdf/library/items/QGQ8KE4B?page=11)) 考虑到这一点，作者在[75]中提出了一种基于通用变压器（UT）[76]的灵活SE方法，通过在变压器中引入自适应循环机制来打破原始的固定结构。

> - ​**​文本数据语义通信（SemCom）的初步实现​**​：
    >
    >     - 受深度学习在自然语言处理（NLP）领域（如机器翻译）成功的启发，研究人员首次将SemCom应用于文本传输。
>
>     - 他们构建了一个简单的系统模型，其中发射器通过擦除信道使用有限的比特数向接收器发送句子。
>
>     - ​**​基于LSTM的SE​**​：
        >
        >         - 首先，词语通过GloVe预训练的词嵌入向量表示，GloVe是一个用于提取语义信息（SI）的预训练查找表。
>
>         - 然后，受机器翻译中序列到序列学习框架的启发，采用了基于长短期记忆（LSTM）的编码器和解码器。其中，前一个估计词的嵌入向量作为下一步的输入，并使用束搜索算法来寻找最可能的词序列。
>
>         - 与Gzip和Huffman等传统方法相比，基于LSTM的SE在给定编码长度和高比特丢失率下实现了最低的词错误率。
>
>         - 随着句子长度的增加，基于LSTM的SE的优越性在特定比特丢失率下变得更加显著。
>
>         - ​**​局限性​**​：然而，GloVe或Word2Vec等词表示模型只能捕捉词之间的关系，无法描述句法信息，这使得它们难以处理复杂句子。
>
> - ​**​Transformer网络的引入及其优势​**​：
    >
    >     - 为了解决基于LSTM方法的局限性，Transformer架构被引入，它能有效提取整个句子的语义信息和句法信息。
>
>     - Transformer网络结合了多头注意力机制，可以并行提取输入句子的多种特征。
>
>     - 与基于循环神经网络（RNN）的架构（如LSTM）相比，Transformer网络在学习输入句子中的长距离依赖关系时，具有更低的计算复杂度和更高的并行计算能力。
>
>     - 在最近的研究中，Transformer网络取代了RNN网络，并且信道模型扩展到加性高斯白噪声（AWGN）信道和衰落信道。
>
>     - 这些研究采用BLEU和句子相似度等语义指标来衡量SemCom性能。在低信噪比（SNR）区域，Transformer在文本数据语义提取（SE）方面的优越性得到了证明。
>
> - **Universal Transformer (UT) 的引入及其益处​**​：
    >
    >     - 标准Transformer具有固定的注意力结构，这限制了其在学习过程中的适应性。
>
>     - 为了解决这一挑战，研究人员提出了基于Universal Transformer（UT）的灵活语义提取方法，通过在Transformer中引入自适应循环机制来打破原有的固定结构。
>
>     - UT集成了自适应计算时间（ACT）模型，该模型可以根据每个步骤预测的停止概率动态调整处理每个输入符号所需的计算步骤数。
>
>     - 这种动态停止机制使得基于UT的SE能够为每个输入符号（即每符号自注意力RNN）发挥其自身的循环机制，并通过不同的循环灵活响应不同的语义信息和变化的物理信道。
>
>     - 在性能比较中，基于UT的SE方法和经典Transformer-based SE方法与传统源编码和信道编码级联方案（使用固定长度编码和Turbo编码或Reed-Solomon编码）进行了对比。
>
>     - 传统方案在很宽的SNR范围内BLEU分数一直很低，只有当SNR增加到15 dB以上时才有显著改善。
>
>     - 相比之下，两种SemCom方案在各种变化的信道条件下都取得了显著更高的BLEU分数。
>
>     - 特别是，由于自适应循环机制有助于更准确地捕获语义信息，基于UT的算法在整个SNR范围内始终优于基于Transformer的算法。
>

* * *

> “Specifically, since the adaptive circulation mechanism facilitates a more accurate capture of SI, the UT-based algorithm consistently scores higher than the Transformer-based algorithm over the full SNR region.” ([Yang 等, 2023, p. 223](zotero://select/library/items/W9A2Y2H8)) ([pdf](zotero://open-pdf/library/items/QGQ8KE4B?page=11)) 具体而言，由于**自适应循环机制有助于更准确地捕获SI**，因此基于UT的算法在整个SNR区域内的得分始终高于基于Transformer的算法。
>
> 多模态数据语义提取”部分讨论了多模态数据的语义通信（SemCom）系统，特别是以视觉问答（VQA）为例。该部分重点介绍了两项关键工作：[86]中的研究和[87]中的研究。
>
> **[86]中的研究**：
>
> - **系统模型**：研究了一个包含图像发送器、文本发送器和接收器的VQA SemCom系统。
>
> - **编码器**：图像发送器采用在ImageNet上预训练的ResNet-101网络，文本发送器采用Bi-LSTM网络。
>
> - **解码器**：为了处理文本和图像语义信息（SI）的融合以及回答视觉问题，采用了记忆、注意力与组合（MAC）神经网络。MAC单元包括控制单元（通过注意力模块根据接收到的文本SI生成查询）、读取单元（通过另一个注意力模块从图像SI中搜索对应键）和写入单元（整合信息并输出预测答案）。
>
> - **性能**：与传统方法（将恢复的图像和文本输入MAC）相比，这种端到端方案实现了显著更高的答案准确性。
>
> - **局限性**：该方案假设完美的信道状态信息，并且由于缺乏注意力机制，对真实世界的信道变化不具有鲁棒性。
>
>
> **[87]中的研究**：
>
> - **统一编码结构**：作者基于Transformer统一了图像发送器和文本发送器的语义编码结构。
>
> - **新型解码器网络**：提出了一个由查询模块和信息融合模块组成的新型语义解码器网络。
    >
    >     - **查询模块**：采用分层Transformer，由Transformer编码器层和Transformer解码器层组成。与经典Transformer不同，[87]中的分层Transformer将每个编码器层的输出token作为每个解码器层的输入，以利用文本信息中更多的关键词和图像信息中对应的区域。
>
>     - **融合模块**：融合两种信息以获得答案。
>
> - **性能**：与[86]相比，该方案在完美和不完美信道状态信息下都取得了可比较的答案准确性，并且显著高于传统方法。
>
> - **局限性**：复杂的模型设计增加了训练的时间消耗和计算资源消耗，特别是对于文本编码。此外，用于训练和测试的图像尺寸较小，该模型在处理大尺寸图像VQA任务方面的优越性仍有待验证。
>

* * *

> **强化学习：**
>
> 目的：为了克服DL对损失函数可微性的严格要求，强化可以解决在其他一些领域中，用户定义的、特定于任务的、不可区分的任务度量的问题。
>
> ​**​挑战​**​：即时奖励函数形式的确定特别棘手，因为解码过程中的奖励直到句子结束才能直接测量 。
>
> ​**​解决方案​**​：
>
> - 使用蒙特卡洛搜索在每个时间步获得奖励 。
>
> - 训练另一个神经网络来估计不完整序列的奖励 。
>
> - 然而，上述方法耗时耗资源，并在巨大的动作和状态空间中引入了发散的风险 。
>
> - ​**​自批判序列训练（SCST）​**​：[96]中采用了自批判序列训练（SCST）方法 。SCST的思想是利用其自身的测试时推理算法的输出来规范其经历的长期奖励，而不是专注于估计奖励或如何规范奖励函数 14。在[96]中，一组选定样本的平均长期回报（即整个句子的语义度量）用于规范奖励，并作为目标函数中的基线项，这使得训练稳定且自监督，且几乎没有额外计算成本 。策略网络直到句子完整传输结束才更新 。
>
>
> 除了语义KB的建立研究外，在[102]、[103]、[104]中，针对SemCom场景中的资源分配，提出了一些关于KB存储模型的想法。
>
> - ​**​分层结构（Hierarchical structure）​**​ [102]：
    >
    >     - 任务集的语义KB采用分层结构，其中不可分割的SI单元称为“信念”（beliefs）。
>
>     - 信念所属的级别越高，包含的SI越多。
>
>     - 对于给定任务，可能存在多个可行的语义表示（SR），每个SR只包含每个级别的一个信念。
>
>     - 缺点：难以整合多个描述与任务之间的关系；高层信念完全依赖于前一层的信念，不够灵活，无法表示不连续级别信念的组合。
>
> - ​**​图形结构（Graphical structure）​**​ [9], [103], [104]：
    >
    >     - 图形结构是建模语义知识的一种潜在解决方案，任何两个SI单元在必要时都可以通过边连接。
>
>     - 在[103]、[104]中，作者首次采用图形结构根据句子的语法结构对文本传输的语义知识进行建模。
>
>     - 编码为固定比特长度的标记被视为顶点，两个标记之间的关系通过边反映。
>
>     - 缺点：通用语义KB的建模问题仍未解决。
>
>
> 总而言之，KB-Assisted SE旨在通过引入知识库来更有效地提取和利用语义信息，以适应不同的通信目标，并解决传统DL-based SE模型在多任务场景下的冗余问题。该方法通过存储SI单元的重要性权重和任务关系，指导语义编码过程，并在图像分类等任务中展现出显著的性能提升。然而，KB的建立和建模，特别是通用语义KB的建模，仍然是开放的研究问题。

### 🔬方法

> 作者解决问题的方法/算法是什么？是否基于前人的方法？基于了哪些？

### 📜结论

该论文的核心学术概念围绕语义通信（SemCom）展开，这是一种革命性的架构，旨在将用户和应用需求以及信息的含义整合到数据处理和传输中，预计将成为6G中的核心范式。

主要学术概念包括：

- **语义通信 (SemCom)**：一种将用户和应用需求以及信息含义整合到数据处理和传输中的新型通信架构，旨在超越香农范式，专注于通信的含义和有效性，而非仅仅数据速率。

- **符号学 (Semiotics)**：语义概念的最初引入领域，被定义为句法、语义和语用学的结合 。

  - **句法 (Syntactics)**：关注符号（视觉和语言）的形式特征之间的相互关系，而不考虑其含义 。

  - **语义 (Semantics)**：专门研究不同层面符号的含义 。

  - **语用 (Pragmatics)**：关注符号在符号系统中相对于用户的效用之间的关系 。

- **通信的三个层面 (Weaver's Three Levels of Communication)**：

  - **A 级（技术层面）**：通信符号的传输准确性 。

  - **B 级（语义层面）**：传输符号传达所需含义的精确度 。

  - **C 级（有效性层面）**：接收到的含义在多大程度上有效地影响了预期的行为 。

  - 香农的经典信息论（CIT）仅关注技术层面，而忽略了语义和有效性层面 。

- **语义信息理论 (Theory of Semantic Information, TSI)**：

  - 在20世纪50年代首次提出，旨在量化语义信息 。

  - 一个词的语义信息量可以定义为该词在所考虑的语言模型中能暗示的句子数量的函数，即暗示的句子越多，包含的语义信息越多 。

  - 一个句子的语义信息量可以计算为其熵，即 $I(i) = -\log_2 m(x)$ 。

- **强语义信息理论 (Theory of Strongly Semantic Information, TSSI)**：与“弱”TSI相比，TSSI中真值扮演着重要角色 。

- **语义通信理论 (Theory of Semantic Communication)**：基于语义信息量化方法，旨在实现语义层面的通信 。

  - 在该模型中，源和目的地被建模为 <世界模型 W、背景知识 K、推理 I 和消息解释器> 的四元组 。

  - 香农熵 H(W) 被用来量化源的信息量，即语义熵 。

  - **语义编码 (Semantic Coding)**：将世界模型的观测值映射到特定消息的过程。


该论文的主要发现围绕语义通信（SemCom）在未来互联网，特别是6G网络中的应用和发展。

主要发现包括：

- **SemCom在6G中的重要性**：SemCom被预测为6G中的新核心范式，它通过整合用户和应用需求以及信息含义进行数据处理和传输，以应对传统架构仅关注高传输速率的局限性，并解决大规模数据传输、高效信息交互和实时资源消耗等挑战 。

- **SemCom的分类**：论文将SemCom分为三类：

  - **语义导向通信（Semantic-oriented communication）**：侧重于源数据语义内容的准确性，通过在编码前引入语义表示来捕获核心信息并过滤冗余，以恢复传输消息的含义 。

  - **目标导向通信（Goal-oriented communication）**：捕获实用信息，通信任务的目标在语义提取中起关键作用，输出是直接执行的动作，旨在以有限的网络资源有效完成任务 。

  - **语义感知通信（Semantic-aware communication）**：指任务导向通信中的SemCom（例如自动驾驶、无人机群），通过分析智能体行为和当前环境获取语义信息，而不是从数据源中提取 。

- **SemCom系统设计维度**：SemCom系统的设计被组织成三个维度：

  - **语义信息（SI）提取**：包括基于深度学习（DL）、强化学习（RL）、知识库辅助和语义原生等方法 。

  - **SI传输**：涉及无线环境、有限网络资源和异构网络设备相关的技术和挑战 。

  - **SI度量**：讨论了基于误差、信息年龄（AoI）和信息价值（VoI）的语义度量以及组合度量 。

- **SemCom的优势**：SemCom能够减轻6G网络的无线数据传输负担，提高6G网络控制和管理的效率，并利用信息语义设计有效的网络资源分配方案 。

- **理论发展**：SemCom理论从语义信息论（TSI）和强语义信息论（TSSI）发展到目标导向通信理论，其演变从关注符号传输准确性到有效传达所需含义和影响行为。


### 🤔思考

> 根据文档内容，F. Lessons Learned Summary部分总结了不同语义提取（SE）方法的经验教训，具体内容如下：
>
> ### 1\. **DL-Based SE（深度学习语义提取）**
>
> - **核心优势**：注意力机制在语义提取中起关键作用，尤其在处理文本、图像和音频等多模态数据时表现突出。
>
> - **应用场景**：适用于语义导向通信（semantic-oriented communication），能够实时处理新数据。
>
>
> ### 2\. **RL-Based SE（强化学习语义提取）**
>
> - **特点**：将解码过程转化为循环步骤，逐词解码，强化句子内部词关联的学习。
>
> - **优势**：
    >
    >     - 能够学习不可微分的语义度量函数特征。
>
>     - 支持在线学习，可整合AoI（信息年龄）和传输延迟等动态指标到奖励函数中。
>
>
> ### 3\. **KB-Assisted SE（知识库辅助语义提取）**
>
> - **依赖**：需依赖预构建且同步的知识库（如知识图谱）。
>
> - **局限性**：
    >
    >     - 仅适用于非实时按需服务，因为知识库需在通信前同步。
>
>     - 相比单一模型训练，知识库构建和维护成本更高。
>
>
> ### 4\. **Semantic-Native SE（语义原生提取）**
>
> - **特点**：最接近人类对话方式，无需收发方背景知识完全同步。
>
> - **优势**：通过上下文推理逐步收敛知识，适用于陌生代理间的动态通信。
>
>
> ### 5\. **Specific SE（特定场景语义提取）**
>
> - **应用**：用于语义感知通信（semantic-aware communication），如多智能体协作任务。
>
> - **关键点**：
    >
    >     - 语义提取不直接处理原始数据，而是分析任务属性和代理行为。
>
>     - 需定制化设计，无通用方法，但能显著减少协作中的通信开销。
>
>
> ### 总结对比
>
> - **前三种方法（DL/RL/KB）**：需背景知识同步，适用于语义导向或目标导向通信。
>
> - **语义原生提取**：更灵活，支持动态知识收敛。
>
> - **特定场景提取**：专注于任务协作优化，间接提升系统性能。
>
>
> 这些经验为6G语义通信系统的设计提供了技术选型和场景适配的指导。

## SC中的传输和挑战

“However, the AI-based methods cannot currently be explained by explicit mathematical expressions. To overcome this obstacle, SemCom system designers have to consider how to make connections between the complex and changing wireless environment and sophisticated SemCom mechanisms, so as to obtain insights to guide the system design. Thus, we discuss the impact of the varying fading channel, uncertain SNR, and bit error on the SemCom performance” ([Yang 等, 2023, p. 230](zotero://select/library/items/W9A2Y2H8)) ([pdf](zotero://open-pdf/library/items/QGQ8KE4B?page=18)) 然而，基于人工智能的方法目前无法用显式的数学表达式来解释。为了克服这一障碍，SemCom系统设计人员必须考虑如何在复杂多变的无线环境和复杂的SemCom机制之间建立联系，从而获得指导系统设计的见解。因此，我们讨论了变化的衰落信道、不确定的信噪比和比特误差对SemCom性能的影响。

## 个人思考

偏向于做 **Semantic-Native SE（语义原生提取）** 的内容，跟符合当下ai的前景，不过考虑到这篇文章是2023年写的，距今已有2年，仍需要当前1年内左右的文献做参考。


> Tips: 本文有什么优缺点？你是否对某些内容产生了疑问？  
> 作者给出了哪些结论？哪些是strong conclusions, 哪些又是weak的conclusions（即作者并没有通过实验提供evidence，只在discussion中提到；或实验的数据并没有给出充分的evidence）?  
> 你是否认为某些研究方式可以改进，如何改进？
