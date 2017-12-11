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
*	Lane tracking using best fit
*	Radius of curvature and Vehicle position w.r.t Lane Center
*	Video generation
*	Discussion


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
[image17]: ./images/end_to_end_pipeline.JPG
[image18]: ./images/sliding_window_prev_frame.JPG

---
### Camera Calibration

Camera calibration is a process of mapping of a pinhole camera from 3D points in the real world to 2D points in an image.
This process learns the camera matrix that helps project 3D points to 2D points. If y represents 3D points in real world
which are projected onto 2D points in an image represented by vector x, matrix C provides the mapping of y to x:

             y ~ Cx

For this project, using chess board images taken from different angles are used to determine the camera matrix C, real world 
object points and image points.

![alt text][image1]

Calibration Image 1:

![alt text][image1]

Calibration Image 2:

![alt text][image2]

Calibration Image 3:
![alt text][image3]


### Distortion correction

After learning the camera matrix, image is undistorted. Below shows the test image and the undistorted image.
The effect of undistortion can be observed in the change to the car hood.

#### Test Image

![alt text][image7]

#### Undistorted image

![alt text][image8]


### Perspective transformation and warp image

#### Perspective transform

Next step in lane detection process is to take the undistorted image and warp the image. Warping the image implies taking the image
taken from frontal center camera view and provide a bird's eye view. Intuition behind this process is that, with this projection,
lane curature can be more clearly identified and we will be able to fit 2nd degree polynomial to the lane lines. 

Initially, I attempted programmatically deriving the source and destination points for obtaining the projection but carefully
chosen points worked much better. Below shows the source and destination points that I have selected for the projection:

Source Points

left_bottom = (257, 685)
right_bottom = (1050, 685)

left_top =  (583, 460)
right_top = (702, 460)

Destination Points

left_bottom = (300,  height)
right_bottom = (width-300, height)

left_top =  (300, 0)
right_top = (width-300, 0)

![alt text][image9]

### Color and Gradient Thresholding

Once I have a warped image, my next step would be to use color and/or gradient thresholding to identify lane markings.
Color and gradient thresholding helsp in identifying the lane markings which could be in different colors, thickness and could be 
affected by shadows. 

I have experimented with HLS and LAB color spaces for different ranges of pixel values. I have used a combination of color and gradient
threshold to identify lane lines. Below were the final threshold values that were helpful:

HLS Space

L - Threshold (40, 255]
S - Threshold (170, 255]

Sobel X - Threshold (20, 100]

Using the above thresholds a combined binary image was generated.

LAB Space

L - Threshold (220, 255]
B - Threshold (190, 255]

Sobel X - Threshold (20, 100]

#### Gradient and Color thresholding - Using HLS

![alt text][image10]

#### Gradient and Color thresholding - Using LAB

![alt text][image11]


### Sliding window fit

Once we have a binary thresholded, warped image, we can generate a histogram of pixel intesities at various
X coordinate positions. It is expected that peaks would be generated where the lane lines exist. Using the peaks 
in the histogram as a starting point, a sliding window approach can be used to filter out points on the lane lines.
Once the lane lines are identified a 2nd degree polynomial can be fit to these points to identify the lane markings.

#### Histogram


Twin peaks can be observed in below histogram:

![alt text][image14]

#### Sliding Window fit

Below is the algorithm for slidng window fit:

1. Start from the base x-coordinates identified from the histogram peaks.
2. Using certain number of windows (say 9) across the y-axis and a margin, we search for non-zero pixels in the window.
3. if the number of non-zero points exceed certain threshold, re-center next window using mean of these non-zero points and continue the search
4. Once all the points on the left and right lanes have been identified, 2nd degree polynomial is fit to the identified points.

Below shows the plot for the sliding window fit:

![alt text][image12]

#### Sliding Window fit using convolutions

Sliding window fit using convolutions is a variation to the above approach. In this approach, while sliding across
the y-axis, convolution operation is performed on the image patch. Best left and right centroids are computed on the convolved patch.

Below shows the sliding window convolved fit:

![alt text][image13]

### Lane tracking using Best fit

In order to smoothen the fit to the lane markings, most recent fits to the lane lines can be used 
instead of detecting the lane lines in each frame. This involves tracking the lane fit and compute
an average of last 'n' fits and use this average as the best fit for a given frame. If the best fit
deviates significantly from the current fit, then use the current fit instead of the best fit.

Below image shows the fit using the previous frames:

![alt text][image18]

### Radius of Curvature and Vehicle offset from lane center

Radius of curvature and vehicle offset provide a sanity check on the fit.

Below shows the Radius of curvature calculation:


![alt text][image15]

Code snippet:

left_curverad = ((1 + (2*left_fit_cr[0]*y_eval*ym_per_pix + left_fit_cr[1])**2)**1.5) / np.absolute(2*left_fit_cr[0])
right_curverad = ((1 + (2*right_fit_cr[0]*y_eval*ym_per_pix + right_fit_cr[1])**2)**1.5) / np.absolute(2*right_fit_cr[0])


Below is Radius of curvature 

![alt text][image16]

Vehicle offset from lane center

This metric is calculated assuming that the camera is fixed to the center of the vehcile. 
This metric provides insight on how much deviation exists between the vehicle position and
the base of lane center. This is calculated as:

        |(image width - average of left and right lane markings)|

Code snippet:

    leftx = left_fit[0] * (bottom_y ** 2) + left_fit[1] * bottom_y + left_fit[2]
    rightx = right_fit[0] * (bottom_y ** 2) + right_fit[1] * bottom_y + right_fit[2]
    vehicle_offset = abs((width / 2) - (leftx + rightx) / 2)


Radius of curvature and vehicle offset computed in pixel space are converted to real world dimensions using
conversion factors.

### End to End Pipeline

End to end pipeline is generated using below steps:

1. Undistort image using calibrated camera matrix 
2. Perform perspective transformation using warped image
3. Use color and gradient threshold to filter out the pixels
4. Use sliding window fit to identify the lane points and fit a 2nd degree polynomial
5. Warp the image to original image space and fill the polynomial
6. Blend the lane marked iamge to the original image

Below is the result of end to end pipeline:

![alt text][image17]

### Video Generation

Below is the ink to the Video generated:

https://github.com/sreakdgeek/Advanced_Lane_Lines/blob/master/test_videos_output/project_video.mp4

### Discussion

There are several potential improvements that can be made to this solution:

1. Explore more color spaces and thresholds to more accurately identify the lane markings especially in conditions
   when shadows are cast, under varying lighting conditions, etc.

2. RANSAC is another approach that can be used potentially instead of polynomial fit through regression.
