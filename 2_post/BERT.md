---
title: "BERT"
author: "Jiawei Du"
date: 2022-10-25T15:12:55+08:00
draft: false
---
tags: `Paper Reading` 
# BERT
Paper Link: [BERT: Pre-training of Deep Bidirectional Transformers for
Language Understanding](https://arxiv.org/pdf/1810.04805.pdf) \
Code Link: [BERT_Github](https://github.com/google-research/bert) \
Authors: Jacob Devlin, Ming-Wei Chang, Kenton Lee and Kristina Toutanova, Oct 2018

## BERT是什么
>预训练+微调

BERT（Bidirectional Encoder Representations from Transformers）是一种Transformer的双向编码器，旨在通过在左右上下文中共有的条件计算来预先训练来自无标号文本的深度双向表示。因此，经过预先训练的BERT模型只需一个额外的输出层就可以进行微调，从而为各种自然语言处理任务生成最新模型。

## BERT的三大关键
- 使用了transformer的结构
- 现有的微调方法有问题：**标准的语言模型并非双向的**
BERT是一个“深度双向”的模型，意味着BERT在训练阶段从所选文本的上下文中汲取信息
- 预训练资料量巨大：
使用BooksCorpus（800M 单词），和英文维基（2500M 单词）

## BERT的输入
![](https://i.imgur.com/TkejV4x.png)

- Token embedding （字向量）
BERT模型通过查询字向量表将文本中的每个字转换为一维向量，作为模型输入
- Segment embedding （文本向量）
该向量的值在模型训练过程中自动学习，用于刻画文本的全局语义信息，并融合单字/词的语义信息
- 位置向量
因为出现在文本不同位置的字和词所携带的语义信息存在差异，BERT对不同位置的字和词分别附加一个不同的向量以作区分（比如：“我爱你”和“你爱我”）

## BERT的预训练
1. Masked LM(MLM)
- 步骤 \
Step1. 随机遮掩一定比例的输入特征，然后预测这些被遮掩的特征 \
Step2. 最终的输出向量对应被遮掩的词然后输出到softmax层，其对应维度为词典大小 \
Step3. 每句话随机遮掩了15%的词(只预测遮掩的词，而不是重建整个输入) \
- 缺点一 \
预训练和微调之间有些不匹配，因为[MASK]遮掩词汇在微调的时候永远不会被看到 \
Solution: 并不总将“masked”掉的词替换为[MASK]符号，相反处理训练数据时随机挑15%。
>eg. \
例如 my dog is hairy 可能选择了 hairy。随后的过程如下： \
80%的情况：替换选中的词为[MASK]，如 my dog is hairy -> my dog is [MASK] \
10%的情况：将选中的词替换为一个随机词，如 my dog is hairy -> my dog is apple \
10%的情况：保持不变。如 my dog is hairy -> my dog is hairy
- 缺点二 \
收敛速度慢问题 \
原因：每 batch 中只预测了15％的词块，导致收敛速度慢

2. Next Sentence Prediction
- 动机 \
许多重要的下游任务如问答自然语言推断都基于对两句话关系的理解，但这并不能直接由语言模型学到
- 做法 \
当选择句子A和B作为预训练样本时，50%的时候B是A的下一句，50%的时候是一个随机的句子（负样本）。
>eg. \
Input = [CLS] the man went to [MASK] store [SEP] \
he bought a gallon [MASK] milk [SEP] \
Label = IsNext \
Input = [CLS] the man [MASK] to the store [SEP] \
penguin [MASK] are flight ##less birds [SEP] \
Label = NotNext

3. 预训练的过程
- 数据：\
BooksCorpus（800M 单词），和英文维基（2500M 单词）\
为了生成各个输入序列，作者对预料库进行两句两句的采样，被称为“句子”，但实际上比一般的句子要长，但两句的总长度不会超过512个词。
- 参数： \
batch size = 256个序列（即256序列512个词=128,000个词/batch） \
train = 1,000,000 step 约为40 epoch \
data = 33亿个词 \
使用Adam \
lr = 1e-4 \
beta1 = 0.9 \
beta2 = 0.999 \
L2 delay = 0.01 \
learning rate warmup = 10000 steps，然后线性递减 \
dropout = 0.1 (in all layers) \
activation = gelu

4. 微调策略
实验中作者们得到这样的微调是最好的
Batch size: 16，32
Learning rate(Adam): 5e-5，3e-5，2e-5
Number of epochs: 3，4