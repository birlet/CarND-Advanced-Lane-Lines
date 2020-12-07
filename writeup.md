## Writeup Advanced Lane Detection

**Advanced Lane Finding Project**

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[image1]: ./output_images/camera_calibration.png "Camera Calibration"
[image2]: ./output_images/distortion_corrected.png "Pipeline"
[image3]: ./output_images/pipeline_warp.png "Pipeline"
[image4]: ./output_images/line_detection.png "Line Detection"
[image5]: ./output_images/map_down.png "map_down"
[video1]: ./test_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---


### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the 2. code cell of the IPython notebook located in "./P2.ipynb".  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function. With this function I get the needed parameters  

I applied this distortion correction later in the image processing pipeline. I used the  `cv2.undistort()` function. An example can be seen here:

![alt text][image1]

### Pipeline

Here you can see the results of my pipeline. The method will be discussed in the following parts. The code is also in "./P2.ipynb".


#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images. I used the parameters received from the camera calibration and the `cv2.undistort()` function. Here you can see an example:

![alt text][image2]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result. & 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.


For the image processing I used my function called 'pipeline()'. Inside the function I used the methods and parameters we worked out in the exercises. 
(2) First I convert the image to HLS color space for a better recognition for colored lines. After that I used the Sobel function to find especially the derivates in x direction, because that is where I expect my route lines to be. I used a combination of color and gradient thresholds to generate a binary image. You can see an example in the following image and the code in the 'pipeline()'-function
(3) After the image processing I did the perspective transformation of the binary image in the 'undistort()'-function . I identified the pixels out of the example picture.


| points       | x-value           | y-value  |
| ------------- |:-------------:| -----:|
| left up      | 595 | 450 |
| right up     | 690 | 450 |
| right down   | 1135 | 720 |
| left down    | 219  | 720 |

You can see the result in the following image.

![alt text][image3]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

I divided my lane-line finding in 2 functions. The first is the 'fit initial()'-function. This function is used to find the initial points describing the first lan lines (or could find the points if the lines are lost). It is using the sliced windows method. After the function has found the initial points I am using the 'search_around_poly()'-function to find the following lane points. After finding lane points, I am using the 'fit_poly()'-function to approximate a 2nd degree polynom. It is using numpys polyfit function for the approximation. 

![alt text][image4]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in the function 'measure_curvature()' and 'lanepos()'. For the transformation I have read out the parameters from an example picture. The x resolution is 3.7m per 465pixels and the y resolution is 30m per 720 pixels (3x 10m long lines included in my search area).

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

The result is created with the 'map_down()'-function an can be seen in the following picture.

![alt text][image5]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

The implemented pipeline works fine for the project_video but there are some improvements to make. My main aim was to improve the lane detection with a initial search and then with a faster search method. There are some more ideas which would lead to a more robust, better and more efficient result:

- One improvement to make would be more sanity checks if the lines are ok and if not to mark this in the class lines and do a new initial line search
- A sanity check could be also to implement a marge for distance between the lines
- Smoothing over several frames would be necessary to get more robust result e.g. for curvature and lane position
- Parameters could also be improved, I got quite nice results with the parameters from the exercises, I only had to change the margin for the polynominal area search
