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


<table style="max-width: 600px;">
<thead>
<tr class="header">
<th align="center" width="50%">MNIST</th>
<th align="center" width="50%">CIFAR-10</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="center" width="50%"><img src="http://785j7b.com1.z0.glb.clouddn.com/mnist.png"></td>
<td align="center" width="50%"><img src="http://785j7b.com1.z0.glb.clouddn.com/cifar_10.png"></td>
</tr>
</tbody>
</table>


First, lets define some methods to load and reshape the downloaded data into the necessary format.


```
def randomize(dataset, labels):
    permutation = np.random.permutation(labels.shape[0])
    shuffled_datadet = dataset[permutation, :, :]
    shuffled_labels = labels[permutation]
    return (shuffled_dataset, shuffled_labels)

def one_hot_encode(np_array):
    return (np.arange(10) == np_array[:, None]).astype(np.float32)

def reformat_data(dataset, labels, image_width, image_height, image_depth):
    _dataset = np.array([np.array(image_date).reshape(image_width, image_height, image_depth) for image_data in dataset])
    _labels = one_hot_encode(np.array(labels, dtype=np.float32))
    return randomize(_dataset, _labels)

def flatten_tf_array(array):
    # flatting an array (a fully connected network needs an flat array as its input)
    shape = array.get_shape().as_list()
    return tf.reshape(array, [shape[0], shape[1] * shape[2] * shape[3]])

def accuracy(preds, labels):
    return (100.0 * np.sum(np.argmax(preds, 1) == np.argmax(labels, 1)) / preds.shape[0])

```

Now, we can load the MNIST and CIFAR-10 datasets with:

```

# load MNIST dataset
mnist_folder = './data/minist'
mnist_image_width = 28
mnist_image_height = 28
mnist_image_depth = 1
mnist_num_labels = 10

mndata = MNIST(mnist_folder)
_mnist_train_dataset, _mnist_train_labels = mndata.load_training()
_mnist_test_dataset, _mnist_test_labels = mndata.load_testing()

mnist_train_dataset, mnist_train_labels = reformat_data(_mnist_train_dataset, _mnist_train_labels, mnist_image_width, mnist_image_height, mnist_image_height)
mnist_test_dataset, mnist_test_labels = reformat_data(_mnist_test_dataset, _mnist_test_labels, mnist_image_width, mnist_image_height, mnist_image_height)


# load CIFAR-10 dataset
c10_folder = './data/cifar10/'
train_datasets = ['data_batch_%d' % n for n in range(1, 6)]
test_dataset = ['test_batch']

c10_image_width = 32
c10_image_height = 32
c10_image_depth = 3
c10_num_labels = 10

with open(c10_folder + test_dataset[0]) as f:
    c10_test_dict = pickle.load(f, encoding='bytes')

_c10_test_dataset, _c10_test_labels = c10_test_dict[b'data'], c10_test_dict[b'labels']
c10_test_dataset, c10_test_labels = reformat_data(_c10_test_dataset, _c10_test_labels, c10_image_width, c10_image_height, c10_image_depth)


_c10_train_dataset, _c10_train_labels = [], []
for train_dataset in train_datasets:
    with open(c10_folder + train_dataset) as f:
        c10_train_dict = pickle.load(f, encoding='bytes')
        _c10_train_dataset_, _c10_train_labels_ = c10_train_dict[b'data'], c10_train_dict[b'labels']
        _c10_train_dataset.append(_c10_train_dataset_)
        _c10_train_labels += _c10_train_labels_

_c10_train_dataset = np.concatenate(_c10_train_dataset, axis=0)
c10_train_dataset, c10_train_labels = reformat_data(_c10_train_dataset, _c10_train_labels, c10_image_width, c10_image_height, c10_image_depth)
del _c10_train_dataset
del _c10_train_labels

```

## 2.3 Creating a 1-layer Neural Network
