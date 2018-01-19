---
title: Details about faster RCNN for object detection
author: xiaoyuliu
date: Wed 27 Dec 2017 11:11:21 AM PST
category: blog
layout: post
tag:
- detection
- tip
---

**This post is to specify some details from faster rcnn series for object detection myself.**

0. **NMS(Non-Maximum Surppression)**
    [python implementation][1]:

    ```py
    # --------------------------------------------------------
    # Fast R-CNN
    # Copyright (c) 2015 Microsoft
    # Licensed under The MIT License [see LICENSE for details]
    # Written by Ross Girshick
    # --------------------------------------------------------

    import numpy as np

    def py_cpu_nms(dets, thresh):
        """Pure Python NMS baseline."""
        x1 = dets[:, 0]
        y1 = dets[:, 1]
        x2 = dets[:, 2]
        y2 = dets[:, 3]
        scores = dets[:, 4]

        areas = (x2 - x1 + 1) * (y2 - y1 + 1)
        order = scores.argsort()[::-1] 
        # sort the detections according to scores, high to low values.

        keep = []

        # keep doing until all bboxes are covered by THIS BBOX
        while order.size > 0: 
            # choose the bbox with highest iou score, namely THIS BBOX
            i = order[0] 
            keep.append(i)
            xx1 = np.maximum(x1[i], x1[order[1:]])
            yy1 = np.maximum(y1[i], y1[order[1:]])
            xx2 = np.minimum(x2[i], x2[order[1:]])
            yy2 = np.minimum(y2[i], y2[order[1:]])

            w = np.maximum(0.0, xx2 - xx1 + 1)
            h = np.maximum(0.0, yy2 - yy1 + 1)
            inter = w * h
            ovr = inter / (areas[i] + areas[order[1:]] - inter)

            # filter out all detections which have IOUs larger than the threshold, which means they are covered by THIS BBOX
            inds = np.where(ovr <= thresh)[0] 
            order = order[inds + 1]

    return keep
    ```
    From the scripts we can tell the principle of NMS is searching the local maximum value and surpressing others. Therefore, starting from the *prediction bbox*(MAX) with highest iou score with its *ground-truth bbox*, excluding all bboxes with $$iou(THIS, MAX) \geqslant THRESH$$. Then selecting the next prediction bbox with the highest iou from the left bboxes until no bbox left.

1. **Is NMS really effective?**
    In [Ren et al.][2], a similar mAP without NMS during testing can be achieved at the cost of evaluating more RoIs. However it is not clear whether NMS is necessary during training procedure.

    - In a [Fast RCNN related paper][3], NMS is believed to be crucial for selecting hard examples, so should be beneficial for training.
    - In [Chen et al.][4], to figure out whether NMS can be helpful for Faster RCNN as well. They designed 4 training strategies: *NMS*, *ALL*, *PRE* and *POW*; 2 test strategies: *NMS* and *TOP*:
        + *NMS* means using the original NMS method
        + *ALL* means simply feeding all top K regions for positive/negative without NMS
        + *PRE* means calculating the final sample ratio according to some pre-trained Faster RCNN model that used NMS to fit the training distribution
        + *POW* means the sample ratio is not related to NMS at all, but to the scale: $$r(s) = s^{-\gamma}$$
        + *TOP* means simply selecting top K proposals for evaluation directly

        The conclusion is: For testing, *TOP* has a constant advantage when K is large enough; for training, the difference between *POW* and *NMS* narrows down when the training iteration increases.


2. **SOFT-NMS**
    The problem of NMS:
    When two ground-truth bboxes are highly overlaped, the bbox with the lower score will be excluded(score is set to 0), which harms the performance of object detection.

    Soft-NMS means treating the score as a function of IOU, so the bbox with the lower score will not be deleted directly. There are two ways achieving the target, *Linear Function* and *Gaussian Function*. [More details][5].

    According to the paper, on MSCOCO dataset, `mAP@[0.5:0.95]` of R-FCN and Faster RCNN models can be increased by around `1%`.

3. TBD



[1]: https://github.com/rbgirshick/py-faster-rcnn/blob/master/lib/nms/py_cpu_nms.py
[2]: https://arxiv.org/abs/1506.01497
[3]: https://arxiv.org/abs/1604.03540
[4]: https://arxiv.org/abs/1702.02138
[5]: https://arxiv.org/pdf/1704.04503.pdf