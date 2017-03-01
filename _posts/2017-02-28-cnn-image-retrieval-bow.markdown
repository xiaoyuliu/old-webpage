---
layout: post
author: xiaoyuliu
date: Tue Feb 28 10:26:48 2017
category: blog
title: "CNN Image Retrieval Learns from BoW: Unsupervised Fine-Tuning with Hard Examples-Notes"
tag:
- unsupervised learning
- image retrieval
- eccv 2017
---

0. From abstract: Fine tune classification task pre-trained CNN(AlexNet or VGG) for image retrieval with hard positive and hard negative examples learned from **3D reconstruction task**.

    <span class="evidence">*Question*</span>: Why can reconstruction task provide clue for hard negative/positive example? How about choosing verification task or recognition task?


1. Compare *Triplet Loss* and *Contrastive Loss*:
    0. In this paper the author chose *contrastive loss* because **generalize better and to converge at higher performance**
    1. Triplet Loss:
    
        $$
        L = \sum_i^N(||f(x^a)-f(x^p)||_2^2 - ||f(x^a)-f(x^n)||^2_2)
        $$
    2. Constrastive Loss:
    
        $$
        L(i,j) = \frac{1}{2}(Y(i,j)||\overline(f)(i)-\overline(f)(j)||^2)+(1-Y(i,j))(\max\{0, \tau-||\overline(f)(i)-\overline(f)(j)||^2\})
        $$

        $$\tau$$ is able to exlude the influence of example when non-matching pairs have large enough distance.

    3. >The triplet loss appears to be inferior in our context; we observe oscillation of the error in the validation set from early epochs, which implies over-fitting.

2. >For a query image *q*, a pool *M(q)* of candidate **positive** images is constructed based on the camera positions in the cluster of *q*. It consists of the *k* images with closest camera centers to the query.
    
    To further sample postive example, three strategies are come up with in the paper:

    0. MAC distance: choose the image with the lowest MAC distance to the query. (MAC distance means inner product between **L2 normalized ReLU** activation of two image representation)
    1. maximum inliers: choose the image with the highest number of co-observed 3D points with the query.
    2. relaxed inliers:
    - change the candidate positive set to **all images** with **enough** number of co-observd 3D points with the query, but not with too extreme scale change
    - select **randomly** from the candidate set
        Help to increase the variance of viewpoints.











