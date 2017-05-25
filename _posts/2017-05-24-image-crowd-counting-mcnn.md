---
title: "Single-Image Crowd Counting via Multi-Column Convolutional Neural Network - Notes"
date: Wed May 24 16:32:00 2017
layout: post
category: blog
author: xiaoyuliu
tag: 
- crowd counting
- density map estimation
---

This paper utilized a 3-col CNN(Multi-col CNN, MCNN) to address crowd counting problem by summing up estimated density map based on still images. Furthermore, the author came up with a new labeled dataset for dense crowd count task.

### Crowd Counting

No matter from perspective of public safety or crowd control, crowd counting has become a pretty important practical problem. Traditional methods to resolve this question developed from detection-based to regression-based algorithms.

0. **Detection-based**: People assumed that every individual can be accurately detected and be used for crowd count. It is apparent that occlusion can have significantly negative effect on this kind of algorithm, also this is called direct count.
1. **Regression-based**: It is named indirect count as well because this method makes use of various features from the foreground of still image, such as area of crowd mask, edge count, or texture features. Then a linear or nonlinear regression model is applied on the features to generate count value.

### Network Structure

![Network structure](https://cl.ly/2O3o341G411i/Image%202017-05-24%20at%204.46.48%20PM.png)

The structure of 3-col CNN is pretty simple and straightforward to understand, there are several points about the network:

- The intention of multi-col design is for addressing heads of different sizes, increasing the network's robustness to different situations. 
- There is no fully-connected layer inside MCNN, which is flexible to inputs of different sizes.  
- Before convolving with the final $$1x1$$ conv layer, feature maps from 3 networks are concatenated together. It should be noted that all feature maps before concatenating share the same size only different number of channels, so they can be concatenated.
- During training, the output density map is of $$[\frac{W}{4}, \frac{H}{4}]$$. In order to calculate loss, the author downsampled the original input to of the same size with output, then generated corresponding ground truth density map. Finally, they use Euclidean distance as training loss.
