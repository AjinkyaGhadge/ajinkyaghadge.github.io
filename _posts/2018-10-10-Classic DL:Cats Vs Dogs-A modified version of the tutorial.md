---
layout: post
title: Classic DL:Cats Vs Dogs-A modified version of the tutorial
subtitle: My presentation of a Cats vs Dogs tutorial.
gh-repo: 
gh-badge:
  - star
  - fork
  - follow
tags:
  - test
published: true
---

# Creating a Neural Network from Scratch using <span style="color:maroon">**Caffe**</span>

#### <span style="color:#3e399e">Caffe is a deep learning framework made with expression, speed, and modularity in mind. 
    
   Developed by Berkeley AI Research (BAIR) and by community contributors.</span>

## About <span style="color:maroon">**Caffe**</span>

1. Caffe stands for <span style="color:#9e5439">C</span>onvolutional <span style="color:#9e5439">A</span>rchitecture <span style="color:#9e5439">F</span>or <span style="color:#9e5439">F</span>eature <span style="color:#9e5439">E</span>xtraction.

2. 600+ Citations, 100+ contributors 

3. Focus on Vision problems

4. Fast, well tested Code

5. Pure C++/CUDA architecture for deep learning

6. Provides a lot of reference models

## <span style="color:#9e8639">Four</span> steps for training a CNN using <span style="color:maroon">**Caffe**</span>

* Step 1 - Data preparation

* Step 2 - Model definition: Construct a CNN architecture and we define its parameters in a configuration file with extension .prototxt

* Step 3 - Solver definition: The solver is responsible for model optimization. We define the solver parameters in a configuration file with extension .prototxt

* Step 4 - Model training: Train the model by executing one Caffe command from the terminal. After training the model, we will get the trained model in a file with extension .caffemodel

After the training phase, we will ```use the .caffemodel trained model to make predictions``` of new unseen data.

## Step 1 : <span style="color:#3e399e">Data Preparation </span>

###  <span style="color:#9e8639">Essential Concepts</span>

#### Blobs : N Dimensional arrays for storing and communicating data
    
    1. Holds data, derivatives and parameters 
    2. lazily allocate data
    3. Shuttle between CPU and GPU

## Step 1 : <span style="color:#3e399e">Data Preparation </span>

###  <span style="color:#9e8639">The Need for data preparation</span>

1. <span style="color:#9e5439">File I/O should not be a bottleneck for otherwise high performance Caffe framework.</span>
2. Resizing : Change an aspect-ratio
    - Flowers and animals might be ok with a little stretching or squeezing, but facial features distort.
3. Rescaling and Cropping 
4. Flipping between RGB and BGR 
    - Caffe uses BGR
5. Caffe prefers CHW(C: Channel (color), H: Height, W: Width)

#### LMDB (<span style="color:red">L</span>ightning <span style="color:red">M</span>emory-Mapped <span style="color:red">D</span>ata<span style="color:red">b</span>ase)

    - Software library that provides a high-performance embedded transactional database in the form of a key-value store.
    
    - lmdb will be a huge file on your computer once you finished data preparation. You can query any file from it using a scripting language
    
    - The efficiency of its design and small footprint had the unintended side-effect of providing good write performance

## Step 1 : <span style="color:#3e399e">Data Preparation </span>

###  <span style="color:#9e8639">Other data ingest formats</span>

1. <span style="color:#9e5439">LMDB</span>
2. LevelDB
3. HDF5
4. In-memory(C++ and Python)

### 1. Image Preprocessing and Database Creation

In the first of many preparation steps, we have to preprocess our images so that they are all the same size and contrast enhanced via histogram equalization. Further preprocessing steps can be done, but we'll keep it simple for now.

After each image is preprocessed, we place it into an LMDB (the kind of database that caffe uses). We are going to randomly assign the image to either the training database or the validation database. 80% of the images will be placed into the training database, and 20% into the validation database. Note that images will be stored in the B,G,R format instead of the R,G,B. The reason for this is to stay consistent with OpenCV, and other architectures such as CaffeNet.

Note that we also need to calculate an image mean for all images in the training database. To acheive this, each preprocessed image is accumumated into "mean_img" and later divided by the number of images in the training database.

### <span style="color:#9e8639">Importing essential libraries</span>


```python
import warnings
warnings.filterwarnings('ignore')
```


```python
import cv2
import os
import glob
import random
import numpy as np
import lmdb
import pdb 
import sys
import caffe
from caffe.proto import caffe_pb2
from matplotlib import pyplot as plt
%matplotlib inline
```

#### <span style="color:#9e8639">Next, define some constants and process the mean image. Also get a list of all the images in the data folder, and shuffle the list randomly.</span>


```python
#as part of the preprocessing, resize all images to this size
IMAGE_WIDTH = 227
IMAGE_HEIGHT = 227
```


```python
#ratio of training set size to validation set size. for every NUM_SKIP images, that image is 
#placed into the validation set. for example if NUM_SKIP = 5, then the data will be split into
#4/5 training and 1/5 validation
NUM_SKIP = 5
```


```python
#accumulate each image in an array to calculate the mean image. also keep a count of the number
#of images accumulated. not really necessary, as we know the total number of images and the ratio
#of images in the training set, but just to be safe...
mean_img = np.zeros(shape=(IMAGE_HEIGHT, IMAGE_WIDTH, 3))
mean_img_count = 0.
```


```python
#get a list of all the image paths and randomly shuffle
image_paths = glob.glob('data/train/*.jpg')
random.shuffle(image_paths)
```

### <span style="color:#9e8639">A Quick look at Numpy : Powerful N-dimmensional Array Object</span>

- Core library for scientific computing in Python. 
- Provides high-performance multidimensional array object
- Provides a lot of tools for working with these arrays


```python
import numpy as temp_np
import matplotlib.pylab as temp_plotlib
array = temp_np.array([[1,2,3,4],[5,6,7,8],[9,10,11,12]])
print(array)
image1 = temp_plotlib.imread('dog1.jpg')
array = image1
print (array.shape)
print("----------------------------------------------------------------")
print(array)
```

    [[ 1  2  3  4]
     [ 5  6  7  8]
     [ 9 10 11 12]]
    (333, 282, 3)
    ----------------------------------------------------------------
    [[[242 242 242]
      [242 242 242]
      [242 242 242]
      ...
      [234 234 234]
      [233 233 233]
      [233 233 233]]
    
     [[242 242 242]
      [242 242 242]
      [242 242 242]
      ...
      [235 235 235]
      [234 234 234]
      [234 234 234]]
    
     [[242 242 242]
      [242 242 242]
      [242 242 242]
      ...
      [235 235 235]
      [235 235 235]
      [235 235 235]]
    
     ...
    
     [[207 206 204]
      [207 206 204]
      [208 207 205]
      ...
      [208 207 205]
      [208 207 205]
      [208 207 205]]
    
     [[207 206 204]
      [207 206 204]
      [208 207 205]
      ...
      [207 206 204]
      [207 206 204]
      [207 206 204]]
    
     [[207 206 204]
      [208 207 205]
      [208 207 205]
      ...
      [205 204 202]
      [204 203 201]
      [204 203 201]]]


#### <span style="color:#9e8639">We Define a function to do histogram equalization and resize the image to the right size</span>


```python
def transform_img(img):
    #do histogram equalization to account for contrast. this function expects a grayscale image
    #so do each RGB component separately
    img[:,:,0] = cv2.equalizeHist(img[:,:,0])
    img[:,:,1] = cv2.equalizeHist(img[:,:,1])
    img[:,:,2] = cv2.equalizeHist(img[:,:,2])

    #resize the image
    #TODO: there are many potential complications with resizing an image, and also different approach
    #is better for upscaling or downscaling an image. this can definitly be improved.
    img = cv2.resize(img, (IMAGE_WIDTH, IMAGE_HEIGHT), interpolation = cv2.INTER_CUBIC)

    return img
```

### <span style="color:#9e8639">Define a function to prepare a database entry</span>


```python
def make_datum(img, label):
    #image is numpy.ndarray format. note that rollaxis changes the dimension from (x,y,3) to (3,x,y)
    return caffe_pb2.Datum(channels=3, width=IMAGE_WIDTH, height=IMAGE_HEIGHT, label=label, data=np.rollaxis(img, 2).tostring())
```

### <span style="color:#9e8639">Create the training database. note that map_size is the maximum size allowed for the database</span>


```python
#define the databases that will be used to store the training and validation data
training_lmdb = 'training_lmdb'

#delete the databases if it already exists
os.system('rm -rf  ' + training_lmdb)

print('compiling training database...')
cat_count = 0
dog_count = 0
training_db = lmdb.open(training_lmdb, map_size=int(1e12))#Hex for 7698
```

    compiling training database...



```python
with training_db.begin(write=True) as in_txn:
    #loop over all images
    for ind, img_path in enumerate(image_paths):
        #skip every NUM_SKIP images
        if ind % NUM_SKIP == 0:
            continue

        #load the image and apply preprocessing steps	
        #NOTE: opencv imread loads an image as BGR order
        img = cv2.imread(img_path, cv2.IMREAD_COLOR)
        img = transform_img(img)
        mean_img = mean_img + img
        mean_img_count = mean_img_count + 1
        
        #apply the appropriate class
        if 'cat' in img_path:
            label = 0
            cat_count = cat_count + 1
        else:
            label = 1
            dog_count = dog_count + 1

        #create a datum which is basically just a container for the data and its label
        datum = make_datum(img, label)
        entry_name = ('%05d') % ind  #format this as a 5 field digit with leading zeroes
        in_txn.put(entry_name.encode(), datum.SerializeToString())
        #print entry_name + ': ' + img_path
training_db.close()
```


```python
print(('%i cats, %i dogs') % (cat_count, dog_count))
```

    9969 cats, 10031 dogs


### <span style="color:#9e8639">Create the validation database</span>


```python
#define the databases that will be used to store the training and validation data
validation_lmdb = 'validation_lmdb'

#delete the databases if it already exists
os.system('rm -rf  ' + validation_lmdb)

print('compiling validation database...')
cat_count = 0
dog_count = 0
validation_lmdb = lmdb.open(validation_lmdb, map_size=int(1e12))
```

    compiling validation database...



```python
with validation_lmdb.begin(write=True) as in_txn:
    #loop over all images
    for ind, img_path in enumerate(image_paths):
        #only go forward if this is the NUM_SKIPth image
        if ind % NUM_SKIP != 0:
            continue

        #load the image and apply preprocessing steps	
        #NOTE: opencv imread loads an image as BGR order
        img = cv2.imread(img_path, cv2.IMREAD_COLOR)
        img = transform_img(img)
        
        #apply the appropriate class
        if 'cat' in img_path:
            label = 0
            cat_count = cat_count + 1
        else:
            label = 1
            dog_count = dog_count + 1

        #create a datum which is basically just a container for the data and its label
        datum = make_datum(img, label)
        entry_name = ('%05d') % ind  #format this as a 5 field digit with leading zeroes
        in_txn.put(entry_name.encode(), datum.SerializeToString())
        #print entry_name + ': ' + img_path
validation_lmdb.close()
print(('%i cats, %i dogs') % (cat_count, dog_count))
```

    2531 cats, 2469 dogs


#### <span style="color:#9e8639">Get the mean image by diving by the total number of images. Before doing the division, make sure that the number isn't overflowed (which is unlikely).</span>


```python
if np.max(mean_img) > sys.float_info.max:
    sys.exit('mean image overflow')
    
print(('number of images in training dataset: %i') % (mean_img_count))
mean_img = mean_img/mean_img_count

#mean_img is already in BRG order so just save it as is
np.save('mean_img.npy', mean_img)
```

    number of images in training dataset: 20000


### Writing the <span style="color:#3e399e">mean.binaryproto</span>


```python
#load the npy mean image and reshape the image such that it is (1,3,x,y) instead of (x,y,3)
#this is the required shape for the array_to_blobproto function called next
mean_img = np.load('mean_img.npy')
mean_img = mean_img.reshape([1,3,227,227])

#now make a prototxt file
blob = caffe.io.array_to_blobproto(mean_img)
with open('mean.binaryproto', 'wb') as f:
    f.write(blob.SerializeToString())
```

## Creating LMDB using script

<span style="color:#9e5439">Caffe</span> has a tool convert_imageset to help you build lmdb from a set of images. Once you build your Caffe, the binary will be under /build/tools. There’s also a bash script under /caffe/examples/imagenet that shows how to use convert_imageset.
### create_lmdb.py script does the following : 

1. Histogram equalization on all training images. Histogram equalization is a technique for adjusting the contrast of images

2. Resize all training images to a 227x227 format

3. Divide the training data into 2 sets: One for training (5/6 of images) and the other for validation (1/6 of images). The training set is used to train the model, and the validation set is used to calculate the accuracy of the model

4. Store the training and validation in 2 LMDB databases. train_lmdb for training the model and validation_lmbd for model evaluation

The correct way to do this is to actually use the caffe tool compute_image_mean on the training lmdb database. However, this requires you to set USE_OPENCV:=1 in the makefile, which for whatever reason causes my Mac running OSX Sierra to just crash into oblivion :(

So as a workaround, let's load up the mean file that we calcluated when we were making the databases, which was stored as an npy file. Load it up here and convert it to a binaryproto file, which is the format that caffe expects.

## Step 2: <span style="color:#3e399e">Model Definition</span>

###  <span style="color:#9e8639">Essential Concepts</span>

#### Layers : Caffe's fundamental unit of computation

Caffe implements layers for rapid creation of neural network architectures

Implemented as layers:

1. Data access
2. Convolution
3. Pooling 
4. Activation Function (ReLU, Sigmoid, Tanh and more)
5. Loss Function (Classification: Softmax, Hinge loss, Regression: Euclidean loss)
6. Dropout


#### Protocol Buffers : think XML, but smaller, faster and simpler.

Protocol buffers are Google's language-neutral, platform-neutral, extensible mechanism for serializing structured data


>message Person {   
>  required string name = 1;
>  required int32 id = 2;
>  optional string email = 3;   
>}    



#### Protocol Buffers : .proto vs .prototxt

1. The .proto file is used to describe the structure (the 'protocol') of the data to be serialized. The protobuf compiler can turn this file into python/or C++/or Java code to serialize and deserialize data with that structure


2. For the .prototxt file. Looking at the documentation here, we can see that, there are two different formats for serialized data (textual or binary). The text format is human-readable and modifiable (and the corresponding files usually have the extension .prototxt), but it takes up a lot more space than the binary format.

- Hence, the definition of messages is stored in a “.proto” file. 
- In Caffe, it resides in Caffe source codes.
- The messages can be in two different formats. One is binary and the other is text. 
- Binary format (.caffemodel) is used for weight blobs (weight matrices) in Caffe; Text format (.prototxt) is used to define network structures.

This is a particularly tedious step involvings lots of specific configurations. It is best to first take a look through the different kinds of layers that caffe offers, and their default parameters: http://caffe.berkeleyvision.org/tutorial/layers.html

In this tutorial, I will be reproducing the CaffeNet architecture, which utilizes the following layers (in no particular order): Data, Convolution, Fully Connected, Max Pooling, Local Response Normalization (LRN), Rectified-Linear (ReLU), Dropout, Accuracy, and Softmax (with and without loss). As a reference, the CaffeNet architecture can be located in the caffe/models/bvlc_reference_caffenet/ directory.

There are three main functions that will define the training, validation, and deployment architectures separately and output corresponding prototxt files. They are mostly all the same, execept for slight differences in the data layers and last output layers. To be more concrete:  
-Validation Net vs Training Net: The data layer for the train net randomly mirrors some of the images (to generalize the model), the validation net does no mirroring. The validation net has an accuracy layer to measure the accuracy of the model, whereas we are not interested in that for training.   
-Validation and Train Net vs Deploy Net: The validation and train nets have a data layer that look for a database (the validation and training databases, respectively). In the deploy net, we specify an input layer instead (which will basically be an image). Also, in the deploy net the loss layer is replaced with a softmax layer since we don't have the true label in this case.

To make things cleaner and easier to read, some "helper functions" are defined to create new layers. It just helps reduce clutter.


Start out by importing a few more things from caffe


```python
from caffe import layers as L, params as P, to_proto
```

Next, define the "helper functions", which are basically layer definitions


```python
#for conv_layer, note that lr_mult and decay_mult are multipliers to the learning rate and decay rate. there are two sets of values, one for the weights
#and the other for the bias terms
def conv_layer(bottom, ks, nout, stride=1, pad=0, group=1, weight_filler=dict(type='constant', value=0.0), bias_filler=dict(type='constant', value=0.0), param=[{'lr_mult':1.0, 'decay_mult':1.0}, {'lr_mult':1.0, 'decay_mult':1.0}]):
    conv = L.Convolution(bottom, kernel_size=ks, stride=stride, num_output=nout, pad=pad, group=group, weight_filler=weight_filler, bias_filler=bias_filler, param=param)
    return conv

def fc_layer(bottom, nout, weight_filler=dict(type='constant', value=0.0), bias_filler=dict(type='constant', value=0.0), param=[{'lr_mult':1.0, 'decay_mult':1.0}, {'lr_mult':1.0, 'decay_mult':1.0}]):
    fc = L.InnerProduct(bottom, num_output=nout, weight_filler=weight_filler, bias_filler=bias_filler, param=param)
    return fc

def max_pool_layer(bottom, ks, stride=1):
    return L.Pooling(bottom, pool=P.Pooling.MAX, kernel_size=ks, stride=stride)

def LRN_layer(bottom, local_size, alpha, beta):
    return L.LRN(bottom, local_size=local_size, alpha=alpha, beta=beta)

def relu_layer(bottom):
    return L.ReLU(bottom, in_place=True)

def dropout_layer(bottom, dropout_ratio):
    return L.Dropout(bottom, dropout_ratio=dropout_ratio, in_place=True)

def accuracy_layer(bottom, label):
    return L.Accuracy(bottom, label)

def softmaxwithloss_layer(bottom, label):
    return L.SoftmaxWithLoss(bottom, label)	

def softmax_layer(bottom):
    return L.Softmax(bottom)
```

Now define the function that will generate the CaffeNet training and validation architecture, and return it in the prototxt format. This architecture will be used to measure the accuracy after a certain number of training iterations. 


```python
def caffenet_train(lmdb, batch_size, mirror):
    ns = caffe.NetSpec()
    ns.data, ns.label = L.Data(source=lmdb, backend=P.Data.LMDB, batch_size=batch_size, ntop=2, transform_param=dict(crop_size=227, mean_file='mean.binaryproto', mirror=mirror))

    ns.conv1 = conv_layer(bottom=ns.data, ks=11, nout=96, stride=4, weight_filler=dict(type='gaussian', std=0.01), bias_filler=dict(type='constant', value=0), param=[{'lr_mult':1, 'decay_mult':1}, {'lr_mult':2, 'decay_mult':0}])
    ns.relu1 = relu_layer(bottom=ns.conv1)
    ns.pool1 = max_pool_layer(bottom=ns.relu1, ks=3, stride=2)
    ns.norm1 = LRN_layer(bottom=ns.pool1, local_size=5, alpha=1e-4, beta=0.75)
    ns.conv2 = conv_layer(bottom=ns.norm1, ks=5, nout=256, pad=2, group=2, weight_filler=dict(type='gaussian', std=0.01), bias_filler=dict(type='constant', value=1), param=[{'lr_mult':1, 'decay_mult':1}, {'lr_mult':2, 'decay_mult':0}])
    ns.relu2 = relu_layer(bottom=ns.conv2)
    ns.pool2 = max_pool_layer(bottom=ns.relu2, ks=3, stride=2)
    ns.norm2 = LRN_layer(bottom=ns.pool2, local_size=5, alpha=1e-4, beta=0.75)
    ns.conv3 = conv_layer(bottom=ns.norm2, ks=3, nout=384, pad=1, weight_filler=dict(type='gaussian', std=0.01), bias_filler=dict(type='constant', value=0), param=[{'lr_mult':1, 'decay_mult':1}, {'lr_mult':2, 'decay_mult':0}])
    ns.relu3 = relu_layer(bottom=ns.conv3)
    ns.conv4 = conv_layer(bottom=ns.relu3, ks=3, nout=384, pad=1, group=2, weight_filler=dict(type='gaussian', std=0.01), bias_filler=dict(type='constant', value=1), param=[{'lr_mult':1, 'decay_mult':1}, {'lr_mult':2, 'decay_mult':0}])
    ns.relu4 = relu_layer(bottom=ns.conv4)
    ns.conv5 = conv_layer(bottom=ns.relu4, ks=3, nout=256, pad=1, group=2, weight_filler=dict(type='gaussian', std=0.01), bias_filler=dict(type='constant', value=1), param=[{'lr_mult':1, 'decay_mult':1}, {'lr_mult':2, 'decay_mult':0}])
    ns.relu5 = relu_layer(bottom=ns.conv5)
    ns.pool5 = max_pool_layer(bottom=ns.relu5, ks=3, stride=2)
    ns.fc6   = fc_layer(bottom=ns.pool5, nout=4096, weight_filler=dict(type='gaussian', std=0.005), bias_filler=dict(type='constant', value=1), param=[{'lr_mult':1, 'decay_mult':1}, {'lr_mult':2, 'decay_mult':0}])
    ns.relu6 = relu_layer(bottom=ns.fc6)
    ns.drop6 = dropout_layer(bottom=ns.relu6, dropout_ratio=0.5)
    ns.fc7   = fc_layer(bottom=ns.drop6, nout=4096, weight_filler=dict(type='gaussian', std=0.005), bias_filler=dict(type='constant', value=1), param=[{'lr_mult':1, 'decay_mult':1}, {'lr_mult':2, 'decay_mult':0}])
    ns.relu7 = relu_layer(bottom=ns.fc7)
    ns.drop7 = dropout_layer(bottom=ns.relu7, dropout_ratio=0.5)
    ns.fc8   = fc_layer(bottom=ns.drop7, nout=2, weight_filler=dict(type='gaussian', std=0.01), bias_filler=dict(type='constant', value=0), param=[{'lr_mult':1, 'decay_mult':1}, {'lr_mult':2, 'decay_mult':0}])
    ns.loss  = softmaxwithloss_layer(bottom=ns.fc8, label=ns.label)

    return ns.to_proto()
```


```python
def caffenet_validate(lmdb, batch_size, mirror):
    ns = caffe.NetSpec()
    ns.data, ns.label = L.Data(source=lmdb, backend=P.Data.LMDB, batch_size=batch_size, ntop=2, transform_param=dict(crop_size=227, mean_file='mean.binaryproto', mirror=mirror))

    ns.conv1 = conv_layer(bottom=ns.data, ks=11, nout=96, stride=4, weight_filler=dict(type='gaussian', std=0.01), bias_filler=dict(type='constant', value=0), param=[{'lr_mult':1, 'decay_mult':1}, {'lr_mult':2, 'decay_mult':0}])
    ns.relu1 = relu_layer(bottom=ns.conv1)
    ns.pool1 = max_pool_layer(bottom=ns.relu1, ks=3, stride=2)
    ns.norm1 = LRN_layer(bottom=ns.pool1, local_size=5, alpha=1e-4, beta=0.75)
    ns.conv2 = conv_layer(bottom=ns.norm1, ks=5, nout=256, pad=2, group=2, weight_filler=dict(type='gaussian', std=0.01), bias_filler=dict(type='constant', value=1), param=[{'lr_mult':1, 'decay_mult':1}, {'lr_mult':2, 'decay_mult':0}])
    ns.relu2 = relu_layer(bottom=ns.conv2)
    ns.pool2 = max_pool_layer(bottom=ns.relu2, ks=3, stride=2)
    ns.norm2 = LRN_layer(bottom=ns.pool2, local_size=5, alpha=1e-4, beta=0.75)
    ns.conv3 = conv_layer(bottom=ns.norm2, ks=3, nout=384, pad=1, weight_filler=dict(type='gaussian', std=0.01), bias_filler=dict(type='constant', value=0), param=[{'lr_mult':1, 'decay_mult':1}, {'lr_mult':2, 'decay_mult':0}])
    ns.relu3 = relu_layer(bottom=ns.conv3)
    ns.conv4 = conv_layer(bottom=ns.relu3, ks=3, nout=384, pad=1, group=2, weight_filler=dict(type='gaussian', std=0.01), bias_filler=dict(type='constant', value=1), param=[{'lr_mult':1, 'decay_mult':1}, {'lr_mult':2, 'decay_mult':0}])
    ns.relu4 = relu_layer(bottom=ns.conv4)
    ns.conv5 = conv_layer(bottom=ns.relu4, ks=3, nout=256, pad=1, group=2, weight_filler=dict(type='gaussian', std=0.01), bias_filler=dict(type='constant', value=1), param=[{'lr_mult':1, 'decay_mult':1}, {'lr_mult':2, 'decay_mult':0}])
    ns.relu5 = relu_layer(bottom=ns.conv5)
    ns.pool5 = max_pool_layer(bottom=ns.relu5, ks=3, stride=2)
    ns.fc6   = fc_layer(bottom=ns.pool5, nout=4096, weight_filler=dict(type='gaussian', std=0.005), bias_filler=dict(type='constant', value=1), param=[{'lr_mult':1, 'decay_mult':1}, {'lr_mult':2, 'decay_mult':0}])
    ns.relu6 = relu_layer(bottom=ns.fc6)
    ns.drop6 = dropout_layer(bottom=ns.relu6, dropout_ratio=0.5)
    ns.fc7   = fc_layer(bottom=ns.drop6, nout=4096, weight_filler=dict(type='gaussian', std=0.005), bias_filler=dict(type='constant', value=1), param=[{'lr_mult':1, 'decay_mult':1}, {'lr_mult':2, 'decay_mult':0}])
    ns.relu7 = relu_layer(bottom=ns.fc7)
    ns.drop7 = dropout_layer(bottom=ns.relu7, dropout_ratio=0.5)
    #note that nout=2 in fc8 indicates there are two output classes (cat or dog)
    ns.fc8   = fc_layer(bottom=ns.drop7, nout=2, weight_filler=dict(type='gaussian', std=0.01), bias_filler=dict(type='constant', value=0), param=[{'lr_mult':1, 'decay_mult':1}, {'lr_mult':2, 'decay_mult':0}])
    ns.acc   = accuracy_layer(bottom=ns.fc8, label=ns.label)
    ns.loss  = softmaxwithloss_layer(bottom=ns.fc8, label=ns.label)

    return ns.to_proto()
```

Define the function that will generate the CaffeNet train architecture, and return it in the prototxt format. This architecture will be used to train the net.

Define the function that will generate the CaffeNet deploy architecture, and return it in the prototxt format. This architecture will be used to test new, unlabeled images.


```python
def caffenet_deploy(batch_size):
    ns = caffe.NetSpec()
    #define the shape of the input data for deployment. value=[batch_size, num_channels, width, height]
    ns.data = L.Input(shape=dict(dim=[batch_size, 3, 227, 227]))

    ns.conv1 = conv_layer(bottom=ns.data, ks=11, nout=96, stride=4, weight_filler=dict(type='gaussian', std=0.01), bias_filler=dict(type='constant', value=0), param=[{'lr_mult':1, 'decay_mult':1}, {'lr_mult':2, 'decay_mult':0}])
    ns.relu1 = relu_layer(bottom=ns.conv1)
    ns.pool1 = max_pool_layer(bottom=ns.relu1, ks=3, stride=2)
    ns.norm1 = LRN_layer(bottom=ns.pool1, local_size=5, alpha=1e-4, beta=0.75)
    ns.conv2 = conv_layer(bottom=ns.norm1, ks=5, nout=256, pad=2, group=2, weight_filler=dict(type='gaussian', std=0.01), bias_filler=dict(type='constant', value=1), param=[{'lr_mult':1, 'decay_mult':1}, {'lr_mult':2, 'decay_mult':0}])
    ns.relu2 = relu_layer(bottom=ns.conv2)
    ns.pool2 = max_pool_layer(bottom=ns.relu2, ks=3, stride=2)
    ns.norm2 = LRN_layer(bottom=ns.pool2, local_size=5, alpha=1e-4, beta=0.75)
    ns.conv3 = conv_layer(bottom=ns.norm2, ks=3, nout=384, pad=1, weight_filler=dict(type='gaussian', std=0.01), bias_filler=dict(type='constant', value=0), param=[{'lr_mult':1, 'decay_mult':1}, {'lr_mult':2, 'decay_mult':0}])
    ns.relu3 = relu_layer(bottom=ns.conv3)
    ns.conv4 = conv_layer(bottom=ns.relu3, ks=3, nout=384, pad=1, group=2, weight_filler=dict(type='gaussian', std=0.01), bias_filler=dict(type='constant', value=1), param=[{'lr_mult':1, 'decay_mult':1}, {'lr_mult':2, 'decay_mult':0}])
    ns.relu4 = relu_layer(bottom=ns.conv4)
    ns.conv5 = conv_layer(bottom=ns.relu4, ks=3, nout=256, pad=1, group=2, weight_filler=dict(type='gaussian', std=0.01), bias_filler=dict(type='constant', value=1), param=[{'lr_mult':1, 'decay_mult':1}, {'lr_mult':2, 'decay_mult':0}])
    ns.relu5 = relu_layer(bottom=ns.conv5)
    ns.pool5 = max_pool_layer(bottom=ns.relu5, ks=3, stride=2)
    ns.fc6   = fc_layer(bottom=ns.pool5, nout=4096, weight_filler=dict(type='gaussian', std=0.005), bias_filler=dict(type='constant', value=1), param=[{'lr_mult':1, 'decay_mult':1}, {'lr_mult':2, 'decay_mult':0}])
    ns.relu6 = relu_layer(bottom=ns.fc6)
    ns.drop6 = dropout_layer(bottom=ns.relu6, dropout_ratio=0.5)
    ns.fc7   = fc_layer(bottom=ns.drop6, nout=4096, weight_filler=dict(type='gaussian', std=0.005), bias_filler=dict(type='constant', value=1), param=[{'lr_mult':1, 'decay_mult':1}, {'lr_mult':2, 'decay_mult':0}])
    ns.relu7 = relu_layer(bottom=ns.fc7)
    ns.drop7 = dropout_layer(bottom=ns.relu7, dropout_ratio=0.5)
    ns.fc8   = fc_layer(bottom=ns.drop7, nout=2, weight_filler=dict(type='gaussian', std=0.01), bias_filler=dict(type='constant', value=0), param=[{'lr_mult':1, 'decay_mult':1}, {'lr_mult':2, 'decay_mult':0}])
    ns.prob  = softmax_layer(bottom=ns.fc8)

    return ns.to_proto()
```

Now, call each of the above three functions to generate separate nets, and write them to a prototxt file. The filenames are validate.prototxt, train.prototxt, and deploy.prototxt. Note that we have defined the batch size for each of the architectures. Batch size is the number of training examples in one forward pass. To keep things easier to follow, I've set the batch size to be the same for all three architectures.


```python
#define nets for validation, training, and deployment
batch_size_validate = 256
net_validate = caffenet_validate('validation_lmdb', batch_size_validate, mirror=False)

batch_size_train = 256
net_train = caffenet_train('training_lmdb', batch_size_train, mirror=True)

batch_size_deploy = 100
net_deploy = caffenet_deploy(batch_size_deploy)

#write each one to file
with open('validate.prototxt', 'w') as f:
    f.write(str(net_validate))

with open('train.prototxt', 'w') as f:
    f.write(str(net_train))

with open('deploy.prototxt', 'w') as f:
    f.write(str(net_deploy))
```

## Step 3: <span style="color:#3e399e">Solver Definition</span>

#### The solver is responsible for model optimization. The solver's parameters are defined in a ```.prototxt file```. 

- A hyperparameter is a parameter whose value is set before the learning process begins. By contrast, the values of other parameters are derived via training.

- There are various hyperparameters like ```base_lr```, ```lr_policy```, ```gamma```, ```momentum``` and ```weight_decay``` that should be tuned to get a good convergence of the model.

Ok. That was a bit painful...this part will be much easier. We need to tell caffe what our learning parameters are going to be. These are stored in another prototxt file, which the following code will generate. We will define things such as the solver type and the learning rate. We will also point the solver to the training and validation prototxt files we just generated, which are in the same directory as this notebook. 

Lastly, we'll define the directory to store the snapshots and how often to make them. Snapshots are seriously awesome, because they are literally snapshots of the solver taken at various intervals of training. So you can train 5000 iterations, kill the program, and then resume training again from that same point a few days later (instead of having to start at iteration 0 all over). Later on, I'll show how to load and continue training from a snapshot.

Define the solver parameters. Note that these values are based on the LeNet example under caffe/examples/mnist/lenet_solver_adam.prototxt


```python
s = caffe_pb2.SolverParameter()

#set a seed for reproducible experiments
#this controls for randomization in training
s.random_seed = 0xCAFFE

#specify locations of the validation and train networks
s.train_net = 'train.prototxt'
s.test_net.append('validate.prototxt')

#test the network after every test_interval training iterations. 
#this gives accuracy measurement based on the validation dataset
s.test_interval = 250  

#do 50 forward passes every time we run the accuracy measurement on the validation dataset. set this to 50
#because the batch size is 100, and there are 5000 total validation images
#NOTE: this value must be adjusted based on the size of the validation set! it is equal to number of validation images 
#divided by validation batch size
#see section 3 for batch sizes, see section 1 for validation database size
s.test_iter.append(50) 
s.max_iter = 10000     # no. of times to update the net (training iterations)
```


```python
s.base_lr = 0.001
s.momentum = 0.9
s.momentum2 = 0.999
# since Adam dynamically changes the learning rate, we set the base learning
# rate to a fixed value
s.lr_policy = "fixed"
# Display every 100 iterations
s.display = 1000
# The maximum number of iterations
s.max_iter = 10000
# snapshot intermediate results
s.type = "Adam"

#snapshots are files used to store networks we've trained.
#we'll snapshot every 200 iterations
s.snapshot = 200
s.snapshot_prefix = 'snapshots/'  #this is the directory where snapshots are saved

#train on the CPU. this can easily be switched to GPU 
s.solver_mode = caffe_pb2.SolverParameter.GPU
```

#### <span style="color:#9e8639">Write the actual prototxt file</span>


```python
#write the solver to file
with open('solver.prototxt', 'w') as f:
    f.write(str(s))
```

## Step 4: <span style="color:#3e399e">Model Training</span>

###  <span style="color:#9e8639">Essential Concepts</span>

#### Net : Caffe nets can have any directed acyclic graph (DAG) structure

1. A network is a set of layers connected as a DAG.
2. Caffe creates and checks net from the definition.
3. Data and derivatives flow through the net as blobs- an array interface.

Finally, all that prep work comes together to train the net. We'll start out by loading the solver and printing the output dimensions of each layer and corresponding weight sizes.


```python
#set caffe to CPU mode. this can easily be changed to GPU (commented out)
#caffe.set_mode_cpu()
#use the below two lines to run on GPU
caffe.set_device(0)
caffe.set_mode_gpu()

#load the solver and create train and validation nets
solver = None  #some weird workaround for lmdb 0data
solver = caffe.SGDSolver('solver.prototxt')



#print the output size for each layer in dimensions of B x W x H x D
#where B=batch size, W=width, H=height, D=depth
print('output sizes:')
for k,v in solver.net.blobs.items(): 
    print(('layer: %s, shape: %s') % (k, v.data.shape))

#print the weight sizes
print('\nweight sizes:')
for k, v in solver.net.params.items(): 
    print(('layer: %s, shape: %s') % (k, v[0].data.shape) )
```

Next, visualize some of the data from the training and validation databases to ensure our data is labelled correctly. Recall that we assigned label 0 to cats and label 1 to dogs in section 1 (during database creation).


```python
#run just one step of gradient descent for both training and validation nets
solver.net.forward()
```


```python
solver.test_nets[0].forward() 
```


```python
#visualize some data from the validation net, and their corresponding labels
imgs = solver.test_nets[0].blobs['data'].data[:8, 0]
imgs_comb = np.hstack((np.asarray(i) for i in imgs))
fig, ax = plt.subplots(figsize=(18, 2))
plt.imshow(imgs_comb, cmap='gray')
plt.axis('off')
plt.show()
print(('validation labels: %s') % (solver.test_nets[0].blobs['label'].data[:8]))
```

Here we can restore the solver to a previously stored snapshot. Each time a snapshot is created, it produces two files:  

1. caffemodel -- this is the output model 

2. solverstate -- this is the snapshot image

Lets say you wanted to set the solver state to the snapshot corresponding to the 5200th iteration. The code below implements this.


```python
DO_RESTORE = False
if DO_RESTORE:
    snapshot = 'snapshots/_iter_6200.solverstate'
    solver.restore(snapshot)
```

Almost ready to start training. Just define a few more variables, and some arrays to store intermediate results.

1. We will run the current model on the validation set every validation_interval iterations to get an idea of the current accuracy. 
2. Batch size for validation is 100. 
3. There are 25000 total images, of which 5000 are in the validation set. Therefore, we need to run 50 forward passes of the validation net to go through all the data. 

```This logic is explained in the solver.prototxt```


```python
validation_interval = 250 #defines how often to test the accuracy on the validation dataset
validation_iter = 50  #this is the value of test_iter in solver.prototxt, and indicates how many forward passes are required to go through all the validation data

#store training loss, validation accuracy
train_loss = []
validation_loss = []
validation_acc = []
loop_count = 0
```

In the following loop, we train the model. It is configured to run until the accuracy is at least 90%. It's possible that it may never reach that level, or that it may take an extremely long time, so you can always terminate this loop at any time. Based on the value in the solver, every s.snapshot iterations a snapshot is made and all the arrays holding loss and accuracy information are also saved.


```python
keep_looping = True
while keep_looping:
    #do one iteration
    solver.step(1)  

    #store the train loss after every step of training
    train_loss.extend([solver.net.blobs['loss'].data.tolist()])
    print(('train loss: %f') % (train_loss[-1]))
    #calculate the accuracy every validation_iter iterations
    if loop_count % validation_interval == 0:
        print(('iteration %i, testing...') % (loop_count))
        correct = 0.
        total_num_samples = 0.
        for validation_it in range(validation_iter):
            solver.test_nets[0].forward()
            correct = correct + sum(solver.test_nets[0].blobs['fc8'].data.argmax(1) == solver.test_nets[0].blobs['label'].data)
            total_num_samples = total_num_samples + len(solver.test_nets[0].blobs['label'].data)

        validation_acc.extend([correct/total_num_samples])
        validation_loss.extend([solver.test_nets[0].blobs['loss'].data.tolist()])

        print(('validation accuracy: %f') % (validation_acc[-1]))
        print (('validation loss: %f') % (validation_loss[-1]))

        #save all the arrays to file
        np.save('train_loss.npy', train_loss)
        np.save('validation_loss.npy', validation_loss)
        np.save('validation_acc.npy', validation_acc)

    #keep training until we reach 90% accuracy
    if validation_acc[-1] > 0.9:
        break

    #increment the loop count
    loop_count = loop_count+1
    
    #do this every loop to force iPython to do realtime printing
    sys.stdout.flush()
```

5250 iterations and about 8 hours later...we reached 90% accuracy! Let's take a look at the accuracy and loss curves. First, load the arrays that were saved as npy files. For the validation arrays, these were updated every validation_interval iterations, so create a corresponding index for plotting purposes.


```python
#load the various arrays for plotting
train_loss = np.load('train_loss_orig.npy')
validation_loss = np.load('validation_loss_orig.npy')
validation_acc = np.load('validation_acc_orig.npy')

#validation_loss and validation_acc were appended to the array every validation_interval iterations, whereas train_loss 
#was appended every iteration. so just define an index for the validation arrays for plotting purposes
validation_inds = range(0,len(validation_loss)*validation_interval, validation_interval)
```

### Plotting the learning curve

A learning curve is a plot of the training and test losses as a function of the number of iterations. These plots are very useful to visualize the train/validation losses and validation accuracy.



```python
fig, ax1 = plt.subplots(figsize=(12, 8))
ax2 = ax1.twinx()

ax1.plot(train_loss, 'g', label='Training Loss')
ax1.plot(validation_inds, validation_loss, 'b', label='Validation Loss')
ax1.set_ylabel('Loss')
ax1.legend(loc='upper left')

ax2.plot(validation_inds, validation_acc, 'r')
ax2.set_xlabel('Iterations')
ax2.set_ylabel('Validation Accuracy')
plt.axis([0, len(train_loss), 0, 1])

plt.show()
```


![png](/img/output_75_0.png)


The above curve looks pretty good. There's no evidence of overtraining, in which the accuracy would continue increasing and the training loss would continue decreasing, but the validation loss would increase. It seems we could have continued training to achieve an even higher accuracy, but since we have the snapshot at iteration 5200 we can still do this with no problem.  

If you went through the training, the snapshots directory will have a ton of snapshots. But to save you the hassle, I've included my snapshot for iteration 5200 (iter_5200.caffemodel). We'll work with this model in the next step.

## Step 5: <span style="color:#3e399e">Predictions on New Data</span>

#### To make predictions, we need four files:

1. Test images
2. Trained model weights
3. Model architecture file ( deploy.prototxt )
4. Mean image (Optional)

Now that we have a fairly accurate model, let's test it out with some new images of cats and dogs. This a relatively simple process in comparison to all that we've had to do so far. 

We'll load two images -- one of a cat and one of a dog. Each image is preprocessed in the same way we preprocessed the training images. In addition, we subtract the mean image because that was part of the transformation step in the data layers of the training and validation architectures. Basically it boils down to this...for every new image we want to classify, we have to exactly reproduce the conditions of the training images. 

First, we point caffe to the deploy architecture, and load up the model from iteration 5200.   


```python
#set caffe to CPU mode
#define the directories for the model definition and model weights
caffe.set_mode_cpu()

model_def = 'deploy.prototxt'
model_weights = 'snapshots/_iter_5200.caffemodel'

#define the net
net = caffe.Net(model_def,      # defines the structure of the model
                model_weights,  # contains the trained weights
                caffe.TEST)     # use test mode (e.g., don't perform dropout)
```

Next, define a function to preprocess the image and subtract the mean image


```python
#load the mean image
mean_img = np.load('mean_img.npy')

def prepare_img(img):
    #do histogram equalization to account for contrast. this function expects a grayscale image
    #so do each BGR component separately
    img[:,:,0] = cv2.equalizeHist(img[:,:,0])
    img[:,:,1] = cv2.equalizeHist(img[:,:,1])
    img[:,:,2] = cv2.equalizeHist(img[:,:,2])

    #resize the image
    #TODO: there are many potential complications with resizing an image, and also different approach
    #is better for upscaling or downscaling an image. this can definitly be improved.
    img = cv2.resize(img, (227, 227), interpolation = cv2.INTER_CUBIC)

    #subtract the mean image
    img = img - mean_img

    #reshape to (3,x,y)
    img = np.rollaxis(img, 2)

    return img
```

Load the two images, and preprocess them


```python
#load images to classify
img1 = cv2.imread('cat1.jpg', cv2.IMREAD_COLOR)
img2 = cv2.imread('dog1.jpg', cv2.IMREAD_COLOR)

plt.imshow(img1)
plt.axis('off')
plt.show()

plt.imshow(img2)
plt.axis('off')
plt.show()

#resize the input images to match the input size of the net and subtract the mean image
#also redefine the axes from (x,y,3) to (3,x,y) 
img1 = prepare_img(img1)
img2 = prepare_img(img2)


#combine these two images together into an array of dimension (100,3,227,227) 
#note that 100 is the batch size for deploy architecture
imgs = np.zeros(shape=(100,3,227,227))
imgs[0] = img1
imgs[1] = img2
```


![png](output_85_0.png)



![png](output_85_1.png)


Now, do the actual classification. The first step is to put the images into the memory allocated for the net. Recall that our batch size for deployment is 100, which is why we defined our "imgs" array to be of size 100. So we could have stacked up to 100 images into the "imgs" array and classified them all at the same time.  

Run forward prop to get the output probabilities.


```python
net.blobs['data'].data[...] = imgs #Check: net.blobs['conv'] would contain computer data in conv layer
net_output = net.forward()
```

net_output is a dictionary, and the probabilities are stored in the key "prob". The key is called "prob" because in our deploy architecture, the last layer (the softmax layer) is named "prob".    

Let's take a look at the first 2 results


```python
print (net_output['prob'][0:2])
```

    [[9.9999952e-01 4.6931061e-07]
     [2.1402871e-04 9.9978596e-01]]


Our first image has a 0.999 probability of being a cat (label 0) and our second image has a 0.9 probability of being at dog (label 1). 



----------------------------------------------------------------------------------------------------------

So it's working well.  


```python
import numpy as np
import matplotlib.pyplot as plt
 
 
def visualize_weights(net, layer_name, padding=4, filename=''):
    # The parameters are a list of [weights, biases]
    data = np.copy(net.params[layer_name][0].data)
    print(data.shape)
    # N is the total number of convolutions
    N = data.shape[0]*data.shape[1]
    print(data.shape[0])
    print(data.shape[1])
    print(data.shape[2])
    # Ensure the resulting image is square
    filters_per_row = int(np.ceil(np.sqrt(N)))
    # Assume the filters are square
    filter_size = data.shape[2]
    # Size of the result image including padding
    result_size = filters_per_row*(filter_size + padding) - padding
    # Initialize result image to all zeros
    result = np.zeros((result_size, result_size))
 
    # Tile the filters into the result image
    filter_x = 0
    filter_y = 0
    for n in range(data.shape[0]):
        for c in range(data.shape[1]):
            if filter_x == filters_per_row:
                filter_y += 1
                filter_x = 0
            for i in range(filter_size):
                for j in range(filter_size):
                    result[filter_y*(filter_size + padding) + i, filter_x*(filter_size + padding) + j] = data[n, c, i, j]
            filter_x += 1
 
    # Normalize image to 0-1
    min = result.min()
    max = result.max()
    result = (result - min) / (max - min)
 
    # Plot figure
    plt.figure(figsize=(10, 10))
    plt.axis('off')
    plt.imshow(result, cmap='gray', interpolation='nearest')
 
 
    # Save plot if filename is set
    if filename != '':
        plt.savefig(filename, bbox_inches='tight', pad_inches=0)
 
    plt.show()
```


```python
#from visualize_caffe import *
import sys
 

model_def = 'deploy.prototxt'
model_weights = 'snapshots/_iter_5200.caffemodel'

net = caffe.Net(model_def,model_weights,caffe.TEST)
 
visualize_weights(net, 'conv1', filename='conv1.png')
visualize_weights(net, 'conv2', filename='conv2.png')
visualize_weights(net, 'conv3', filename='conv2.png')
```

    (96, 3, 11, 11)
    96
    3
    11



![png](output_93_1.png)


    (256, 48, 5, 5)
    256
    48
    5



![png](output_93_3.png)


    (384, 256, 3, 3)
    384
    256
    3



![png](output_93_5.png)


### References

1. caffe oficial documentation
2. http://cs231n.stanford.edu/  
3. http://adilmoujahid.com/posts/2016/06/introduction-deep-learning-python-caffe/
4. http://christopher5106.github.io/deep/learning/2015/09/04/Deep-learning-tutorial-on-Caffe-Technology.html





Get in touch: https://ajinkyaghadge.github.io

Following the above steps, we've gone through the entire process of obtaining images for training, creating databases for training and validation, defining network architectures, training a network with high accuracy, and testing new unlabeled images.  

You can take this framework and apply it to any other classification problem you'd like to solve. There are definitely many adjustments and improvements you can make, depending on the problem you're trying to solve.  

First, good image preprocessing can make a big difference. In the case of OCR analysis, deskewing the image as a preprocessing step has major benefits. In other cases with high frequency noise, perhaps a lowpass filter would help. You'll have to be creative and most likely determine the best conditions through trial and error.

Second, choosing the right network architecture is important. There are lots of really powerful and well researched architectures out there. Each one has its advantages and disadvantages, and most likely some architectures perform optimally for certain types of data versus other types of data. For example, a complex problem such as a cat versus dog classifier would probably perform poorly in a tiny architecture (ie, a network with only one convolution layer). 

Lastly, setting the solver parameters can make or break your training. If your learning rate is too high, the solver may spiral out of control and crash. If you set the learning rate too low, it may take unreasonably long to get a good accuracy. Choosing the type of solver (such as SGD vs Adam) and its corresponding parameters is also important. Fortunately there are a lot of good examples out there to get you started.
