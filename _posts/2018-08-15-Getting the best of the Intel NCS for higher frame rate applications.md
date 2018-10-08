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
