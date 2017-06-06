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

0. **Formulation and Loss Function**

    Retrieve relevant images given a query caption, and relevant captions given a query image. When training, the input is ($$I,C$$), image and caption pair, with $$K-1$$ sample images and $$K-1$$ random captions, then the target for the network is 

    - Retrieving $$I$$ from *K* captions given *C* 
    - Retrieving $$C$$ from *K* images given *I*

    The paper uses the sum of two expected negative log-likelihoods of image and caption retrieval over all image-caption pairs(*I,C*):

    $$
    L(\theta) = E_{(I,C)}[-logP_{im}(I|C)] + E_{(I,C)}[-logP_{cap}(C|I)]
    $$

1. **VQA-agnostic Models**

    In VQA-agnostic models for image-caption ranking, there's no prior knowledge utilized from VQA corpora. They use a vectorized image representation, which is the hidden layer activation from an image classification pretrained CNN; and a vectorized caption representation, which is namely the encoded sentence using an RNN.

    This kind of structure requires a high volume of training image-caption ranking dataset to learn the necessary knowledge. The state-of-the-art VQA-agnostic model for image-caption ranking task is also treated as [baseline][1] in the paper. It projects image representation and sentence representation into feature space of the same dimension as sentence representation, and scoring function is the dot product between the embedded unit-norm vectors.

2. **VQA-grounded Models**

    Because there are two different tasks in image-caption ranking, the author takes advantage of **VQA model** to provide caption retrieval needed probability $$P_I(A||Q,I)$$, and **VQA-Caption model** to provide image retrieval needed probability $$P_C(A||Q,C)$$.

    Here, VQA-Caption takes a caption and a question about the same image, then generates an answer.

    And it is worthy to notice, in $$P_I(A_i||Q_i, I_i)$$ if the *question* is "What is the man wearing on his head?" and correct *answer* is "Helmet", **even if there is no person present in the image or mentioned in the caption, the model may still assess the plausibility of a man wearing a helmet**.

    But when using VQA-grounded models only, the sentence structure of caption and image saliency will lose, so the author proposed two strateges to fuse VQA-agnostic and VQA-grounded models.

3. **Fuse Two Models**

    ![fusion](https://cl.ly/1k0R1L41062m/Image%202017-03-27%20at%207.41.28%20PM.png)
    <figcaption class="caption">Two fusion strategies</figcaption>

    - Score-level fusion:
        + First compute score according to two models separately
        + Fuse two scores using two parameters
        To be noticed, the fusion parameters are learned based on validation set in case of overfitting.

    - Representation-level fusion
    
        + First compute image and caption representations in two models
        + Fuse them using several parameters
        These parameters are learned on training set.

### Results

Both score-level and representation-level fusion are better than VQA-agnostic state-of-the-art, which proves the effectiveness of VQA corpora in improving image-caption ranking performance. And among them, representation-level fusion is slightly better than score-level, I guess it's because representation-level fusion model has one more layer.





[1]: https://arxiv.org/abs/1411.2539