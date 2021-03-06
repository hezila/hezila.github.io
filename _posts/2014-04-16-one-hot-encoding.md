---
layout: post
title: 机器学习类别特征的预处理：独热编码（One-Hot Encoding）
tags: [Machine Learning]
keywords: [机器学习,实战,数据预处理]
---

在实际的机器学习任务中，很多特征不是连续型的实数值，有些是分类值。例如，
性别，国籍等等。这类变量不能直接在机器学习的模型中应用（决策树和Bayesian模型除外）。
最简单的处理方式是讲不同类别随机的映射到一个整数（例如，男性：0，女性：1）。
但是这中处理方式就将类别特征堪称是有序的，很显然这是不符合实际的情况的。

*** 独热编码(One-Hot Encoding) ***

在实际问题中，对于这类问题，最常用的方法就是把类别特征转换成二元特征编码。
如果一个类别特征有m中可能，那么这个特征就对应了m位的01字符串。实际就是
转换成了稀疏特征(在marketing领域，又叫dummy变量)。

