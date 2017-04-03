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


[1]: https://cl.ly/0R1l0620011Q/Image%202017-04-02%20at%205.42.26%20PM.png