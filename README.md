# Blind source separation
[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1wSBcYpbc8WwlzpSZDcGKvyuiqHrKmkow?usp=sharing)

- The purpose of the project is to separate an image obtained as a sum of a two images into its components. 
- The two images `img1` and `img2` summed together come from different dataset: `mnist` and `fashion_mnist`, respectively.
- No preprocessing is allowed. The network takes in input the sum `img1+img2` and returns the predicted components `hat_img1` and `hat_img2`. 
- The metric used to evaluate the project is the `mean squared error` between predicted and ground truth images.

# U-Net Architecture 
The architecture that I chose to solve the task is the one of the well-known U-Net model. While the initial research paper that introduced the U-Net architecture was to solve the task of Biomedical Image Segmentation, it was not limited to this single application. The model can still solve the most complex problems in deep learning and it showed its utlity also to solve the current Blind source separation problem. 
<br>  
In the original paper, the UNET is described as follows:  
<div align ='center'><img src="https://lmb.informatik.uni-freiburg.de/people/ronneber/u-net/u-net-architecture.png" width="500" height="300"/></div>
<br>

It uses the concept of fully convolutional networks, thus it only contains Convolutional layers and does not contain any Dense layer or Flatten layers (or similar) because of which it can accept image of any size.
<br>  
The architecture can be divide into two different paths:
- **Downsampling path (encoder)**: used to capture the context in the image. The encoder is just a traditional stack of convolutional and max pooling layers. 
- **Upsampling path (decoder)**: symmetric path to the previous one which is used to enable precise localization using transposed convolutions. 


The intent of the U-Net is to capture both the features of the context as well as the localization. This process is completed successfully by the type of architecture built. The main idea of the implementation is to utilize successive contracting layers, which are immediately followed by the upsampling operators for achieving higher resolution outputs on the input images. 

# Customized U-Net

However, starting from the standard U-Net architecture I made some improvements to the net in order to adapt it in the best way to the current task:

- **Concatenation of output channels**: In the standard U-Net architecture, the output layer is a convolutional layer which produces a single feature map and makes use of sigmoid. In our problem, we need to separate two mixed images, therefore the output layer must have two output channels. Then the two output channels have been concatened in order to create an unique 32x64 image.

- **Increase of filters and coinvolutions before upsampling**: Before performing the upsampling step I decided to double the number of filters (from 128 to 256) and the number of coinvolutions per step (from 2 to 4). This choice has been made because we have two output channels instead of only one and it helped to reduce substantially the results the final MSE error.

- **Depthwise coinvolution**: a final Depth-wise convolution turned out to be useful to apply each filter channel only at one input channel. Therefore, it is as if I applied different convolution to each image. It gave an important contribute for the improvement of  the results.

We can summarize the final chosen architecture in the following manner:
- 2 encoder blocks with 2 convolution per layer. Each time the numbers of filters is doubled.

- 2 decoder blocks with 4 convolution per layer. Each time the numbers of filters is halved.

- Eventually, a Depthwise-Convultion has been applied to the net before concatening the two output channels.

The **total number of parameters** turns out to be **3,684,214**

# Results on test set
The results on the test set show that the model managed to get a **mean MSE error** of about **0.00036**. The **standard deviation** is **very low (2.0802659e-06)**, therefore there is no variance between the results of each iteration. As it is possible to see from the sample outputs inside the notebook, the predicted components and the original ones are almost the same. 
