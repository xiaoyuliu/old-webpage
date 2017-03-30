---
layout: post
category: blog
author: xiaoyuliu   
date: Thu Mar 30 11:30:23 2017
title: "LabelBank: Revisiting Global Perspectives for Semantic Segmentation - Notes"
tag:
- segmentation
---

This paper leverages holistic information in the form of a LabelBank to improve pixel-level semantic segmentation performance. 

### Framework

![framework][1]
<figcaption class="caption">Semantic segmentation framework</figcaption>

The framework is composed of three components:

- A holistic inference process, taking varied information sources to reason about the LabelBank representation of an image;
- A detailed semantic segmentation process to produce preliminary semantic segmentation map;
- A holistic filtering process, leveraging the inferred LabelBank to filter out false-positive pixel predictions in preliminary semantic segmentation results.

### Holistic Filtering

LabelBank and preliminary segmentation result are two matrix of the same dimension, which contain confidences for assigning each semantic category label on each image pixel. 

After filtering, the final confidence will be high only when both the LabelBank and segmentation confidences are high, and it will be low to 0 either one of the two confidences is low.

### LabelBank Inference

0. SPP for visual appearance
    
    - Employ a feature network (i.e., low-level layers of convolution and pooling) to extract a feature map on an image. 
    - Apply spatial pyramid pooling on the feature map, followed by a multi-layer perception to predict the LabelBank.

1. DC for visual appearance
    
    - Apply the same feature network as the SPP architecture.
    - Densely crop the feature map to obtain features on image windows for location-aware predictions.





[1]: https://cl.ly/0p0z0u2j1R3v/Image%202017-03-30%20at%202.22.19%20PM.png