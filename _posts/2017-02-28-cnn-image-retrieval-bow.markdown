---
layout: post
author: xiaoyu
date: Tue Feb 28 10:26:48 2017
category: blog
title: "CNN Image Retrieval Learns from BoW: Unsupervised Fine-Tuning with Hard Examples-Notes"
tag:
- unsupervised learning
- image retrieval
- eccv 2017
---

0. From abstract: Fine tune classification task pre-trained CNN(AlexNet or VGG) for image retrieval with hard positive and hard negative examples learned from **3D reconstruction task**.

    <font color=#ff1493>*Question*</font>: Why can reconstruction task provide clue for hard negative/positive example? How about choosing verification task or recognition task?

1. Compare *Triplet Loss* and *Contrastive Loss*:
    0. In this paper the author chose *contrastive loss* because **generalize better and to converge at higher performance**
    1. Triplet Loss:
    2. Constrastive Loss:
        $$L(i,j) = \frac{1}{2}(Y(i,j)||f(i)-f(j)||^2)$$
2. 










