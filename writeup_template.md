## Writeup 

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


## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### README
 

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first-fourth cells of the IPython notebook located in "advanced_lane-detevction.ipynb" .  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result can be found in both the notebook and the output images folder.

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

an undistorted image of a 9x6 image of chessboard is provided in output of notebook and output folder

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image. This is same the code from the classroom quiz where we use gradient and color thresholding on the image to get it's binary form. an output image is provided in the output images folder and also shown in notebook

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `warper()` (output_images/perceptive_warped.jpg).  The `warper()` function takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points.  I chose the hardcode the source and destination points in the following manner:

```python
 src = np.float32([[685,450],
                      [1033,671],
                      [262,678],
                      [595,450]])
    
    # destination rectangle points
    dst = np.float32([[1033,0],
                      [1033,671],
                      [262,678],
                      [262,0]])
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 685, 450      | 1033, 0       | 
| 1033, 671     | 1033, 671     |
| 262, 678      | 262, 678      |
| 595, 450      | 262, 0        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

the output image can be found in notebook as well as output images folder

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

The code snippet here is used from the classroom quiz to detect lane pixels. the function is named as find_lane_pixels

steps followed are as follows:
1.Take a histogram of the bottom half of the image
2.Create an output image to draw on and visualize the result
3.Find the peak of the left and right halves of the histogram,These will be the starting point for the left and right lines
4.Set height of windows - based on nwindows above and image shape
5. Identify the x and y positions of all nonzero pixels in the image
6. Create empty lists to receive left and right lane pixel indices\
7. Step through the windows one by one
     Identify window boundaries in x and y (and right and left)
     1.Find the four below boundaries of the window
     2.Draw the windows on the visualization image
     3.Identify the nonzero pixels in x and y within the window
     4. Append these indices to the lists
     5.If you found > minpix pixels, recenter next window on their mean position
     6.Concatenate the arrays of indices (previously was a list of lists of pixels)
     7.Extract left and right line pixel positions
     8.Find our lane pixels first
     9.Fit a second order polynomial to each using np.polyfit()
     10. fit another polymial with scaled version of these values in metres for curvature 
     11.Generate x and y values for plotting
     12.use different Colors in the left and right lane regions
     13.Plots the left and right polynomials on the lane lines
     
this goes for once and later the search_around_poly function takes over which searches in the near vicinity of detected lines
     
The output of this function can be found in the output_images and output of cell.


#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

1.Define conversions in x and y from pixels space to meters
2. Generate x and y values for plotting
3.We'll choose the maximum y-value, corresponding to the bottom of the image
4.calculation of R_curve (radius of curvature) using previously fitted curve scaled for metres.
    left_curverad = ((1 + (2*left_fit_cr[0]*y_eval*ym_per_pix + left_fit_cr[1])**2)**1.5) / np.absolute(2*left_fit_cr[0])
    right_curverad = ((1 + (2*right_fit_cr[0]*y_eval*ym_per_pix + right_fit_cr[1])**2)**1.5) / np.absolute(2*right_fit_cr[0])



#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in the function unwarp_and_draw and the output of this step shows the lane area annotated. the output is also provided in the output_images folder.


---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video_output.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

As it can be seen most of the code is from the lesson's. thanks to her, she's my favourite! I just had to integrate these parts in to a final class and write some code to reuse the fitted lines . the base condition was to search around the lines until there's massive difference in the curvature and if it isn't met then do the search from the scratch. One difficulty is that it still struggles at harder challenge video. I tried fine tuning, but seems this is it for now. please consider the notebook for depth understanding. 
