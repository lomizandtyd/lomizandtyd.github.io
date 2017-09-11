---
title: 'work schedule: 2017/08/18'
date: 2017-08-18 09:49:33
tags: work schedule
---

## 韵律结构预测

### 韵律结构总述

韵律结构预测，需要预测给定文本在朗读时需要的停顿和间断。它也影响了后续合成输入时的文本特征生成结果。

韵律层级，主要包括韵律词（PW）、韵律短语（PPH）以及韵律短句（PS）等多个层级。具体划分依赖不同的标准。理论上，韵律词的划分，是基于词典词；韵律短语的划分则是基于韵律词边界。

韵律结构预测的主要困难，还是来源于不同人的划分标准不一致，同一个不同时间的划分标准不一致。从数据层面导致了韵律结构预测的困难。

### 韵律结构预测方法

[基于规则学习的韵律结构预测(赵晟)](http://jcip.cipsc.org.cn/CN/article/downloadArticleFile.do?attachType=PDF&id=1272)

[语法信息与韵律结构的分析与预测(王永鑫)](http://jcip.cipsc.org.cn/CN/article/downloadArticleFile.do?attachType=PDF&id=1328)

[基于语法信息的汉语韵律结构预测(曹剑芬)](http://jcip.cipsc.org.cn/CN/article/downloadArticleFile.do?attachType=PDF&id=1313)

Multi-Task Learning for Prosodic Structure Generation using BLSTM RNN with Structured Output Layer(Yuchen Huang)

韵律结构预测的相关文献比较少。基本上可以看作是一类序列标注的问题。

在输入特征处理上，除原始的文本数据外，还可能用到：
1. POS 词性信息
2. Word2Vec 词向量
3. 声调信息，上下文窗
4. 其它

实际上，在我的实验中，目前仅用到了pos词性信息。

在标注模型上，一般用到的方法包括：
1. 决策树
2. 条件随机场 
3. RNN

有关衡量标准，目前基本以准确率，精确率，召回率以及 F1 / F0.5 为主。

### 韵律结构预测存在的问题

为了解决标注标准不一的问题，韵律语料库往往是由多人一起讨论标注。因此，可以先假设，对于给定语料库有一个单一的标准。从目前的实验来看，同一语料库内的标准相近，不同语料库间的差异较大。

合成语料库的韵律结构，通常是根据说话人当时的说话状态来确定的。这与纯文本语料库之间存在巨大的差异。

衡量标准的问题，准确率、精确率、召回率以及F1等，尽管是经过实际考验的客观评价标准。但对于具体的语料库而言，它并不能真正的反映预测结果的好坏。

### 客观评价标准中存在的问题

没有估计到，phrase级别的平均句错误率。