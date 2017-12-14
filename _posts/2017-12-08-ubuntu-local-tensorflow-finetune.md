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

0. #### Prepare dataset 

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
    if len(labels) == 0:
        continue
    ```

    Follow the instruction of installation and running from the repo. Then you can get the `train.record` and `eval.record`. For training, I produced only the `train.record`.

1. #### Train the model

    0. Download a pre-trained object detection model from [detection_model_zoo][2] or a classification model from [classification_model_zoo][4]
    1. Create an object detection training pipeline.config file: I modify one from `/path/to/pretrainedModels/faster_rcnn_resnet50_lowproposals_coco_2017_11_08/pipeline.config`, simply changing `num_classes`, `fine_tune_checkpoint`, `num_steps`, `label_map_path` and `tf_record_input_reader/input_path`.
    2. Set the correct value for **from_detection_checkpoint**. If you are fine-tuning from a pre-trained object detection model, set it to *true*; if from a classification pre-trained model, set it to *false*
    3. Use the following command to train
    ```
    # From the tensorflow/models/research/ directory
    python object_detection/train.py \
        --logtostderr \
        --train_dir=${PATH_TO_TRAIN_DIR} \
        --pipeline_config_path=${PATH_TO_YOUR_PIPELINE_CONFIG}
    ```

2. #### Export the trained model

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
4. **Test the model locally** using `object_detection_tutorial.py` in `tensorflow/models/research/object_detction`, you can generate the `.py` file from `.ipynb` through Jupyter notebook. Modify the following parts:
    - PATH_TO_CKPT
    - PATH_TO_LABELS
    - NUM_CLASSES
    - PATH_TO_TEST_IMAGES_DIR

    You can also set different `min_score_thresh` inside the function `vis_util.visualize_boxes_and_labels_on_image_array`.

5. In order to train the model using **multiple GPUs**:
    0. Specify the ids of GPUs you want to assign by adding `CUDA_VISIBLE_DEVICES=id1,id2,id3,...` at the beginning of training command. This is in case of random GPU assignment by Tensorflow.
    1. Modify `batch_size: GPU_NUMBER` in `pipeline.config` file
    2. Add flag `--num_clones=GPU_NUMBER --ps_tasks=1` at then end of training command
6. #### Remote Tensorboard
    
    0. Run Tensorboard on the server by `tensorboard --logdir=${PATH_TO_MODEL_DIRECTORY}`
    1. Train the model on the server and view the training process by tensorboard locally using:
    ```
    ssh -L 16006:127.0.0.1:6006 -p PORT SERVER_NAME@SERVER_ADDRESS
    ```
    Then open http://127.0.0.1:16006/ on the local machine, fantastic!
    
7. #### Different Model Saving Strategy
    
    During the training procedure, the operation of saving a model is wrapped in Tensorflow official scripts, and the training api only provides a parameter related to saving the model `save_interval_secs: How often, in seconds, to save the model to 'logdir'`. I cannot achieve the target to save the model every *N* iterations, and can only save the model every *N* seconds. Here is how to do it:
    - `/path/to/tensorflow/models/research/object_detection/trainer.py: slim.learning.train`, add `save_interval_secs=WHATEVER_YOU_WANT`. The default value is 600, which means every 10 minutes a model will be saved
    - `/path/to/tensorflow/models/research/object_detection/trainer.py: f.train.Saver`, add `max_to_keep=WHATEVER_YOU_WANT`. The default value is 5, which means only the latest 5 checking points(trained models) will be saved
    - `pipeline.config: eval_config`, set `max_evals=WHATEVER_YOU_WANT(max_to_keep)`. The default value is 10, which means the evaluation script will evaluate only the first 10 checking points and exits.

[1]: https://github.com/offbye/tensorflow_object_detection_create_coco_tfrecord/
[2]: https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/detection_model_zoo.md
[3]: https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/exporting_models.md
[4]: https://github.com/tensorflow/models/tree/master/research/slim