---
layout: post
title: Offline Caffe Installation on Ubuntu
subtitle: For anyone who wants to start using Caffe, than on building and debugging it.
gh-repo: AjinkyaGhadge
gh-badge:
  - star
  - fork
  - follow
tags:
  - Caffe
  - Python
  - Offline
  - Install
  - Build
  - CNN
  - Convolutional Neural Networks
  - Machine Learning
  - Deep Learning
  - Beginners
published: true
---
# Offline Caffe Installation on Ubuntu
**Follow this tutorial to install Caffe on an offline Linux machine, with Linux Distribution of Ubnutu**

**Caffe Version: 1.0**

## Hardware Requirement and Recomendation
#### [Requirement for GPU enalbled version of Caffe](https://www.nvidia.com/en-us/data-center/gpu-accelerated-applications/caffe/) ####

* `64-Bit Linux` - GPU software stack is not-available for 32-Bit linux.
* `Nvidia Cuda 7.5` - Nvidia Cuda 8.0 for Nvidia Pascal GPU.
* `cuDNN v 5.1` - cuDNN v 5.1 is needed for Caffe GPU installation.
* `Compute capablity > 3.0` - Nvidia GPU should have compute capability greater than 3.0.

#### Recomended setup for Caffe CPU deployment ####


*For CPU based caffe systems, although caffe can be compiled with any compatible linux system with available packages,following is the minimum recommended system configuration. For bigger problems, more hardware capacity can be added.*

* `4GB-8GB RAM` - GPU software stack is not-available for 32-Bit linux.
* `Intel Core i5 or better CPU` - Although some older CPU are also supported, this is recommended minimal.
* `64-bit Linux` - For better availability of packages, it is recommended to use 64-bit linux. 

#### Programs required ####


*For better installation experience, download the following softwares*

* `Ubuntu 16.04` - Download the ISO file, [click here](http://releases.ubuntu.com/16.04/).
* `Virtual Box` - Choose the correct version of Virtual Box, [click here](https://www.virtualbox.org/wiki/Downloads).

## Notation

*The installation process needs two machines, one of which needs to be connected to the internet for downloading packages. We are using the following notation for the course of the installation process to denote different machines*

* `System A` - The System on which Caffe is being Installed.
* `System B` - The System which is connected to the internet.
* `System C` - Guest Operating System hosted on host System B using Virtual Box.

## Steps to Install

**1.** Install Virtual Box on System B.

**2.** On System B, create an operating system instance of **Ubuntu 16.04** using Virtual box.
 
**3.** If there is a proxy configured on System B, exactly same network settings have to be configured for System C.

**4.** The original proxy settings can be found in System Settings **>>>** Network **>>>** Network Proxy on System B.

Note the proxy settings from here, to be used in System C.

**5.** Apply the same settings by navigating to System Settings **>>>** Network **>>>** Network Proxy in System C.

**6.** In System B, note down the contents of the file using the comand ```cat /etc/apt/apt.conf```. This informatation is useful to configure proxy for the package manager, in our case it is *apt-get*.

**7.** If a file doesnot exist, create a file in System C using ```sudo gedit /etc/apt/apt.conf``` and copy the contents noted down from system B.

**8.** On System C, run command ```sudo apt-get update```.

**9.** On System C, run command ```sudo apt-get install aptitude``` to install aptitude package manager.

**10.** Download all the packages needed for Caffe, *along with their dependencies* using the command, ```sudo aptitude --download-only -y install <package_1_name> <package_2_name> <package_n>```. 

Copy and paste the following command, to download all the packages, 

```sudo aptitude --download-only -y install libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libhdf5-serial-dev protobuf-compiler build-essential cmake git pkg-config libprotobuf-dev libleveldb-dev libsnappy-dev libhdf5-serial-dev protobuf-compiler``` 

```sudo aptitude --download-only -y install libatlas-base-dev libgflags-dev libgoogle-glog-dev liblmdb-dev python-pip python-dev python-numpy python-scipy python3-dev python3-numpy python3-scipy libopencv-dev libboost-all-dev```.

**11.** After downloading all the packages on system C, copy the packages and their dependencies from cache using the following command:
```cp /var/cache/apt/archives/*.deb /path/to/desired/location```

**12.** In System C, clone the GitHub repository of Caffe using the command: [```git clone https://github.com/BVLC/caffe.git```](https://github.com/BVLC/caffe.git)

**13.** From System C, copy the created Dependency folder and the cloned GitHub Repository to a USB drive, and copy the contents to System A.

**14.** On System A, open terminal inside the copied packages folder and run the command ```sudo dpkg -i *.deb```

**15.** On System A, inside the copied caffe directory, run the command ```cp Makefile.config.example Makefile.config```

**16.** Open Makefile.config using command ```gedit Makefile.config``` , select the parameters for configuration, set CPU/GPU mode by commenting and uncommenting, choose the default BLAS package.

**17.** Execute ```make all```

**18.** Next, execute ```make test``` and then ```run test```

## Alternative Steps

*You can skip the steps from step 8 to step 11 by executing this script, save this as a program and pass the path where you want to store the dependencies as an argument to the script*

```
#!/bin/bash
#please pass the directory as an argument
echo "This will automatically download all the dependencies for caffe in your cache and copy it to home folder"
sudo mkdir $1
sudo apt-get -y update
sudo apt-get install -y aptitude
sudo aptitude --download-only -y install libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libhdf5-serial-dev protobuf-compiler
sudo aptitude --download-only -y install libboost-all-dev
sudo aptitude --download-only -y install libatlas-base-dev libopenblas-dev 
sudo aptitude --download-only -y install build-essential cmake git pkg-config
sudo aptitude --download-only -y install libprotobuf-dev libleveldb-dev libsnappy-dev libhdf5-serial-dev protobuf-compiler
sudo aptitude --download-only -y install libgflags-dev libgoogle-glog-dev liblmdb-dev
sudo aptitude --download-only -y install python-pip python-dev python-numpy python-scipy python3-dev python3-numpy python3-scipy
sudo aptitude --download-only -y install libopencv-dev kate
sudo cp /var/cache/apt/archives/*.deb $1
```
> If you have named the script autodownload.sh, the command would be ```autodownload <path-to-store-packages>```

####Note: 
>Sometimes you might have to make the script explicitly executable using the command ```chmod +x <scriptname>```

Continue the furtur steps as mentioned in Steps to Install.

## Common errors

**1. Caffe didn't see hdf5.h when compiling**

In your Makefile.config try to append /usr/include/hdf5/serial/ to INCLUDE_DIRS:

>--- INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include
>+++ INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include /usr/include/hdf5/serial/

and rename *hdf5_hl* and *hdf5 to hdf5_serial_hl* and *hdf5_serial* in the Makefile:

>--- LIBRARIES += glog gflags protobuf boost_system boost_filesystem m hdf5_hl hdf5
>+++ LIBRARIES += glog gflags protobuf boost_system boost_filesystem m hdf5_serial_hl hdf5_serial

**2. HDF5 issues**

**Problem:**
>src/caffe/net.cpp:8:18: fatal error: hdf5.h: No such file or directory
>compilation terminated.
>Makefile:581: recipe for target '.build_release/src/caffe/net.o' failed
>make: *** [.build_release/src/caffe/net.o] Error 1

**or**

>AR -o .build_release/lib/libcaffe.a
>LD -o .build_release/lib/libcaffe.so.1.0.0
>/usr/bin/ld: cannot find -lhdf5_hl
>/usr/bin/ld: cannot find -lhdf5
>collect2: error: ld returned 1 exit status
>Makefile:572: recipe for target '.build_release/lib/libcaffe.so.1.0.0' failed
>make: *** [.build_release/lib/libcaffe.so.1.0.0] Error 1

**Solution**

* install libhdf5-dev

* open Makefile.config, locate line containing LIBRARY_DIRS and append /usr/lib/x86_64-linux-gnu/hdf5/serial

* locate INCLUDE_DIRS and append /usr/include/hdf5/serial/

* rerun make all

**3. opencv_imgcodecs opencv_videoio issues:**

**Problem**

>/usr/bin/ld: cannot find -lopencv_imgcodecs
>/usr/bin/ld: cannot find -lopencv_videoio
>collect2: error: ld returned 1 exit status
>Makefile:579: recipe for target '.build_release/lib/libcaffe.so.1.0.0-rc5' failed
>make: *** [.build_release/lib/libcaffe.so.1.0.0-rc5] Error 1```

**solution:** open your Makefile with some text editor, locate line 164 (in my case), add opencv_imgcodecs below it.

>LIBRARIES += glog gflags protobuf leveldb snappy \
>  lmdb boost_system hdf5_hl hdf5 m \
>  opencv_core opencv_highgui opencv_imgproc opencv_imgcodecs
 

