---
layout: post
title: Triks for Distributed Tensorflow
categories: [Machine Learning, Deep Learning, AI]
tags: [tensorflow, deeplearning]
keywords: [tensorflow, deeplearning]
---

**From**: [TensorFlow在微博的大规模应用与实践](https://www.weibo.com/ttarticle/p/show?id=2309404163888068345147)

# Distribute Tensorflow

The distributed tensorflow is very simple, which consists of several **parameter severs** and some **workers**. In each iteration, the updated parameters (i.e., **local parameters**) resulted from each work will be feeded into parameter servers. Then, the local parameters from different workers will be merged by parameter server as **global parameters**, which will be send to workers for future computing in next iteration.

- The bottleneck of tensorflow is parameter server as it requires large bandwidth to pass all parameters
- don't use synchronous update
- use padas to load csv file (as feature sources)
