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

牛顿法首先假设目标函数可以用一个2次项函数很好的近似：
$$
f(x^{(j)} + d^{(j)}) \approx f(x^{(j)}) + (d^{(j)})^T \bigtriangledown f(x^{(j)}) + \frac{1}{2} (d^{(j)})^T \bigtriangledown^2 f(x^{(j)})d^{(j)} =: \mathcal{T}_j(d^{(j)})
$$

最小化上式，我们得到
$$
d^{(j)} = -[ \bigtriangledown^2 f(x^{(j)}) ]^{-1} \bigtriangledown f(x^{j})
$$

缺点：

1. 每步迭代需要计算Hessian
2. 每次迭代需要计算更新方向
3. 需要保证hessian是正定的（如果hessian不是正定的，那么需要修改算法）

拟牛顿法 (Quasi-Newton Method)
--------------------------------

BFGS
-----


Limited Memory BFGS (L-BFGS)
---------------------------


实战
---

Logistic Regression逻辑回归
--------------------------

+ CTR 问题
  
  LR是概率输出用于ctr预估是合理的。不使用线性回归另一个原因是特征空间中
的某个点本身有click和noclick，如果使用线性回归就会合并这些点，
例如一个点上click一次，noclick2次，和另一个点click一百万次，noclick两百万次。
LR可以体现这种置信度，强调这些频繁出现的样本，保证大多数pv预估准确。



参考：
+ [http://www.umiacs.umd.edu/~hal/docs/daume04cg-bfgs.pdf](http://www.umiacs.umd.edu/~hal/docs/daume04cg-bfgs.pdf)
+ [http://blog.droidzone.in/page/3/](http://blog.droidzone.in/page/3/)