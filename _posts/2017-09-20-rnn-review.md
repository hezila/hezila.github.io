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


* An input sequence: $$(\mathbf{x}^{(1)}, \mathbf{x}^{(2)}, \ldots, \mathbf{x}^{(T)})$$, where each data point $$\mathbf{x}^{(t)}$$ is a real-valued vector.
* A target sequence: $$(\mathbf{y}^{(1)}, \mathbf{y}^{(2)}, \ldots, \mathbf{y}^{(T)})$$.

Using temporal terminalogy, an input sequence consists of data points $$\mathbf{x}^{(t)}$$ that arrive in a discrete sequence of *time steps* indexed by $$t$$. When a model predicted data points, these are labeled $$\hat{\mathbf{y}}^{(t)}$$.


**Nerual networks**

Generally, a neural network consits of a set of *artifical neurons*, commonly referred to as *nodes* or *units*, and a set of directed edges between them, which intuitively represent the *synapses* in a bilogical neural network. Associated with each nuron $$j$$ is an activation function $$l_j(*)$$ (e.g., $$\sigma$$ sigmoid function), which is sometimes called a link function. The values of the hidden nodes in a network which, as a vector, is commonly noted $$\mathbf{h}$$.

Associated with each edge from node $$j'$$ to $$j$$ is a weight $$w_{jj'}$$ (denotes the "to-from" weight). The value $$v_j$$ of each neuron $$j$$ is calculated by applying its activation function to a weighted sumo of the values of its input nodes:

\begin{equation}
v_j = l_j(\sum_{j'} w_{jj'} v_{j'})
\end{equation}

For convenience, we term the weighted sum as *incoming activation* and note it as $$a_j$$. 

Common choices for the activation function include the *sigmoid* $$\sigma(z) = 1/(1 + \exp^{-z})$$ and the *tanh* function $$\phi(z) = (\exp^z - \exp^{-z})/(\exp^z + \exp^{-z})$$. Another activation function which has become prominent is the *rectified linear unit* (ReLU) whose formula is $$l_j(z) = max(0, z)$$. 


The activation function at the output nodes depends onpon the task. For multiclass classification with $$K$$ aleternative classes, we apply a softmax nolinearity in an output layer of $$K$$ nodes, The softmax function calculates

\begin{equation}
\hat{y}_k = \frac{\exp^{a_k}}{\sum_{k'=1}^{K} \exp^{a_{k'}}}
\end{equation}

For multilabel classification, the activation function is simply a point-wise sigmoid, and for regression we typically have linear output.


**Feedforward networks and backpropagation**

In feedforward network, all nodes can be arranged into layers, and the values of the nodes in each layer are computed successively as a function of the prior layers. The input $$\mathbf{x}$$ to a feedforward network is provided by setting the vaues of the lowest layer. Each higher layer is then successively computed until output is generated at the topmost layer $$\hat{\mathbf{y}}$$. Learning is accompolished byb iteratively updating each of the weights to minimize a loss function, $$\mathcal{L}(\hat{\mathbf{y}}, \mathbf{y})$$, which penalizes the distance between the output $$\hat{\mathbf{y}}$$ and the target $$\mathbf{y}$$.

The most successful algorithm for training neural networks is **backpropagation**. Backpropagation uses the chain rule to calculate the derivation of the loss function $$\mathcal{L}$$ with respect to each parameter in the network. The weights are then adjusted by gradient descent. Because the loss surface is non-convex, there is no assurance that backpropagation will reach a global minimum. 

Nowdays, neural networks are usully trained with *stochastic gradient descent* (SGD) using mini-batches. With batch size equal to one, the stochastic gradient update equation is

\begin{equation}
\mathbf{w} \leftarrow \mathbf{w} - \eta \Delta_{\mathbf{w}} F_i
\end{equation}

where $$\eta$$ is the learning rate and $$\Delta_{\mathbf{w}}F_i$$ is the gradient of the objective function with respect to the parameters $$\mathbf{w}$$ as calculated on a single example $$(x_i, y_i)$$.


To calculate the gradient in a feedforward neural network, backpropagation proceeds as follows. First, an example is propagated forward through the network to produce a value $$v_j$$ at each node and outpus $$\hat{\mathbf{y}}$$ at the topmost layer. Then a loss function value $$\mathcal{L}(\hat{y}_k, y_k)$$ is computed at each output node $$k$$. Subsequently, for each output node $$k$$, we calculate

\begin{equation}
\delta_k = \frac{\partial \mathcal{L}(\hat{y}_k, y_k)}{\partial \hat{y}_k} \cdot {l'}_k(a_k)
\end{equation}

Given these values $$\delta_k$$, for each node in the immediately prior layer we calculate

\begin{equation}
\delta_j = {l'}(a_j)\sum_{k} \delta_k \cdot w_{kj}
\end{equation}

This calculation is performanced successively for each lower layer to yield $$\delta_j$$ for every node $$j$$ given the $$\delta$$ values for each node connected to $$j$$ by an outgoing edge. Each value $$\delta_j$$ represents the derivative $${\partial\mathcal{L}}/{\partial a_j} of the total loss function with respect to that node's incoming activation. 













