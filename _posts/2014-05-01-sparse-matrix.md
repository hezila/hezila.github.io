---
layout: post
title: 用什么数据结构表达稀疏矩阵比较好?
tags: [Machine Learning, Programming]
keywords: [matrix,矩阵]
---

用什么数据结构表达稀疏矩阵比较好?
----------------------------------


两个候选，(1) unordered_map 哈希表，(2) vector 。知道矩阵大小，但不知道非零值的个数，所以在计算过程中数据结构可能动态变化，譬如需要分配内存。如果每个线程都有一个这样的数据结构，使用默认allocator就可能造成resource contention。

目前的稀疏矩阵乘法（SpMV和SpMM）比MKL快2-4倍~ 

