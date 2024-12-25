# Udacity - CarND - Advanced Lane Finding

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

[undistorted1]: ./output_images/undistorted1.png "Undistorted"
[undistorted2]: ./output_images/undistorted2.png "Undistorted"

[RGB_threshold1]: ./output_images/RGB_threshold1.png "RGB_threshold"
[RGB_threshold2]: ./output_images/RGB_threshold2.png "RGB_threshold"

[HLS_threshold1]: ./output_images/HLS_threshold1.png "HLS_threshold"
[HLS_threshold2]: ./output_images/HLS_threshold2.png "HLS_threshold"

[sobel_absolute]: ./output_images/sobel_absolute.png "Sobel absolute" 
[sobel_magnitude]: ./output_images/sobel_magnitude.png "Sobel magnitude" 
[sobel_direction]: ./output_images/sobel_direction.png "Sobel direction"
[LAB_B_threshold]: ./output_images/LAB_B_threshold.png "LAB B"

[test_image]: ./test_images/test4.jpg "Road Transformed"

[binary_and_warped]: ./output_images/binary_and_warped.png "Road Transformed"

[polyfit1]: ./output_images/polyfit1.png "Sliding window"
[polyfit2]: ./output_images/polyfit2.png "Polynomial fit"

[final]: ./output_images/Final.png "Detected lane"


### Camera Calibration

#### 1. Computation of the camera matrix and distortion coefficients

* Camera calibration is defined in the `Calibarator` class in the `pipeline.ipynb`
* I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the 9x6 chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.
* Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  
* I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![Undistorted][undistorted1]

* For road images, the distortion corrections are a bit subtle and observable towards the bottom of the image 

![Undistorted][undistorted2]

### Pipeline (single images)

#### 1. Example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][test_image]

#### 2. Use of color transforms, gradients or other methods to create a thresholded binary image. 

I used a combination of color and gradient thresholds to generate a binary image (thresholding steps are defined in the `Thresholds` class).  Here's an example of my output for this step.  (note: this is not actually from one of the test images)

* Thresholding over RGB

![RGB_thresholds][RGB_threshold1]
![RGB_thresholds][RGB_threshold2]

* Thresholding over HLS

![HLS_threshold][HLS_threshold1]
![HLS_threshold][HLS_threshold2]

* Thresholding Sobel absolute
![sobel_absolute][sobel_absolute]

* Thresholding Sobel magnitude
![sobel_magnitude][sobel_magnitude]

* Thresholding Sobel direction
![sobel_direction][sobel_direction]

* Thresholding LAB B
![LAB_B_threshold][LAB_B_threshold]

* Since R in the RGB gives the clearest lane lines, I use RGB R with threshold (200, 255)
* Since S in the HLS gives the clearest lane lines, I use HLS S with threshold (120, 255)
* Since Sobel absolute (very similar to LUV L) gives clearer lane lines on the dark surface, I use this with threshold (10, 100)
* Since LAB B detects the white lines clearer, I use LAB B with threshold (155-200) 

#### 3. Performing perspective transform.

The code for my perspective transform includes a function called `perspective_transformed(img)`, which appears in lines `pipeline` class.  Using source (`src`) and destination (`dst`) points it computes the transformation and inverse transformation matrices.  

```python
src = np.float32([[690,450],[1110,img_size[1]],[175,img_size[1]],[595,450]])
offset = 300 # offset for dst points
dst = np.float32([[img_size[0]-offset, 0],[img_size[0]-offset, img_size[1]],
                  [offset, img_size[1]],[offset, 0]])
```

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

* Conversion to binary and then warped:
![binary and warped][binary_and_warped]


#### 4. Identifying lane-line pixels and fit their positions with a polynomia

Then I did some other stuff and fit my lane lines with a 2nd order polynomial kinda like this:

![polyfit1][polyfit1]

![polyfit1][polyfit2]

#### 5. Calculating the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in the `radius_of_curvature_and_center_distance(...)` function in the `pipeline` class

#### 6. An example image of the result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in `draw_lane(...)` function in the `pipeline` class.  Here is an example of my result on a test image:

![final][final]

---
### Pipeline (video)

#### 1. A link to your final video output.  

Here's a [link to my video result](./project_video_ouput.mp4)

---

