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

[undistored_image1]: ./examples/undistort_output.png "Undistorted"
[road_image1]: ./test_images/undistort_road1.jpg "Road Transformed1"
[road_image2]: ./test_images/undistort_road2.jpg "Road Transformed2"
[road_image3]: ./test_images/undistort_road3.jpg "Road Transformed3"
[road_image4]: ./test_images/undistort_road4.jpg "Road Transformed4"
[road_image5]: ./test_images/undistort_road5.jpg "Road Transformed5"
[road_image6]: ./test_images/undistort_road6.jpg "Road Transformed6"
[road_image7]: ./test_images/undistort_road7.jpg "Road Transformed7"
[road_image8]: ./test_images/undistort_road8.jpg "Road Transformed8"
[binary_image1]: ./examples/binary_road1.png "Binary Example1"
[binary_image2]: ./examples/binary_road2.png "Binary Example2"
[binary_image3]: ./examples/binary_road3.png "Binary Example3"
[binary_image4]: ./examples/binary_road4.png "Binary Example4"
[binary_image5]: ./examples/binary_road5.png "Binary Example5"
[binary_image6]: ./examples/binary_road6.png "Binary Example6"
[binary_image7]: ./examples/binary_road7.png "Binary Example7"
[binary_image8]: ./examples/binary_road8.png "Binary Example8"
[transform_image1]: ./examples/transform_road1.png "Warp Example1"
[transform_image2]: ./examples/transform_road2.png "Warp Example2"
[transform_image3]: ./examples/transform_road3.png "Warp Example3"
[transform_image4]: ./examples/transform_road4.png "Warp Example4"
[transform_image5]: ./examples/transform_road5.png "Warp Example5"
[transform_image6]: ./examples/transform_road6.png "Warp Example6"
[transform_image7]: ./examples/transform_road7.png "Warp Example7"
[transform_image8]: ./examples/transform_road8.png "Warp Example8"
[fit_image1]: ./examples/fit_line1.jpg "Fit Visual1"
[fit_image2]: ./examples/fit_line2.jpg "Fit Visual2"
[fit_image3]: ./examples/fit_line3.jpg "Fit Visual3"
[fit_image4]: ./examples/fit_line4.jpg "Fit Visual4"
[fit_image5]: ./examples/fit_line5.jpg "Fit Visual5"
[fit_image6]: ./examples/fit_line6.jpg "Fit Visual6"
[fit_image7]: ./examples/fit_line7.jpg "Fit Visual7"
[fit_image8]: ./examples/fit_line8.jpg "Fit Visual8"
[image6]: ./examples/example_output.jpg "Output"
[video1]: ./project_out.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first code cell of the IPython notebook located in "./lane_finding.ipynb"  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][undistored_image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

I use cv2.undistort to distort images from front camera of car. To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like these:
![alt text][road_image1]
![alt text][road_image2]
![alt text][road_image3]
![alt text][road_image4]
![alt text][road_image5]
![alt text][road_image6]
![alt text][road_image7]
![alt text][road_image8]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image. I filter S channel from HLS and filter x, y gradient and gradient magnitude. Here are examples of my output for this step.

![alt text][binary_image1]
![alt text][binary_image2]
![alt text][binary_image3]
![alt text][binary_image4]
![alt text][binary_image5]
![alt text][binary_image6]
![alt text][binary_image7]
![alt text][binary_image8]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `warper()`, which appears in lines 1 through 8 in the file `example.py` (output_images/examples/example.py) (or, for example, in the 3rd code cell of the IPython notebook).  The `warper()` function takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points.  I chose the hardcode the source and destination points in the following manner:

```python
src = np.float32(
    [[(img_size[0] / 2) - 55, img_size[1] / 2 + 100],
    [((img_size[0] / 6) - 10), img_size[1]],
    [(img_size[0] * 5 / 6) + 60, img_size[1]],
    [(img_size[0] / 2 + 55), img_size[1] / 2 + 100]])
dst = np.float32(
    [[(img_size[0] / 4), 0],
    [(img_size[0] / 4), img_size[1]],
    [(img_size[0] * 3 / 4), img_size[1]],
    [(img_size[0] * 3 / 4), 0]])
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 585, 460      | 320, 0        | 
| 203, 720      | 320, 720      |
| 1127, 720     | 960, 720      |
| 695, 460      | 960, 0        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][transform_image1]
![alt text][transform_image2]
![alt text][transform_image3]
![alt text][transform_image4]
![alt text][transform_image5]
![alt text][transform_image6]
![alt text][transform_image7]
![alt text][transform_image8]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Then I did some other stuff and fit my lane lines with a 2nd order polynomial. I also use previous fit to find new lane line in coming frame and smooth this fitting line with average method. 

![alt text][fit_image1]
![alt text][fit_image2]
![alt text][fit_image3]
![alt text][fit_image4]
![alt text][fit_image5]
![alt text][fit_image6]
![alt text][fit_image7]
![alt text][fit_image8]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in lines with radius calculation formula and also claculate offset of position. I plot these information in the left bottom of video.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

Here is a image from plotted back down onto the road:

![alt text][image6]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_out.mp4)
