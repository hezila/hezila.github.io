---
layout: post
title: App Discovery with Google Play [1/3]
categories: [Machine Learning, Deep Learning, Recommender System]
tags: [deeplearning, recsys]
keywords: [deeplearning, recsys]
---

# Part 2: Personalized Recommendations with Related Apps

In order to create a better overall experience, one must also take into account the tastes of the user and provide personalized recommendations. In this post, a deep learning framework is described which provide personalized recommendations to users based on their previous apps downloads and the context in which they have used.

One particularly strong contextual signal is app relatedness, based on **previous installs** and **search query clicks**. As an example, a user who has search for and plays a lot of graphics-heavy games likely has a preference for apps which are also graphically insense rather than apps with simpler graphics. So, when this user installs a car racign game, the "You might also like" suggestings includes apps which relate to the "seed" app (because they are graphically intense racign games) ranked higher than racing apps with simpler graphics. This allows for a finer level of personalization where the characteristics of the apps are matched with preferences of the user.

To incorporate this app relatedness in recommendation procedure, two approaches are proposed: (a) offline candiate generation i.e., the genration of the potential related apps that other users have downloaded, in addition to the app in question, and (b) online personalized re-ranking, where these candidates are re-ranked using a persinalized ML model.

**Offline Candidate Generation**

The problem of finding related apps can be formulated as a **nearest neighor search** problem. Given an app X, we want to find the *k* nearest apps. To approach it, a deep neural network to predict the next app installed by the user given their previous installs is developed. Output embeddings at the final layer of this deep neural network generally represents the types of apps a given user has installed. We then apply the nearest neighbor algorithm to find teh related apps for a given seed app in the trained embeddeding space. Thus, we perform dimensionality reduction by representing apps using embeddings to help prune the space of potential candidates.

**Online Persinalized Re-ranking**

The objective is to assign scores to the candidates so they can be re-ranked in a personsized way. In this goal, a deep neural network is developed to predict the likelihood of a related app being specifically relevant to the user. The input of this neural network consists of 1) the characteristics of candidate app, 2) user specific context features (e.g., regine, language), 3) user install history.


**From**: [https://research.googleblog.com/2016/12/app-discovery-with-google-play-part-2.html](https://research.googleblog.com/2016/12/app-discovery-with-google-play-part-2.html)
