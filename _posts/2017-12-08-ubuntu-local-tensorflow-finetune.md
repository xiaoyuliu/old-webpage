---
layout: post
category: blog
title: "Fine-tune pre-trained model on a MSCOCO subset using Tensorflow locally"
author: xiaoyuliu
date: Fri Dec 8 10:13:48 2017
tag:
- tensorflow
- Ubuntu
---

#### This is simply a record for my own use to fine-tune a pre-trained tensorflow model on 6 subcategories of MSCOCO dataset.

0. Prepare dataset 

    I use the code from [Xitao Zhang's github repository][1] and modify `create_coco_tf_record.py`:

    ```
    LABELS_EXPECTED = {1,2,3,4,6,8}

    for ann in anns:
        category_id = ann['category_id']
        if not category_id in LABELS_EXPECTED:
            continue
        bboxes_data = ann['bbox']
        bboxes_data = [bboxes_data[0]/float(pic_width), bboxes_data[1]/float(pic_height),\
                              bboxes_data[2]/float(pic_width), bboxes_data[3]/float(pic_height)]
                     # the format of coco bounding boxs is [Xmin, Ymin, width, height]
        bboxes.append(bboxes_data)
        # labels.append(ann['category_id'])
        if category_id in range(1, 5):
            labels.append(category_id)
        elif category_id == 6:
            labels.append(5)
        elif category_id == 8:
            labels.append(6)
    ```

    Follow the instruction of installation and running from the repo. Then you can get the `train.record` and `eval.record`. For training, I produced only the `train.record`.

1. Train the model

    - Download a pre-trained model from [detection_model_zoo][2]
    - Object detection training pipeline: I modify one from `/path/to/pretrainedModels/faster_rcnn_resnet50_lowproposals_coco_2017_11_08/pipeline.config`, simply changing `num_classes`, `fine_tune_checkpoint`, `num_steps`, `label_map_path` and `tf_record_input_reader/input_path`.
    - Use the following command to train
    ```
    # From the tensorflow/models/research/ directory
    python object_detection/train.py \
        --logtostderr \
        --train_dir=${PATH_TO_TRAIN_DIR} \
        --pipeline_config_path=${PATH_TO_YOUR_PIPELINE_CONFIG}
    ```

2. Export the trained model
    After training, you can get a bunch of strange files that endwith '.ckpt' and a file named `checkpoint`, to export the trained model to a Tensorflow graph proto, run the [provided script][3]:
    ```
    # From tensorflow/models/research/
    python object_detection/export_inference_graph.py \
        --input_type image_tensor \
        --pipeline_config_path ${PIPELINE_CONFIG_PATH} \
        --trained_checkpoint_prefix ${TRAIN_PATH} \
        --output_directory output_inference_graph.pb
    ```

3. Finally you can get a graph named `output_inference_graph.pb`
4. Test the model locally using `object_detection_tutorial.py` in `tensorflow/models/research/object_detction`, you can generate the `.py` file from `.ipynb` through Jupyter notebook. Modify the following parts:
    - PATH_TO_CKPT
    - PATH_TO_LABELS
    - NUM_CLASSES
    - PATH_TO_TEST_IMAGES_DIR

    You can also set different `min_score_thresh` inside the function `vis_util.visualize_boxes_and_labels_on_image_array`.

[1]: https://github.com/offbye/tensorflow_object_detection_create_coco_tfrecord/
[2]: https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/detection_model_zoo.md
[3]: https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/exporting_models.md