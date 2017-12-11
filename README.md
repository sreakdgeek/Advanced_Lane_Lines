## Advanced Lane Detection

### Introduction

Advanced Lane detection project involves detecting and tracking lane lines from input images or a video stream in 
varying conditions of the road. Using techniques such as distortion correction, perspective transformation, gradient
and color thresholding and sliding window fit, lane line detector is built. Below are the broad steps involved
in building such a lane detector:


*	Camera Calibration
*	Distortion correction
*	Perspective transformation
*	Gradient and color threshold
*	Sliding window fit
*       Lane tracking using best fit
*	Radius of curvature and Vehicle position w.r.t Lane Center
*       Video generation
*       Discussion


[//]: # (Image References)

[image1]: ./images/camera_calibration1.JPG
[image2]: ./images/camera_calibration2.JPG
[image3]: ./images/camera_calibration3.JPG
[image4]: ./images/camera_calibration4.JPG
[image5]: ./images/camera_calibration5.JPG
[image6]: ./images/camera_calibration6.JPG
[image7]: ./images/test_image.JPG
[image8]: ./images/undistorted_image.JPG
[image9]: ./images/warped1.JPG
[image10]: ./images/thresholded_HLS.JPG
[image11]: ./images/thresholded_LAB.JPG
[image12]: ./images/sliding_window_fit.JPG
[image13]: ./images/sliding_window_fit_convolutions.JPG
[image14]: ./images/sliding_window_histogram.JPG
[image15]: ./images/radius_of_curvature_calculation.JPG
[image16]: ./images/radius_of_curvature.JPG
[image17]: ./images/end_to_end_pipeline.JPg
[image18]: ./images/sliding_window_prev_frame.JPG

---
### Camera Calibration

![alt text][image1]
![alt text][image2]
![alt text][image3]
![alt text][image4]


### Distortion correction

#### Test Image
![alt text][image7]

#### Undistorted image
![alt text][image8]


### Perspective transformation and warp image

#### Perspective transform

![alt text][image9]

### Color and Gradient Thresholding

#### Gradient and Color thresholding - Using HLS

![alt text][image10]

#### Gradient and Color thresholding - Using LAB

![alt text][image11]


### Sliding window fit

#### Histogram

![alt text][image14]

#### Sliding Window fit

![alt text][image12]

#### Sliding Window fit using convolutions

![alt text][image13]

### Lane tracking using Best fit

![alt text][image18]

### Radius of Curvature and Vehicle offset from lane center

![alt text][image15]

![alt text][image16]

### End to End Pipeline

![alt text][image17]

### Video Generation


### Discussion
