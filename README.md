

**Advanced Lane Finding Project**
---

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

[image1]: ./output_readme/chessboard_undistort.png "chess undistort"
[image2]: ./output_readme/road_undistort.png "road undistort"
[image3]: ./output_readme/thresholding.png "thresholding"
[image4]: ./output_readme/points.png "point map"
[image5]: ./output_readme/warped.png "binary warp"
[image6]: ./output_readme/warped2.png "road warp"
[image7]: ./output_readme/hist.png "histogram"
[image8]: ./output_readme/fit.png "fit"
[image9]: ./output_readme/result.png "end result"
[video1]: ./test_Trim.mp4 "Video"

#### Step 1: Camera Calibration and distortion correction 

The camera was calibrated using chessboard images that was taken from the camera. Calibration of image taken can be done by finding the corners of the chessboard (image points) ,using `cv2.findChessboardCorners()`, and comparing it to the standard coordinates of chessboard corners (object points).  
The distortion coefficient (dst) and camera matrix (mtx) can then be computed using `cv2.calibrateCamera()` with the inputs of the image points and object points. We can now undistort any images that was taken by this camera using `cv2.undistort`. Examples of undistorted examples:  

![alt text][image1]

![alt text][image2]
 
#### Step 2: Color and Gradient Thresholding 
 
The color space was converted to HLS and Saturation was selected as the specific color layer that we are thresholding (filtering). The selection was done based off plotting the image on a few different color spaces and testing various thresholds. The best selection was determined by the prominence of the lane lines in the image. Color thresholding is very important as we can filter away unwanted noise such as shadows and also extract lane lines features.

The gradient thresholding was done using Sobel operators. Both color and gradient thresholds returns a binary image which was then combined using a `bitwise OR operator`. The individual plots for gradient threshold and color threshold can be seen below. The combined thresholds are of satisfactory standard as the lane lines are clearly defined. Gradient thresholding can be seen in the code under the `function combined_grad` while color thresholding is in the `function color_threshold`. (Cell 5) 

![alt text][image3]


#### Step 3: Perspective Transform 
 
The binary image from the threshold filtering was then warped to get a bird-eye view of the lane lines. This is necessary to detect curve lane lines and compute the polynomial equation of the curve lane line.  Firstly, we can look to find 4 coordinates points (source coordinates) to form the a trapezoidal shaped region of interest around the lane lines to be warped. The images below show how we can gauge the coordinates for the region of interest by manipulating the position of the four dots to sufficiently contain both lane lines.

![alt text][image4]



Once we are happy with the source coordinates, we can warp it to the destination coordinates. The destination coordinates are determined by trial and error to get a bird-eye view image with the lane lines clearly defined and also removing the noise such as the divider at the side.

With the source and destination coordinates, we can use the functions `cv2.getPerspectiveTransform` to get the transformation matrix required for warping.  The function `cv2.warpPerspective` was then use by inputting the transformation matrix found to produce the warped image. The images below shows the warped image of the road and the binary image formed after thresholding. 

![alt text][image5]
![alt text][image6]

#### Step 4: Identifying lane-line pixels and fitting polynomial 
 
Using the warped binary image, we can then apply the sliding windows technique to find the center of the pixels (lines) along the y-axis. The starting point of the sliding window was determined by finding the points where the pixel intensity is the highest. This can be visualized by plotting the graph with y-axis being the amount of white pixels:
 
 ![alt text][image7]
 
It can be observed that there are 2 obvious peaks where most pixels are located that gives us the starting point for the sliding window. We can determine the center line of the lanes through the sliding windows. With the coordinates of the pixel positions, the function `np.polyfit` can be used to produce the coefficient of the quadratic equation. The results was visualized as seen below: 


![alt text][image8]
 
 
#### Step 5: Determining radius curvature of lane lines and the position of vehicle with respect to the center  
 
As we now know where the lane lines are, we can estimate how much the road is curving and where is the vehicle located with respect to the center of the lane. Note that the pixels in the image are converted to meters for calculations. The radius of curvature was calculated  in the function `find_lane` (Cell 10) and the center offset of  the vehicle is found in the function `drawLine`(Cell 11). The center offset is determined by finding the difference between the center of the image to the center of the lanes. 
 
The end result of this project for an image: 

![alt text][image9]

## Pipeline (Applying to a Video stream):
---

Here's a ![link to my video result](./test_Trim.mp4 ).

Note: GitHub cannot open such a large mp4 file, you can download it to play.

### Improvements for the future
---

Current solution is able to deal with shadows and different shade of road colour pretty well as color thresholding has been done well. However, the major issues arises with the **fixed region of interest** indicates that the code will not do well in detecting lane lines on roads that has slighty narrow curves. This can be fixed by sharing the curvature and sliding windows outputs to the region of interest. For example, if the sliding windows is having trouble detecting the lane lines and the curvature before was decreasing at a rapid rate (narrow turn), we can have the region of interest to expand accordingly. 

Aside from that, the code is done through procedural style programming. An Object Oriented Style of programming will be better to tackle this problem. We can create a LaneLine() class with all of the attributes of lane lines which gives us better organisation of code and also it will be easier to keep track of both attributes of the lane lines and update the sliding windows based off past data.


### Conclusion
---

A more dynamic approach is neccesary to solve many of the challenges a self driving car will face on the road especially since the environment of the road is never fixed and many unforeseen issues may arise. 

Due to the nature of the problem, I believe that the end goal/solution of a self driving car is one with  AI capabilities that **truly understand** how to detect lane lines just as how humans like us do and not through a manual search. Sensor Fusion will be a key to this problem for self-driving cars as information are collected from various sensors has to be combined for the car to make decisions just like how the brain combines information from our sense of sight,touch,hearing,et cetera, to formulate an action plan.

I am very excited to explore more especially in the area of Deep Learning and Sensor Fusion, stay tune for more projects!




 
 
 

