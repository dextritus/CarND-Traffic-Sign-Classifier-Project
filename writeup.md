# **Traffic Sign Recognition** 


---

**Build a Traffic Sign Recognition Project**

The goals / steps of this project are the following:
* Load the data set (see below for links to the project data set)
* Explore, summarize and visualize the data set
* Design, train and test a model architecture
* Use the model to make predictions on new images
* Analyze the softmax probabilities of the new images
* Summarize the results with a written report

[//]: # (Image References)
[before]: ./examples/before.jpg "Original image"
[after]:  ./examples/after.jpg  "Nomalized image"

[image1]: ./examples/visualization.jpg "Visualization"
[image2]: ./examples/grayscale.jpg "Grayscaling"
[image3]: ./examples/random_noise.jpg "Random Noise"
[img30]: ./german/30.jpg "Speed limit 30km/h"
[imgyield]: ./german/yield.jpg "Yield"
[imgahead]: ./german/oneway.jpg "Ahead only"
[imgpriority]: ./german/rightaway.jpg "Priority"
[imgdonotenter]: ./german/donotenter.jpg "Do not enter"

---
## Data set

The data sets consists of german traffic signs belonging to 43 different classes. There are 34799 training images, 4410 validation images and 12630 test images. Each image is an RGB image with the size of 32x32 pixels.

An example of a training image is shown below.

![alt text][before]

### Data normalization
After looking at several examples of images in the training data, I observed that the brightnesses vary considerably among the images. After some research, I found a way to normalize the brightness of an RGB image. The pipeline for this is as follows:

* convert the original image to HLS space
* apply histogram equalization on the L channel (this normalizes the luminance, basically)
* convert the image back to RGB
* finally, convert it to grayscale 

I decided to use the grayscale image since most traffic signs differ in content and shape, and the color is not usually a deciding factor in deciding which traffic sign is which. Of course, using the full RGB image would have been an option as well.

The same example image after normalization is shown below.

![alt text][after]

## Convolutional Neural Network

The starting point was the LeNet network, and after trying different hyperparameters and architectures, the following structure was chosen:

* Layer 1: Convolutional. Input = 32x32x1. Output = 28x28x16 (16 features)
* Activation: ReLu
* Max Pooling. Input = 28x28x16. Output = 14x14x16. 
* Layer 2: Convolutional. Output = 10x10x32. (32 features)
* Activation: ReLu
* Max Pooling. Input = 10x10x32. Output = 5x5x32.
* Layer 3: Fully Connected. Input = 800. Output = 256.
* Dropout + Activation (ReLu)
* Layer 4: Fully Connected. Input = 256. Output = 128.
* Dropout + Activation.
* Layer 5: Fully Connected. Input = 128. Output = 43.

The hyperparameters I chose were:

* learning rate : 0.0008
* epochs : 75 (early stopping if accuracy > 0.95)
* batch size: 128
* keep probability in the droput layers : 0.75

To choose these parameters, I mainly played with the learning rate and batch size that results in a steady increase in validation accuracy, an plateaus somewhere around 94% percent. 

The accuracy on the validation data is around 0.95. The accuracy with this network on the test data is 0.926.

## Test the network on 5 new images

The five images I chose are presented below, after being cropped to a 32x32 dimension.

![alt text][img30]
![alt text][imgyield]
![alt text][imgpriority]
![alt text][imgdonotenter]
![alt text][imgahead]

These represent a speed limit sign (30km/h), a yield sign, a priority sign, a do not enter sign, and an ahead only sign.

Therefore, the lables for these 5 images are [1 13 12 17 35]. 
The NN predicted all 5 images correctly, the logits being [1 13 12 17 35].

## Highest 5 softmax probabilites

For the 5 images, the highest 5 softmax probabilities are:

* Speed limit sign: [ 0.9989, 1e-4, 1e-10, 1e-13, 1e-14], for labels [1, 5, 2, 3, 8]
* Yield sign      : [1.00, 9.566-37, 0, 0, 0] for labels [13, 1, 0, 2, 3]
* Priority sign   : [1.00, 0, 0, 0, 0] for labels [12, 0, 1, 2, 3]
* Do not enter sign: [1.00, 7.1e-26, 4.5e-37, 2.27e-37, 0] for labels [17, 14, 40, 0, 1]
* Ahead only sign:   [1, 6.5e-11, 3.48e-13, 3.57e-14, 9.95e-18] for labels [35, 36, 3, 34, 33] 

For these particular images, the neural network was very confident on the labels of the images, as it is reflected on the highest softmax probabilities being as high as 99%. Note however that the images are relatively clear, and it is expected that they do not to pose a major difficulty for the network.

## Summary

A slighlty modified CNN based on the LeNet architecture was created for traffic sign classification. The input images were grayscale images, luminance normalized from RGB images. The accuracy on test data was close to 93%, whereas on the validation data 95%. The newtork correctly classified new german traffic signs acquired from google images.

The advantage consists in a very simple normalization of the input images, whereas the biggest disadvantage is the lack of training data augmentation, such as slight translations and scalings of the training set. I will develop this in the future, however I have not set this goal for this project.

