---
layout: post
title: 机器学习中的优化方法
tags: [Machine Learning, Optimization, BFGS, Netween, LBFGS]
keywords: [机器学习, 参数优化, BFGS, LBFGS]
---

牛顿法 (Newton Method)
-----------------------

在第 $$$j$$$ 次线性迭代搜索过程中，我们都需要计算两个值：搜索更新方向
($$$ d^{(j)} $$$)和更新幅度 ($$$ \alpha_j $$$). 参数$$$ x^{(j+1)} $$$
迭代更新的公式为：
$$
x^{(j+1)} = x^{(j)} + \alpha_j d^{(j)}
$$
不同的线性搜索方法区别就在于$$$ d^{(j)} $$$ 和 $$$ \alpha_j $$$ 的选取方式不同。


拟牛顿法 (Quasi-Newton Method)
--------------------------------

BFGS
-----


Limited Memory BFGS (L-BFGS)
---------------------------
