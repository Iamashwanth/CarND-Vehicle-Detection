## Writeup Template
### You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---

**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./sliding_window.png
[image2]: ./result.png
[image3]: ./heatmap.png
[video1]: ./project_video.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Vehicle-Detection/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Histogram of Oriented Gradients (HOG)

#### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.

HOG feature extraction is implemented in the function `get_hog_features()`
I choose the following parameters - 8 orientation bins for histogram,
8x8 pixels per cell and 2x2 cells per cell and gray scale for color space.
Reason to choose gray scale is explained in the next section.
Choosing the above parameters kept the feature vector small
without having any negative effect on accuracy.

#### 2. Explain how you settled on your final choice of HOG parameters.

Initial I used all three channels of the image to compute HOG, this gave very high test accuracy(99).
But the downside was that the number of features increased by 3 fold.
Since this was slowing down the processing on the project video, I decided to go with gray scale image.
Using gray scale resulted in loss of accuracy(went down to 97).
I also tried L-channel from HLS color space, but it was giving very similar results as gray.

#### 3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I have included Spatial Binning of colors, Histogram of colors and HOG for feature vector.
After normalizing these features, I used it to train Linear SVM.

### Sliding Window Search

#### 1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

I experimented with the size and overlapping parameters of sliding window based on their position in the image.
I choose to use small window size for region of image that covers upper part of the road. Following this logic I decided to use different window sizes over different regions of the image.

I used three different window sizes - 192, 144, 96.
Used a high Overlapping factor - 0.7 to 0.9

![alt text][image1]

#### 2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?


![alt text][image2]
---

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./output.mp4)


#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I made use of the technique mentioned in the lectures.
Basically apply a threshold on the detected windows and remove windows that does not meet the threshold criteria. Then I labelled each of the blobs and plotted bouding boxes around them.

### Below is the image of the headmap and the bounding boxes.
![alt text][image3]
---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

My pipeline is inefficient in computing HOG features since it does not use subsampling.
Since I compute HOG on every single window when doing sliding window search, my model is very slow.
Also I am not maintaing information about positive samples from the previous frames.
Using this information would help the model perform better.