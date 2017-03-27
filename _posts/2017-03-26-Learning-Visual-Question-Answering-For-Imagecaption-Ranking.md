---
layout: post
category: blog
author: xiaoyuliu
date: Sun 26 Mar 2017 02:54:53 PM PDT
title: "Leveraging Visual Question Answering for Image-Caption Ranking - Notes"
tag:
- VQA
- image caption ranking
- eccv 2016
---

This paper focused on ultilizing pre-trained VQA model as a **feature extraction** method to improve image caption ranking performance. The author **embeded** images and captions into the space of VQA questions and answers using VQA models.

### Image-Caption Ranking

Definition: Retrieve relevant images given a query caption, and relevant captions given a query image. When training, the input is ($$I,C$$), image and caption pair, with $$K-1$$ sample images and $$K-1$$ random captions, then the target for the network is 

- **Retrieving $$I$$ from *K* captions given *C* **
- **Retrieving $$C$$ from *K* images given *I* **

The paper uses the sum of two expected negative log-likelihoods of image and caption retrieval over all image-caption pairs(*I,C*):

$$
L(\theta) = E_{(I,C)}[-logP_{im}(I|C)] + E_{(I,C)[-logP_{cap}(C|I)]}
$$



