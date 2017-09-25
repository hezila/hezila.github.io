---
layout: post
title: The tensorflow tutorials - Background [1/4]
categories: [Machine Learning, Deep Learning]
tags: [tensorflow, deeplearning]
keywords: [tensorflow, deeplearning]
---

**From**: [Building Convolutional Neural Network with Tensorflow](http://ataspinar.com/2017/08/15/building-convolutional-neural-networks-with-tensorflow/)

# 1. Tensorflow basics


Here, I will give a basic introduction to tensorflow for newbies (you can go ahead and skip to this section if you are already familiar with Tensorflow). This series are designed to get you quickly up to speed up with *deep learning*.

## 1.1 Constants and Variables

The basic units in tensorflow are **contants**, **variables** and **placeholders**. The `tf.constant` has a contant value which can not be changed, and `tf.Variable` can be changed after it has been set.

For example, we can create weight matrices and biases vectors which can be used in a neural network.

```
weights = tf.Variable(tf.truncated_normal([256 * 256, 10]))
biases = tf.Variable(tf.zeros([10]))
print(weights.get_shape().as_list())
print(biases.get_shape().as_list())
```

## 1.2 Tensorflow Graphs and Sessions

In Tensorflow, all of the different **variables** and the **operations** done on these variables are saved in a graph. After you have build a graph which contains all of the computationsl steps for your model, you can run this graph within a **session**. This session then distributes all of the computations across the available CPU an GPU resources.

```
graph = tf.Graph()
with graph.as_default():
    a = tf.Variable(8, tf.float32)
    b = tf.Variable(tf.zeros([2,2], tf.float32))

with tf.Session(graph=graph) as sess:
    tf.global_variables_initialier().run()
```

## 1.3 Placeholders and feed_dict

The **placeholders** in Tensorflow do not require an initial value and only serve to allocate the necessary amount of memory. During a session, the placeholder can be filled in with (external) data with a **feed_dict**.

```
list_of_points1 = [[1,2], [3,4], [5, 6], [7, 8]]
list_of_points2 = [[15, 16], [13, 14], [11, 12], [9, 10]]
list_of_points1 = np.array([np.array(item).reshape(1, 2) for item in list_of_points1])
list_of_points2 = np.array([np.array(item).reshape(1, 2) for item in list_of_points1])

graph = tf.Graph()
with graph.as_default():
    point1 = tf.placeholder(tf.float32, shape=(1, 2))
    point2 = tf.placeholder(tf.float32, shape=(1, 2))

    def cal_euclidian_dist(p, q):
        diff = tf.subtract(p, q)
        power2 = tf.pow(diff, tf.constant(2.0, shape=(1, 2)))
        add = tf.reduce_sum(power2)
        eclidian_dist = tf.sqrt(add)
        return eclidian_dist

    dist = cal_euclidian_dist(point1, point2)

with tf.Session(graph=graph) as sess:
    tf.global_variables_initializer().run()
    for i in range(len(list_of_points1)):
        p = list_of_points1[i]
        q = list_of_points2[i]
        feed_dict = {point1: p, point2: q}
        distance = sess.run([dist], feed_dict=feed_dict)
        print("the distance between {} and {} -> {}").format(p, q, distance)

```
