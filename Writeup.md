[//]: # (Image References)
[image1]: ./test_images/test4.jpg "Raw image"
[image2]: ./writeup_images/undistorted.jpg "Undistorted"
[image3]: ./writeup_images/binary.jpg "Binary"
[image4]: ./writeup_images/perspective_transform.jpg "Perspective Transform"
[image5]: ./writeup_images/poly_lines.jpg "Polynomial Lines"
[image6]: ./writeup_images/result.jpg "Result"


# Writeup
This is the test image that I will use in this writeup to illustrate each part of the code.

![alt text][image1]

## Calibrating the Camera and Undistorting the Image 
The code for this step is contained in the second code cell of the IPython notebook located in "LLA_code.ipynb". I started by listing the number of inside corners in the x and y directions in the chessboard calibration images. `object points` holds the (x, y, z) coordinates of the chessboard corners in the world, assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image. `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time all chessboard corners are detected in a test image. `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.

Then, using the `cv2.calibrateCamera()` function along with `objpoints` and `imgpoints`, I calculated the distortion coefficients. The undistorted image looks like this:

![alt text][image2]

## Creating a Binary Image
The code for this step is contained in the fourth code cell. The first threshold is `abs_sobel_thresh` which restricts the absolute value of the Sobel in either the x or y direction. The absolute value must be betweeen 30 and 60 in both the x and y directions to fit this threshold. The next threshold is `mag_thresh` which restricts the magnitude of the gradient. The magnitude must be between 90 and 180 to fit this threshold. The next threshold is `dir_thresh` which restricts the direction of the gradient. The direction must be between 0.7 and 1.3 to fit this threshold. This threshold is especially helpful in removing horizontal lines because lane lines are for the most part vertical. The fourth and final threshold is `s_color_thresh` which restricts the saturation of the line using the HLS color scheme. The saturation must be between 90 and 255 to fit this threshold. 

Finally, the `stack_thresholds` function returns back a binary image with the pixels that have fit any of the four thresholds. The final binary image looks like this:

![alt text][image3]

## Transforming the Perspective of the Image
The code for this step is contained in the fifth code cell. The 4 source point coordinates were obtained using the image viewer window. I identified the destination points by adding and subtracting `offset` from the source points and corners of the image. This resulted in the following soure and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 250, 700      | 300, 700      |
| 585, 460      | 300, 0        |
| 700, 460      | 1010, 0       |
| 1060, 700     | 1010, 700     |

I then applied the `cv2.getPerspectiveTransform` and `cv2.warpPerspective` functions and got the following result:

![alt text][image4]

## Indentifying the Lanes and Fitting Polynomials
The code for this step is contained in the sixth code cell. I identified tha lane pixels using the sliding windows technique. I created a histogram to find the starting x coordinates for the lanes and to find the midpoint. `nonzero` contains the x and y coordinates of all nonzero pixels in the image, while `left_lane_inds` and `right_lane_inds` will receive the lane pixel indices. 

The code loops through each window, defining the boundaries and identifying the nonzero pixels in the x adnd y directions. These indiced are then added to `left_lane_inds` and `right_lane_inds`. The next window will be repositioned if more than `minpix` have been found. `left_lane_inds` and `right_lane_inds` are concatenated with themselves and the pixel positions are extraced into `leftx`, `lefty`, `rightx`, and `righty`. 

Using the `np.polyfit` function, the left and right fits are calculated (`left_fit` and `right_fit`, respectively). Then, x and y values for plotting are calculated and the following results are produced:

![alt text][image5]

## Calculating Radius of Curvature and Lane Deviation
The code for this step is contained in the seventh code cell. I converted the lane polynomials from pixels to meters in `left_fit_cr` and `right_fit_cr`. I then used the equation found in the lesson to calculate the radii `left_curverad` and  `right_curverad`. 

To find the lane deviation, I found the left and right polynomial x intercepts. Using those as the left ane right lane points, I found the center by averaging them. The camera is located at the center of the car, so I calculated that by dividing the width of the image by 2. The deviation is simply the difference of the center of the lane and the center of the car.

## Putting Information onto Original Image]
The code for this step is contained in the eighth code cell. I used the left and right polynomials of the line along with the `cv2.fillPoly` function to draw the green polygon. I then used the inverse matrix and the `cv2.warpPerspective` function to unwarp the image. I then used `cv2.addWeighted` to add everything to the original image.

To write the text, I rounded the numbers and used `cv2.putText`. I determined if the car was left or right of the center of the lane to make the words on the screen display differently. Combining everything yields this final result:

![alt text][image6]

## Applying to a Video - Final Result
Here's a [link to my video result](./output_video.mp4)

## Discussion
The biggest challenge in this project was the polynomial. It is manageable to do the problem on paper, but converting the math into Python was difficult for me. The pipeline is fairly resilient, however, I think it would fail at night when the camera is reliant on the car's headlights. This would make the camera pickup a lot less information, and would therefore produce a sloppier result. To tackle this, a sensor would have to be used that doesn't use light like a camera. This would most likely be in the form of some sort of radar.
