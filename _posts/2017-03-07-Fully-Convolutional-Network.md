---
layout: post
category: blog
title: "Fully Convolutional Networks for Semantic Segmentation - Notes"
author: xiaoyuliu
date: Tue Mar  7 12:32:44 2017
tag:
- fcn
- cvpr 2015
- segmentation
---

### Contributions

0. No need for patchwise training, faster and similar performance
1. Be able to combine coarse, semantic information and shallow, fine information
2. Be capable of handling with inputs with different sizes

### Comparision of speed between AlexNet and FCN

0. > AlexNet takes 1.2 ms to produce the classification scores of a 227x227 image while the fully convolutional version takes 22 ms to produce a 10x10 grid of outputs from a 500x500 image, which is more than 5 times faster than the naïve approach.

1. > The corresponding backward times for the AlexNet example are 2.4 ms for a single image and 37 ms for a fully convolutional 10×10 output map, resulting in a speedup similar to that of the forward pass.


### Connect coarse outputs and dense pixels

0. **Unmatched size between output and input**

    - Shift-and-stitch

        > If the outputs are downsampled by a factor of $$f$$ , the input is shifted (by left and top padding) *x* pixels to the right and $$y$$ pixels down, once for every value of $$(x,y) \in \{0,...,f − 1\}×\{0,...,f − 1\}$$.

        We interlace $$f^2$$ outputs from $$f^2$$ shifted inputs to produce predictions correspond to the pixels at the *center* of their receptive fields.

        Also we can decrease the stride of one convolutional/pooling layer from *s* to *1*, which causes upsampling the output by *s*, in this case we can adjust the filters and layer strides to keep size of output and input same. **However**, because *the original filter only sees a reduced prtion of its (now upsampled) input*. And there is still extended way to solve the problem.

    - Interpolation

        > Upsampling with factor $$f$$ is convolution with a fractional input stride of 1/$$f$$. So long as $$f$$ is integral, a natural way to upsample is therefore *backwards convolution* (sometimes called *deconvolution*) with an output stride of $$f$$ .

        Using this method, we can upsample the reduced-sized output to the same size with input. And it can be done in-network for end-to-end learning.

    - In-network upsampling is *fast* and *effective* in this paper, and the filter can be learned.

1. **Deep Jet**

The output of upsampling the final pool layer is coarse like Figure 1, because the large pixel stride at the final prediction limits the scale of detail in it. 
![refine comparision](https://cl.ly/3u2v0J0w1j42/Image%202017-03-07%20at%202.48.13%20PM.png)
<figcaption class="caption">Figure 1. Comparision between with deep jet and without</figcaption>
So combining the final prediction layer with lower layers with finer strides(smaller strides) can produce finer segmentation result.

>  As they see fewer pixels, the finer scale predictions should need fewer layers, so it makes sense to make them from shallower net outputs.

Here **"finer scale"** means more concentration on local feature, and shallower net outputs are more suitable for generating finer scale predictions because they pay attention to local regions instead of global images.

### Exact way to do it

![process of deep jet](https://cl.ly/0d1N2P3n3A1f/Image%202017-03-07%20at%202.57.06%20PM.png)
<figcaption class="caption">Figure 2. Details of how to implement deep jet</figcaption>

Simply speaking, the author did a 2x upsampling to the output of a higher layer and fused the upsampled result with the output of a lower layer. Then upsampled the fused result to match the input's size, calculated the loss. And when fusing features inside the network, according to the paper：

> Final layer deconvolutional filters are fixed to bilinear interpolation, while intermediate upsampling layers are initialized to bilinear upsampling, and then learned.

Therefore, when using FCN-32s there's no fusing operation, and the final upsampling should be simply `torch.nn.UpsamplingBilinear2d` in `Pytorch`. And if you want to train this *deconv layer* you can use `torch.nn.ConvTranspose2d`. 




