## Writeup Template


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

[cam-dist]: ./output_images/cam-dist.png
[cam-undist]: ./output_images/cam-undist.png
[undist]: ./output_images/undist.jpg
[bin_flt]: ./output_images/bin_flt.jpg
[fill_poly]: ./output_images/fill_poly.jpg
[persp]: ./output_images/persp.jpg
[with_line]: ./output_images/with_line.jpg
[result]: ./output_images/result.jpg

[image1]: ./examples/undistort_output.png "Undistorted"
[image2]: ./test_images/test1.jpg "Road Transformed"
[image3]: ./examples/binary_combo_example.jpg "Binary Example"
[image4]: ./examples/warped_straight_lines.jpg "Warp Example"
[image5]: ./examples/color_fit_lines.jpg "Fit Visual"
[image6]: ./examples/example_output.jpg "Output"
[video1]: ./project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first code cell of the IPython notebook located in ["./examples/example.ipynb"](./examples/example.ipynb). 

The location of the chessboard corners in the real world for each image is represented in the objpoints variable. The corresponding points in the image are represented using the imgpoints variable. I do the following for each image:
* Convert each image to grayscale
* Find the chessboard corners using cv2.findChessboardCorners()
* If the corners are found, then calibrate the camera based on the image points found using cv2.findChessboardCorners()
* (The image is also undistorted for illustration)

Below is the distorted image followed by the undistorted image:

![cam-dist]
![cam-undist]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![undist]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I converted the color image to HLS and HSV (S -> S2) formats. I applied the Sobel operator to the L, S, V and (S2) spaces and combined it with a binary thresholded S space image. The code for this is present as part of the "Pipeline Definition" cell and the helper methods are present in the "Helper functions for Color Space Conversion and Binary Thresholding" cell. The resultant output looks like this:

![bin_flt]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.
The code for my perpective transform is present in a function called `perspective_transform()` present in the `Undistorion and Perspective Transform` code cell. The hardcoded source and destination points are:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 589, 450      | 210, 0        | 
| 200, 700      | 210, 700      |
| 1175, 700     | 1070, 700      |
| 693, 450      | 1070, 0        |

The warped image looks like this:

![persp_transform]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

I identified the centroids of the window in the warped image (`find_window_centroids()` method in the `Line Finder` cell). Then based on the centroid, I fit the points to a 2nd degree polynomial using the `np.polyfit()` method in the `fit_lines()` method in the `Line Finder` cell. To avoid having to look over the entire image again for new frames, I track the previous fits and restrict the search in the `fit_lines_cont` method in the `Line Finder` cell.

The line identified for the above warped image is:

![with_line]

After unwarping back and filling the polygon, it look like:

![fill_poly]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

The radius of curvature is calculated in the `fit_lines()` and `fit_lines_cont()` methods in the `Line Finder` cell. The offset of the vehicle is calculated in the `pipeline()` method in the `Pipeline Definition` cell.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

The overlaying of the identified lane is done in the `overlay()` method defined in the `Line Finder` cell.

![result]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video_op.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.  
