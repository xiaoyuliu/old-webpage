---
layout: post
category: blog
author: xiaoyuliu
date: Fri Mar 10 15:03:34 2017
title: 'Recent survey on crowd density estimation and counting for visual surveillanc - Notes'
tags:
- crowd density
- EAAI 2015
---

### Category

1. Direct approach
    
    > Segment and detect each individual in crowd scenes and then counting them using some classifiers.
    
    However, when a severe crowd or occlusions occurred, this method could be very complex. And direct appraoch is also called *detection-based* approach, it can be further divided into:

    - **model-based approach**: segment, detect every single person, and then counting them using a model or appearance of human shapes
    - **trajectory-clustering-based approach**: detect every independent motion in the crowd scene by clustering interest points on people tracked over time and then count the people


2. Indirect approach
    
    > People counting is carried out using the measurements of some features with learning algorithms or statistical analysis of the whole crowd to achieve counting process.
    
    Complicated to understand, while considered to be more robust than direct methods and the basic principle is:

    > The relationship between the foreground area and the number of people in the scene is nearly linear.

    But when heavy occlusions and prespective problems happen, this relation usually fails. Therefore, some appraches have been proposed to utilize local features to deal with issues and to reduce the required training data:

    - **Pixel-based analysis**: Focused on mostly crowd density estimation rather than identifying individuals.
    - **Texture-based analysis**: Mainly used to estimate the number of people rather than counting persons in a scene. [??]
    - **Corner point based analysis**: Treat moving corner points as features to estimate number of moving people

### Benchmark datasets

- [The Mall Dataset](http://personal.ie.cuhk.edu.hk/~ccloy/downloads_mall_dataset.html)
- [Grand Central Dataset](http://www.ee.cuhk.edu.hk/~xgwang/grandcentral.html)
- [QUT Dataset](https://wiki.qut.edu.au/display/cyphy/Datasets)
- [Fudan Pedestrian Dataset](https://www.cis.upenn.edu/~jshi/ped_html/)
- [Pets2009 Dataset](http://www.cvg.reading.ac.uk/PETS2009/a.html)
- [The UCSD Dataset](http://www.svcl.ucsd.edu/projects/peoplecnt/)
- LIBRARY Dataset (can't find)

