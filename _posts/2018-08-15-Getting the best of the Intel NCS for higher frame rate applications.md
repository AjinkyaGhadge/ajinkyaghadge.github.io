---
layout: post
title: Getting the best of the Intel NCS for higher frame rate applications
subtitle: Exploring suitable architectures for working with Intel NCS !
gh-repo: AjinkyaGhadge
gh-badge:
  - star
  - fork
  - follow
tags:
  - Intel
  - Movidius
  - NCS
  - Deep Learning
  - CNN
  - Real-Time
  - Frame-Rate
  - VPU
  
published: true
---
# Getting the best of the Intel NCS for higher frame rate applications

A few years ago, the hype was all about the Internet of Things and Cloud Computing. In a way it still is, data collected from myraid tiny sensors is analyzed mostly in the cloud. For all the leverage that it offers, cloud does suffer if there is poor network infrastructure. This means, often applications running on edge-devices need large bandwidth and minimal turnaround time to work in real-time situations. In its spree of acquiring startups, Intel acquired Movidius, which pioneered VPU, Vision Processing Unit, targeted towards dedicated hardware for vision and deep learning inference.

Today, the trend is cloud and the edge will work in tandem. Nvidia is leading the edge with its incredible Jetson TX2(I will be writing a lot on this soon)

The Intel Movidius Neural Compute Stick is an awesome tool, it's key highlight is that despite consuming only ~1W of Power, it is better at inference of deep learning models than most CPU(s). GPU are still ahead though. The tricky part here is, image stream applications often need to be processed at ~20 Frames/second. The NCS flounders with most CNN architectures. But there are ways of exploiting this pocket monster.

How to get the best frame-rates on the NCS?

To start with, classification problems are computationally less intensive than detection. Many modern CNN architectures are optimizing detection and classification(Yolo, SSD). [https://arxiv.org/pdf/1611.10012.pdf](This) paper by Google ofers an apples to apples comparison of detection algorithms. Here is a glimpse of what the paper has to say.

Yolo and SSD are pretty good when it comes to frame-rates.

![Frame-Rate highest and Lowest Reported](https://cdn-images-1.medium.com/max/1250/1*EQKFp_c6jMYcDZbIwbEOzA.png)

MobilNet has particularly low memory consumption too.

![Frame-Rate highest and Lowest Reported](https://cdn-images-1.medium.com/max/1000/1*yFgDeWPB68YncH4KrQL2eQ.png)

An edge device like NCS is limited by Memory, and also has limted compute as compared to GPU(s). In case of classification problems, i obtained very good results by using optimized architectures and using OpenVino. 

### Here are a few Ideas:
1. Use architectures like SqueezeNet adn SqueezeNext for image classification tasks.
2. For detection, prefer SSD-MobileNet, YoloV3
3. Keep the number of classes low, especially for object detection.
4. The initial image size of the network should be small, basically use smaller resolution images.
5. Finally use hardware optimizations like OpenVino in case of NCS.(Like TensorRT in Jetson)

A caveat of all this is that often the mAP, mean accuracy and precision of detection is lesser than detection architectures like R-FCN and Faster-RCNN. The accuracy of SqueezeNet and SqueezeNext is also comparable only with AlexNet, and not with more robust CNNs like ResNet and GoogLeNet.

![mAP issues](https://cdn-images-1.medium.com/max/1250/1*GD5Th5eZBMufVFJQjo_5mA.png)

At the time of writing this, i am working on a paper involving comprehensive analysis of frames/sec/watt for the Intel NCS and Jetson TX2, will update this soon.
