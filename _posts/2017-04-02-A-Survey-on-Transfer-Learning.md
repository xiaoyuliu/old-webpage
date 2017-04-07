---
layout: post
category: blog
author: xiaoyuliu
date: Sun Apr  2 14:07:08 2017
title: "A Survey on Transfer Learning - Notes"
tag:
- transfer learning
---

Transfer learning is to address the difference between feature space of training dataset and test dataset without expensive data relabeling effort.

### Motivation

Many machine learning methods work with a restriction that **the training and test data share the same feature space and the same distribution**.Therefore, when the distribution changes, most statistical models need to be retrained from scratch using newly collected data.

### Multi-task Learning v.s. Transfer Learning

![comparison][1]

In multi-task learning, it aimes at learning all the *source* and *target* tasks simultaneously.

In transfer learning, it aimes at extracting knowledge from *source* tasks and applying to a *target* task, it cares *target* more.

### Definition

There are two tasks in all learning procedures, *source* task and *target* task, each is composed of a feature space *X* and a marginal probability distribution *P* over the feature space. 

In traditional machine learning problem, $$X_S = X_T$$ and $$P(Y_{S} \vert X_{S}) = P(Y_{T} \vert X_{S})$$, source task and target task share the same input feature space and marginal probability distribution. Take document classification as an example, input feature space can be treated as language used in the document, marginal probability distribution is considered as topic of the document. As contrarary, in transfer learning, either $$X_S \neq X_T$$ or $$P(Y_{S} \vert X_{S}) \neq P(Y_{T} \vert X_{S})$$, which means either the language in the document is not the same or the documents are for different topics.








[1]: https://cl.ly/0R1l0620011Q/Image%202017-04-02%20at%205.42.26%20PM.png