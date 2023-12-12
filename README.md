
# Object detection with ROI using jetson nano and yolov7

This repository contains step by step guide to build safety project on Jetson nano by converting Yolov7 into TensorRT engine

Jetson nano details:

Jetpack: 4.6

RAM: 4GB
## Pre-requisites 

Please install below libraries:

```bash
$ sudo apt-get update
$ sudo apt-get install -y liblapack-dev libblas-dev gfortran libfreetype6-dev libopenblas-base libopenmpi-dev libjpeg-dev zlib1g-dev
$ sudo apt-get install -y python3-pip
```
## Install Python packages
Please install below packages 

```bash
#If different version numpy is installed, first uninstall current version using below command and install numpy
sudo python3 -m pip uninstall numpy
python3 -m pip install numpy==1.19.0
python3 -m pip install pandas==0.22.0
python3 -m pip install Pillow==8.4.0
python3 -m pip install PyYAML==3.12
python3 -m pip install scipy==1.5.4
python3 -m pip install psutil
python3 -m pip install tqdm==4.64.1
python3 -m pip install imutils
```

## PyCuda installation

```bash
$ export PATH=/usr/local/cuda-10.2/bin${PATH:+:${PATH}}
$ export LD_LIBRARY_PATH=/usr/local/cuda-10.2/lib64:$LD_LIBRARY_PATH
$ python3 -m pip install pycuda --user
```
## Torch and Torchvision installation

```bash
$ wget https://nvidia.box.com/shared/static/fjtbno0vpo676a25cgvuqc1wty0fkkg6.whl -O torch-1.10.0-cp36-cp36m-linux_aarch64.whl
$ pip3 install torch-1.10.0-cp36-cp36m-linux_aarch64.whl
$ git clone --branch v0.11.1 https://github.com/pytorch/vision torchvision
$ cd torchvision
$ sudo python3 setup.py install 
```
## wts file generation from pt file
Yolov7-tiny.pt is included in the repository, you have the option to download a different version of the Yolov7 model. Afterward, execute the following command to convert the .pt file into a .wts file
```bash
$ python3 gen_wts.py -w yolov7-tiny.pt -o yolov7-tiny.wts
```
## Build the Makefile
Establish a 'build' directory within the yolov5 folder. Transfer the generated .wts file into the 'build' directory, and execute the provided commands below. In the case of utilizing a custom model, ensure that you update 'kNumClas' in yolov7/include/config.h

```bash
$ cd yolov7/
$ mkdir build
$ cd build
$ cp ../../yolov7-tiny.wts .
$ cmake ..
$ make 
```
## Build engine file 
$ sudo ./yolov7 -s yolov7-tiny.wts  yolov7-tiny.engine t
## Engine file testing
$ sudo ./yolov7 -d yolov7-tiny.engine ../images

## Object detection using Python & Yolov7-tiny
$ python3 odtest.py

If you're using a custom model, make sure to adjust the categories in the yolovDet.py file to match the classes you have in your model

## Safety Applications of Object detection (POC only)

The objective is to implement a person detection system within a user-defined area. The program will trigger an immediate alarm upon detecting the presence of a person within the specified region.

## Frame capturing to define area
```bash
$ python3 opencv_capture_frame.py
```
Press 'x' to cature image 

## Getting co-ordinates of the polygon/region

```bash
$ python3 opencv_co_ordinates.py
```
Add the co-oridnates into tiny-main file to define the range.

## Main file exucution with output from jetson nano
```bash
python3 tiny-main.py
```
Please refer tiny-main.py file for better understanding.
