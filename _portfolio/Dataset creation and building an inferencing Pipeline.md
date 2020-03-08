---
title: "Scripting a dataset from terabytes of raw data"
excerpt: "Raw image data to a nice workable dataset, one stop script"
header:
  teaser: assets/images/datasetcreation.png
---

# Context

This explanation is about a aerial imagery dataset taken using both IR and Visual Camera. A good way to think of it is the view of DJI mavick drone at its highest altitude. While a lot of things can be planned ahead of time like time of day, weather suitability, choice of geography. There are still a lot of variables that are encountered while the real shoot takes place, like glare in images and synchronisation of the two cameras. This project is about creating the dataset from raw data and using scripting to automate some of the tasks like removing glared images, synchronization of frames from two cameras, adjusting aspect ratios across a dataset. 

# How does the raw data look like?

Quite huge, both the Infrared camera and the RGB camera were high speed. Which means greater than 300 frames per second. Each of the camera output was a raw uncompressed image of "tiff" format. These two aspects made the data humongous, greater than 100 terabytes.

# Design questions faced when the data

## How much compression is suitable for the task?

* Compression without loss of features (edges, contours, shapes, etc), that will have no effect on the accuraccy of the Machine learning algorithm that uses the data is a good compression.
* Real-time performance starts at 24 frames per second, and high speed cameras would not really add much valuable information by virtue of more number of frames. So frame rate can also be compressed if it doesn't break the 24 FPS threshold

## Do we need same aspect ratio from both the cameras?

* Yes, in this case if same aspect ratio could be very useful. It can allow for transfer learning from RGB camera to IR camera when using Neural Networks

* Wide angle camera will have more features but also more noise. If the object in focus is important, image can be cropped to maintain aspect ratio

## Computationally, how expensive would these transformations be for the large dataset, and how do we test if the script works?

* Profile each of the transformations for one image, and multiply it with the size of the raw data. This yields an approximate time it would take. This helps remove any transformations that may take several weeks
* Test it on a small subset, verify results manually

## What are some of the impurities(noise) in the image stream, that should be handled?

* No blurr due to high speed cameras.
* Glare present for certain angles and some drone maneuvers.

# Compression

## JPEG vs PNG

* JPEG format is more suitable for natural images. This is due to natural images containing smoother edges which makes loss due to [discrete cosine transform](https://en.m.wikipedia.org/wiki/Discrete_cosine_transform), immaterial. 
* JPEG compression has negligible impact on quality for performance of vision algorithms as discussed in [this](https://arxiv.org/abs/1604.04004) study.
* A compression ratio of 10:1 is a very solid ratio, that would be useful in reducing the dataset size and making it workable. 

## Reducing frame-rate

* Reducing frame rate from 300 frames per second to 70 frames per second, gives a compression ratio of 4:1, which is a enough to reduce the dataset down to a few hundred giga bytes.

## Cropping image to maintain aspect ratio

* The RGB camera was a wide angle camera and hence cropping equally from the left and right to match the IR camera reduced each image by almost 40 percent

# Handling issues 

## Automated glare detection

* Use Binary thresholding to find glare spots
* Linear search for glare would be very slow. Check for glare by maintaining a skipping distance capped to a maximum of 350 frames, increase skipping distance only if no glare is found.

## Cropping

* Had to be done linearly for each of the frame, using opencv

# Creating dataset

## Normalization

* Subtract dataset mean image
* Easier to do once the compressions were completed

## Annotation

* Used Matlab image labeler

# Putting it all together

The script has the knobs, turn on and off things like binary thresolding by setting flags. Make a function for each of the task and make it super modular for flexibility. 