## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---

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

[image1]: ./examples/undistort_output.png "Undistorted"
[image2_input]: ./writeup_images/test1_undistorted_input.png "Road Transformed input"
[image2_output]: ./writeup_images/test1_undistorted.png "Road Transformed output"
[image3_sobel]: ./writeup_images/sobel.png "Binary Example sobel"
[image3_saturation]: ./writeup_images/saturation.png "Binary Example saturation"
[image4_src]: ./writeup_images/src_region.png "Warp Example"
[image4_dest]: ./writeup_images/dest_region.png "Warp Example"
[image5]: ./examples/color_fit_lines.jpg "Fit Visual"
[image6]: ./writeup_images/lane_area.png "Output"
[video1]: ./output_images/project_video_output.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first code cell of the IPython notebook located in `submision.ipynb` code cell 2.  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]


### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image2_input]
![alt text][image2_output]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image ( `submision.ipynb` cell 6).  Here's an example of my output for this step.  (note: this is not actually from one of the test images)

![alt text][image3_sobel]
![alt text][image3_saturation]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.


I computed the transformation matrix from in cell 5 of the notebook.
these are the coordinates of the src and destination points. 

```python
pts_src = np.float32( 
    [
        [589.815, 455.645], 
        [696.734,455.645], 
        [914.606, 603.919],
        [386.065, 603.919]
    ])

pts_dest = np.float32( 
    [
        [386.065, 100], 
        [914.606, 100], 
        [914.606, 600],
        [386.065, 600]
    ])

```



I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4_src]
![alt text][image4_dest]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

using thew function `compute_lanes_poly` in the notebook which looks at the histogram of the values 
in the thresholded images. 


![alt text][image5]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.
the curve was computed using the following formula 

``` python 
    curve_radius =  ((1 + (2*A*y + B)**2)**1.5) / np.absolute(2*C)
```
the code can be found in the function compute_curvature
`submission.ipynb`

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in lines # through # in my code in `submission.ipynb` in the function `process_image()`.  Here is an example of my result on a test image:

![alt text][image6]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./output_images/project_video_output.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

- I had some issue on some part of the road where the color was different. I solved this by switching the thresholding function from the sobel operator to using Saturation when those case where detected