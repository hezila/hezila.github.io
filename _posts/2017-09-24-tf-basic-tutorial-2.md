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
