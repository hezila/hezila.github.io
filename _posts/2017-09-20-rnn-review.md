---
layout: post
title: The review of Recurrant Neural Network
tags: [rnn, deeplearning]
keywords: [rnn]
---

Introduction
-------------

Neural networks are powerful learning models that archive state-of-the-art performance in a wide range of machine learning tasks. They are suited especially well for *machine perception tasks*, where the raw underlying features are not individually interpretable. This success is attributed to their ability to **learn hierarchical representations**, unlike traidtional methods that rely upon hand-engineered freatures. 
Recurrent neural networks (RNNs) are *connectionist models* with the ability to selectively pass information across sequence steps, while processing sequential data one element at a time. 


**RNNs vs Markov models?**


Markov model approaches are limited because their states must be drawn from a modestly sized **descrete state space**. And each hidden state in Markov model can depend only on the immediately previous state. While, **RNNs** is able to capture *long-range time dependencies*, overcoming the chief limitation of Markov models (the hidden state at any step can contain information from a nearly arbitraily long context window). 


Background
---------

**Notations**


* An input sequence: $$(\mathbf{x}^{(1)}, \mathbf{x}^{(x)}, \ldots, \mathbf{x}^{(T)})$$, where each data point $$$\mathbf{x}^{(t)}$$$ is a real-valued vector.
* A target sequence: $$$(\mathbf{y}^{(1)}, \mathbf{y}^{(x)}, \ldots, \mathbf{y}^{(T)})$$$.

Using temporal terminalogy, an input sequence consists of data points $$$\mathbf{x}^{(t)}$$$ that arrive in a discrete sequence of *time steps* indexed by $$$t$$$. When a model predicted data points, these are labeled $$$\hat{\mathbf{y}}^{(t)}$$$.


**Nerual networks**








