---
date: Fri Nov  3 22:05:29 2017
title: "Ubuntu 16.04 + GTX 1070 + CUDA 8.0 + cuDNN 5.1"
author: xiaoyuliu
layout: post
category: blog
tag:
- ubuntu
---

Spent a whole day to figure out the way to safely install these things.

### Install GTX 1070 driver

There are two different ways to install the driver:

0. Install through `.run` file from [this link](1)

    Simply speaking:

    + Ban built-in *nouveau* driver
        ```
        Ctrl + Alt + F1
        //input your user id and password
        sudo vi /etc/modprobe.d/blacklist-nouveau.conf
        ```

        Insert `blacklist nouveau  options nouveau modset=0` and save
        
        ```
        sudo update-initramfs -u
        lspci | grep nouveau //success if return nothing
        Ctrl + Alt + F7 //return to GUI
        ```


    + Install GTX 1070 driver
        Download the corresponding `.run` file through the link above

        ```
        Ctrl + Alt + F1
        //input your user id and password
        sudo service lightdm stop
        cd to the root folder of driver
        sudo chmod 755 NVIDIA-Linux-x86_64-XXX.XX.run  //get privilege
        sudo ./NVIDIA-Linux-x86_64-XXX.XX.run --no-opengl-files
        sudo service lightdm start
        Ctrl + Alt + F7 //return to GUI
        nvidia-smi //success if return gpu information
        ```

    **However, my Ubuntu system broke down when trying this method. There came lots of strange problems like I can't use my pinyin input and my system can't locate *update* package. Therefore I chose the second way to install nvidia driver.**


1. According to [this answer](2):

    There is a compiled driver that already supports GTX 1070, which is 367.

    So we can simply using:

    ```
    sudo add-apt-repository ppa:graphics-drivers/ppa
    sudo apt-get update
    sudo apt-get install nvidia-367
    ```

    Then you need to reboot your machine and "surprisingly" the driver works now. 


### Install CUDA 8.0

- Download the corresponding `.run` file
- `sudo sh cuda_8.0.61_375.26_linux.run`
- Answer to the questions
    + accept
    + **Most important**: Install NVIDIA Accelerated Graphics Driver for Linux-x86_64 375.26? **No**, because usually the version of cuda driver will be older and we just installed newer nvidia driver
    + y
    + y
    + y
- There will be some `warning` sentences but they don't matter
- Set PATH:
    + `sudo vi /etc/profile`
    + Insert:
    ```
    export PATH=/usr/local/cuda-8.0/bin:$PATH
    export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:$LD_LIBRARY_PATH
    ```
    + Save and exit the file
    ```
    sudo ldconfig //validate PATH immediately
    sudo apt-get install freeglut3-dev build-essential libx11-dev libxmu-dev libxi-dev  libglu1-mesa libglu1-mesa-dev libgl1-mesa-glx
    cd /usr/local/cuda/samplessudo
    make all
    cd ./bin/x86_64/linux/release 
    ./deviceQuery //success if gpu information appears
    ```

    **There might be another problem when simply using `make all` here, which is one sample will not be able to be built. I'll add details and the solution later.**

### Install cuDNN 5.1

- Download the corresponding 
-  on [the official website](3), registration required
- After extracting, `cd` to the folder and run
    ```
    sudo cp cuda/include/cudnn.h /usr/local/cuda-8.0/include
    sudo cp cuda/lib64/libcudnn* /usr/local/cuda-8.0/lib64
    sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda8.0/lib64/libcudnn*
    sudo ln -sf libcudnn.so.5.1.10 libcudnn.so.5  
    sudo ln -sf libcudnn.so.5 libcudnn.so  
    sudo ldconfig 
    ```

[1]https://www.geforce.com/drivers
[2]https://askubuntu.com/questions/791439/trouble-installing-ubuntu-16-04-since-i-got-gtx-1070
[3]https://developer.nvidia.com/cuda-downloads
