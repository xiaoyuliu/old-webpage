---
title: Prepare LMDB for Caffe from custom datasets
author: xiaoyuliu
date: Fri 23 Feb 2018 01:27:09 PM PST
category: blog
layout: post
tag:
- Caffe
---

There is some scripts to create LMDB specially for MSCOCO or VOC datasets, but sometimes we need to combine two different datasets. And it would be efficient for Caffe to write both datasets into a single LMDB file.

Take the combination fo [MSCOCO][1] and [UA-DETRAC][2] as an example in this article.

0. Get the LMDB scripts

    ```sh
    cd data
    git clone https://github.com/weiliu89/coco.git
    cd coco
    git checkout dev
    cd PythonAPI
    python setup.py build_ext --inplace
    ```

1. Create folders required

    ```sh
    cd /path/to/your/dataset/folder
    mkdir Annotations
    mkdir ImageSets
    mkdir JPEGImages
    ```

2. Set up COCO and DETRAC images

    ```sh
    cd JPEGImages
    wget http://images.cocodataset.org/zips/train2017.zip
    wget http://images.cocodataset.org/zips/val2017.zip
    wget http://detrac-db.rit.albany.edu/Data/DETRAC-train-data.zip
    unzip DETRAC-train-data.zip
    unzip train2017.zip
    unzip val2017.zip
    mv Insight-MVT_Annotation_Train/* ../
    rm -r Insight-MVT_Annotation_Train
    ```

3. Set up the `JPEGImages` folder as:

    ```
    JPEGImages/MVI_20011/imgXXXXX.jpg
    JPEGImages/MVI_20012/imgXXXXX.jpg
    JPEGImages/MVI_20032/imgXXXXX.jpg
    ...
    JPEGImages/train2017/XXXXXXXXXXXX.jpg
    JPEGImages/val2017/XXXXXXXXXXXX.jpg
    ```

4. Set up COCO and DETRAC annotations

    ```sh
    cd ../Annotations/
    wget http://images.cocodataset.org/annotations/annotations_trainval2017.zip
    wget http://detrac-db.rit.albany.edu/Data/DETRAC-Train-Annotations-XML.zip
    unzip annotations_trainval2017.zip
    unzip DETRAC-Train-Annotations-XML.zip
    ```

5. Generate your dataset's label map file, I used:

    ```
        item {
          name: "none_of_the_above"
          label: 0
          display_name: "background"
        }
        item {
          name: "1"
          label: 1
          display_name: "person"
        }
        item {
          name: "2"
          label: 2
          display_name: "bicycle"
        }
        item {
          name: "3"
          label: 3
          display_name: "car"
        }
        item {
          name: "4"
          label: 4
          display_name: "motorcycle"
        }
        item {
          name: "5"
          label: 5
          display_name: "other"
        }
        item {
          name: "6"
          label: 6
          display_name: "bus"
        }
        item {
          name: "7"
          label: 7
          display_name: "train"
        }
        item {
          name: "8"
          label: 8
          display_name: "truck"
        }
        item {
          name: "9"
          label: 9
          display_name: "boat"
        }

    ```

6. Generate every image's annotation txt file, and set up the `Annotation` folder as:

    ```
    Annotation/MVI_20011.xml-anno/annoXXXXX.txt
    Annotation/MVI_20012.xml-anno/annoXXXXX.txt
    Annotation/MVI_20032.xml-anno/annoXXXXX.txt
    ...
    Annotation/train2017TXT/XXXXXXXXXXXX.txt
    Annotation/val2017TXT/XXXXXXXXXXXX.txt
    ```
    
    The content of every `.txt` file is:

        ```
        # [label] [x1] [y1] [x2] [y2]
        1 0.0 104.98 500.0 370.61
        ```
    **Refer COCO's PythonAPI for efficiently loading COCO annotations from JSON files**

7. Generate list files for training images and validation images. Set up the `ImageSets` folder as:

    ```
    ImageSets/train.txt
    ImageSets/val.txt
    ```
    The content of `train.txt` file is:

        ```
        MVI_20011/imgXXXXX.jpg
        MVI_20011/imgXXXXX.jpg
        ...
        MVI_20012/imgXXXXX.jpg
        MVI_20012/imgXXXXX.jpg
        ...
        train2017/XXXXXXXXXXXX.jpg
        ...
        ```

        **NO** `val2017` images are included in `train.txt`

    The content of `val.txt` file is:

        ```
        val2017/XXXXXXXXXXXX.jpg
        ...
        ```

        **NO** `train2017` or `MVI_200XX` images are included in `val.txt`

8. Generate `train.txt` and `val.txt` files indicating relationships between each single image and the corresponding annotation file under `/PATH/TO/CAFFE/data/coco`. The content should be like:

    ```
    # train.txt
    JPEGImages/MVI_XXXXX/imgXXXXX.jpg Annotations/MVI_XXXXX.xml-anno/annoXXXXX.txt
    ...
    JPEGImages/train2017/XXXXXXXXXXXX.jpg Annotations/train2017TXT/XXXXXXXXXXXX.txt
    ...

    # val.txt
    JPEGImages/val2017/XXXXXXXXXXXX.jpg Annotations/val2017TXT/XXXXXXXXXXXX.txt
    ...
    ```

    Similarly, **NO** `train2017` or `MVI_200XX` images are included in `val.txt` and **NO** `val2017` images are included in `train.txt`.

8. Generate LMDB

    Modify `/PATH/TO/CAFFE/data/coco/create_data.sh`:
        - root_dir: /PATH/TO/CAFFE/
        - data_root_dir: /PATH/TO/CAFFE/data/coco
        - mapfile: /PATH/TO/CAFFE/data/coco/${maplabelfile}
        - label_type: "txt"
        - `for subset in val train`
    
    Run the script and it will create lmdb files for train, val with encoded original image:

        ```
        /PATH/TO/CAFFE/data/coco/lmdb/coco_val_lmdb
        /PATH/TO/CAFFE/data/coco/lmdb/coco_train_lmdb
        ```

*Actually if only LMDB files are needed, the relationship files are enough. But sometimes some extra files are required, which might need a stable file structure.*




[1]: http://cocodataset.org/#home
[2]: http://detrac-db.rit.albany.edu/

