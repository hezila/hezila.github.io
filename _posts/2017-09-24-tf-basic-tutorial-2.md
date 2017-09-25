---
layout: post
title: The tensorflow tutorials - Neural Network [2/4]
categories: [Machine Learning, Deep Learning]
tags: [tensorflow, deeplearning]
keywords: [tensorflow, deeplearning]
---

# 2. Neural Networks in Tensorflow

## 2.1 Introduction

<p><img src="http://785j7b.com1.z0.glb.clouddn.com/tensorflow_model-1.png" alt="neural network in tensorflow" /></p>

The above image demonstrate how the neural network models work in tensorflow by the following steps:

1. The **input data**: the training, validation, and test dataset. The test and validation datasets can be placed in a `tf.constant()`. And the training dataset is placed in `tf.placeholder()`, so that it can be feeded in batches during the process of optimizing the parameters contains in the model.

2. The **Nerual Network Model** with all of its layers. This can be a simple fully connected neural network with only single layer, or a more complicated neural network with 5, 9, 16 etc layers.

3. The **weights** matrices and **biases** vectors defined in the proper shape and initialized to their initial values (*One weight matrix and bias vector per layer*).

4. The **loss** value: the model has output and by comparing the output with the groud truth, we can calculate teh loss value (e.g., with the softmax with cross-entropy in classification case). The loss value is an indication of how close the estimated trainging labels are to the actual training labels and will be used to update the weight values.

5. An **optimizer**, which will use the calculated loss value to update the weights and biases with backpropagation.

## 2.2 Loading the data

First, we will load the dataset which we are going to use to train and test neural networks. Here, we will download the [MNIST](http://yann.lecun.com/exdb/mnist/) (classification of handwritten digits) and [CIFAR-10](https://www.cs.toronto.edu/~kriz/cifar.html) (classification of small image across 10 distinct classes). The MNIST dataset contains 60.000 images where each image size is 28x28x1 (grayscale). The CIFAR-10 dataset contains 60.000 colour images (3 channels) - size 32x32x3.


<table>
<thead>
<tr class="header">
<th align="center">MNIST</th>
<th align="center">CIFAR-10</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="center"><img src="http://785j7b.com1.z0.glb.clouddn.com/mnist.png"></td>
<td align="center"><img src="http://785j7b.com1.z0.glb.clouddn.com/cifar_10.png"></td>
</tr>
</tbody>
</table>
