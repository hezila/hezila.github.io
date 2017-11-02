---
layout: post
title: The Applications of Deep Learning in Recommender Systems
categories: [Deep Learning, Recommender System]
tags: [deeplearning, recsys]
keywords: [deeplearning, recsys]
---


- **AutoRec: Autoencoders Meet Collaborative Filtering (WWW '15)**

Autoencoder(AE)是一个无监督学习模型，它利用反向传播算法，让模型的输出等于输入。文献[2]利用AE来预测用户对物品missing的评分值，该模型的输入为评分矩阵R中的一行(User-based)或者一列(Item-based)，其目标函数通过计算输入与输出的损失来优化模型，而R中missing的评分值通过模型的输出来预测，进而为用户做推荐。

Denoising Autoencoder(DAE)是在AE的基础之上，对输入的训练数据加入噪声。所以DAE必须学习去除这些噪声而获得真正的没有被噪声污染过的输入数据。因此，这就迫使编码器去学习输入数据的更加鲁棒的表达，通常DAE的泛化能力比一般的AE强。Stacked Denoising Autoencoder(SDAE)是一个多层的AE组成的神经网络，其前一层自编码器的输出作为其后一层自编码器的输入。

- **Collaborative Deep Learning for Recommender Systems (KDD '15)**

在SDAE的基础之上，提出了Bayesian SDAE模型，并利用该模型来学习Item的隐向量，其输入为Item的Side information。该模型假设SDAE中的参数满足高斯分布，同时假设User的隐向量也满足高斯分布，进而利用概率矩阵分解来拟合原始评分矩阵。该模型通过最大后验估计(MAP)得到其要优化的目标函数，进而利用梯度下降学习模型参数，从而得到User与Item对应的隐向量矩阵。

- **A Hybrid Collaborative Filtering Model with Deep Structure for Recommender Systems (AAAI '17)**

携程基础BI算法团队通过改进现有的深度模型，提出了一种新的混合协同过滤模型. 利用User和Item的评分矩阵R以及对应的Side information来学习User和Item的隐向量矩阵U与V，进而预测出评分矩阵R中missing的值，并为用户做物品推荐。Additional Stacked Denoising Autoencoder(aSDAE)的深度模型用来学习User和Item的隐向量，该模型的输入为User或者Item的评分值列表，每个隐层都会接受其对应的Side information信息的输入(该模型灵感来自于NLP中的Seq-2-Seq模型，每层都会接受一个输入，我们的模型中每层接受的输入都是一样的，因此最终的输出也尽可能的与输入相等)。

结合aSDAE与矩阵分解模型, 模型通过两个aSDAE学习User与Item的隐向量，通过两个学习到隐向量的内积去拟合原始评分矩阵R中存在的值，其目标函数由矩阵分解以及两个aSDAE的损失函数组成，可通过stochastic gradient descent(SGD)学习出U与V。

- **Deep Neural Networks for YouTube Recommendations (Recsys '16)**

Google利用DNN来做YouTube的视频推荐. 通过对用户观看的视频，搜索的关键字做embedding，然后在串联上用户的side information等信息，作为DNN的输入，利用一个多层的DNN学习出用户的隐向量，然后在其上面加上一层softmax学习出Item的隐向量，进而即可为用户做Top-N的推荐。

- **Convolutional Matrix Factorization for Document Context-Aware Recommendation (Recsys '16)**

通过卷积神经网络(CNN)提出了一种卷积矩阵分解，来做文档的推荐，该模型结合了概率矩阵分解(PMF)与CNN模型。该模型利用CNN来学习Item的隐向量，其对文档的每个词先做embedding，然后拼接所有词组成一个矩阵embedding矩阵，一篇文档即可用一个二维矩阵表示，其中矩阵的行即为文档中词的个数，列即为embedding词向量的长度，然后在该矩阵上做卷积、池化以及映射等，即可得到item的隐向量。User的隐向量和PMF中一样，假设其满足高斯分布，其目标函数由矩阵分解以及CNN的损失函数组成。
