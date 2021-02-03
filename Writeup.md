[//]: # (Image References)
[image1]: ./test_images/test4.jpg "Raw image"
[image2]: ./writeup_images/undistorted.jpg "Undistorted"
[image3]: ./writeup_images/binary.jpg "Binary"
[image4]: ./writeup_images/perspective_transform.jpg "Perspective Transform"

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
