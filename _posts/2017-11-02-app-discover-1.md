---
layout: post
title: App Discovery with Google Play [1/3]
categories: [Machine Learning, Deep Learning, Recommender System]
tags: [deeplearning, recsys]
keywords: [deeplearning, recsys]
---

# Part 1: Understanding of the Topics associated with an App

Most of time, people don't really know what they want specifically, and they only have a broad notion of interest, like "horror games" and "selfie apps". Such broad searches by topics happens nearlly half of the quries in Play Store.

Searches by topic require more than simply indexing apps by query terms; they require **an understanding of the topics** associated with an app. Some machine learning approaches have been proposed to address this problem, but their success heavilly depends on the number of training examples. While for some popular topics such as "social network", we had many labeled apps to learn from, the majority of topics had only a handful of examples. The challenge was to learn a very limited number of training examples and scale to millions of apps across thousands of topics.

The inital attempt was to build a deep neural network (DNN) trained to predict topics for an app based on words and phrases from the app title and description. However, given the learning capacity of DNNs, it completely "memorized" the topics for the apps in our small training data, and failed to generailze to new apps that hadn't been seen before.

In contrast to DNNs, human beings need much less training data. Just by knowing the language describing the apps, people can correctly infer topics from even a few examples. To emulate this, we tried a very rough approximation of this language-centric learning. We tained a neural network to learn how language was used to describe apps. We built a **[Skip-gram model](https://www.tensorflow.org/tutorials/word2vec#the-skip-gram-model)**, where the neural network attempts to predict the words aroud a given word, for example "share" given "photo". The neural network encodes its knowledges as vectors, referred as *embeddings*. These embeddings were used to train another model called a *classifier*, capable of distinguilshing which topics applied to an app. This approach need much less training data to learn about app topics, due to the large amout of learning already done with skip-gram.

While this approach generalized well for popular topics liek "*social networking*", we ran into a new problem for more niche topics liek "*selfie*". The single classifier built to predict all the topics together focused most of its learning on the popular topics, ignoring the errors it made on the less common ones. To solve this problem, we build a seperate classifier for each topic and tuned them in isolation.



**From**: [https://research.googleblog.com/2016/11/app-discovery-with-google-play-part-1.html](https://research.googleblog.com/2016/11/app-discovery-with-google-play-part-1.html)
