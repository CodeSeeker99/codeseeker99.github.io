---
published: false
---
## Pytorch Build 1: Basic classification tasks

_In this tutorial we're going to go through the basics of transfer learning while training 2 models, one on blur detection and one on exposure detection_

_**Note**: This tutorial requires you to have basic knowledge of machine learning concepts and terminologies_

---

<h2 style="background-color:black; color:white">PROBLEM STATEMENT</h2>

_Before embarking on any deep learning task. It is very important to clearly define the problem itself. If the programmer is not clear on the goals of the task, then decision making for the model parameters becomes difficult and vague._

The video production team at any college fest faces an immense task when starting out to create promotional videos for it. This is, sorting through the entire footage collected over a span of many days, often collected by many people. In this pile of footage, there are a lot of videos which are objectively unusable because they're too shaky or too blurred. The problem I've chosen today is to make something that can ease this sorting process by flagging unusable footage. 

For the sake of simplicity, let's define unusable as footage that is too blurred, too bright, or too dim. To form this into a more solid problem statement. We now need an algorithm that classifies a video into these categories. Divide and Conquer is a very well known paradigm in Deep Learning. So we'll split this problem into 2 atomic sets. 
**One, is to classify the blurriness of a frame**
**The second, is to classify the exposure of a frame**
We will then average the results for the entire video and rate the footage.

Now that we have our problem statement. Let's begin with the tutorial.

<h2 style="background-color:black; color:white">BACKGROUND</h2>

For this tutorial we would be using the Pytorch frameowrk to train and test our deep learning models. We are going to employ the transfer learning approach as the training set we have is pretty small.

<h3 style="text-align:left">PyTorch</h3>

- - add picture here pytorch - -

Pytorch is one of the most popular libraries for implementing deep learning algorithms. It is based off of Facebook AI Research (FAIR) lab's library called Torch. Its doctrine is to be a staple equivalent of NumPy for general mathematical calculations in the area of Deep Learning (DL). Please install PyTorch on your machine according to the instructions from [here](https://pytorch.org/get-started/locally/).

<h3 style="text-align:left">Transfer Learning</h3>

As the name suggests, the method of transfer learning uses a model's knowledge gained from solving one task, in a different but related task. In our use-case, we use DL models trained on the [ImageNet](http://www.image-net.org/) database to classify pictures from a 1000 categories, for classification of blurred and overexposed images in a video. In this technique, we take the pre-trained model, and add only a few classification layers to the end before training it for a few epochs. The idea is to use the model's existing knowledge of edges and lighting to detect blurred images, or overexposed images. This technique is particularly useful when computation power and time for training are limited. 

<h2 style="background-color:black; color:white">DATASET</h2>

For the blur detection task. We will be using the [CERTH Image Blur dataset] (https://pgram.com/dataset/certh-image-blur-dataset/). Originally, this dataset contained more classes, but we will stick to the classfication between natural blur and non-blurred images. This is a good sized dataset with over a 1000 images in total.

For the exposure task. We would be using a dataset of my own. This is simply a collection of clips from college fests that have been sorted manually. [Drive link to dataset](#) (TODO: Add link here)

<h2 style="background-color:black; color:white"> Tutorial </h2>

.
