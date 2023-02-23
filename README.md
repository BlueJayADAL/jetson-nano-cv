# Computer Vision Setup Guide for Jetson Nano

## Target Platform Info
+ Module: NVIDIA Jetson Nano (4GB RAM)
+ SoC: tegra210
+ CPU Architecture: aarch64
+ OS: Ubuntu 18.04 LTS
+ Python: 3.6.9
+ CUDA: 10.2.300
+ cuDNN: 8.2.1.32
+ TensorRT: 8.2.1.8
+ OpenCV: 4.6.0 with CUDA enabled
+ L4T: 32.7.1
+ Jetpack: 4.6.1 

## Install OpenCV 4.6.0 with CUDA Enabled
Follow the guide [here](https://qengineering.eu/install-opencv-4.5-on-jetson-nano.html) that covers:
+ Install system debugging tool **JTOP**
  
    ```
    $ sudo -H pip install -U jetson-stats
    $ sudo reboot
    # start the app with the simple command
    $ jtop
    ```
+ Changing the desktop GUI manager from gdm3 to **lightdm**.
+ Overclocking the ARM CPU (not necessary)
+ Enlarging the memory SWAP space (recommended)
+ Installing OpenCV 4.6.0 with an easy script

## Install PyTorch
PyTorch can be installed either by wheel or from source. We choose to use the wheel.

Find a version of PyTorch that matches with your Python version from [here](https://qengineering.eu/install-pytorch-on-jetson-nano.html). For instance, Python3.6 support PyTorch 1.8, 1.9 and 1.10

## Install PyTorch Vision
Torchvision is a collection of frequent used datasets, architectures and image algorithms. The installation is simple when you use one of our wheels found on GitHub. Torchvision assumes PyTorch is installed on your machine on the forehand.

Find a version of PyTorch that matches with your Python version from [here](https://qengineering.eu/install-pytorch-on-jetson-nano.html). You need to scroll down to around the middle of the page. 

Alternatively, you can install from source

```
git clone — branch v0.9.1 https://github.com/pytorch/vision torchvision
cd torchvision/
sudo python3 setup.py install
```

## Install Tensorflow

Now install Tensorflow 2.4.1 by following this [article](https://qengineering.eu/install-tensorflow-2.4.0-on-jetson-nano.html). If gdown command (used to download tf2.4.1.whl file) doesn’t work, then simply copy the drive link from the steps and download the file yourself.

## Install Darknet for Yolov4

Follow this [guide](https://qengineering.eu/install-darknet-on-jetson-nano.html).

You can test Darknet with the following Python command when a webcam is connected to your Jetson Nano.
+ Sample command:

    ```
    # darknet location
    $ cd ~/darknet
    # download yolov4 tiny weight file
    $ wget https://github.com/AlexeyAB/darknet/releases/download/darknet_yolo_v4_pre/yolov4-tiny.weights --no-check-certificate
    # run with webcam
    $ ./darknet detector demo cfg/coco.data cfg/yolov4-tiny.cfg yolov4-tiny.weights "v4l2src ! video/x-raw, framerate=30/1, width=640, height=360 ! videoconvert ! appsink" -ext_output
    ```

## Install Yolov5
1. Clone the repo and move inside the cloned folder: 
```
git clone https://github.com/ultralytics/yolov5.git
cd yolov5
export OPENBLAS_CORETYPE=ARMV8
```
2. Now type `nano requirements.txt` in terminal and comment out these packages as these are already installed -> `numpy`, `tensorflow`, `torch`, `torchvision` and `opencv`.

3. Now install the remaining libraries using the following command. This process may take time as many of these libraries are downloaded and built locally on the jetson.
```
pip3 install -r requirements.txt
```

4. After installing, download Yolov5s model from the repository, place it in the yolov5 directory and run the following. If the following commands throws error, kindly look for missing dependencies. `source 0` means you are using Webcam via USB such as Logitech C270(HD) webcam. The Raspi camera IMX219 unfortunately is not supported, as the GStreamer tool is not supported by Yolov5. Change the source to path of image or video if you don’t want to use live feed. `device 0` means that the model should be loaded in GPU.
```
python3 detect.py --weights yolov5s.pt --source 0 --device 0
```
5. Do checkout detect.py for more options while running inference on YoloV5s model (Yolov5s is a light weight model)
6. Every time you run yolov5’s detect.py, the output is saved. Remember to delete the output if not needed else it will eat up space.