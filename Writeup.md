[image1]: ./test_images/test4.jpg "Raw image"
[image2]: ./writeup_images/undistorted.jpg "Undistorted"

# Writeup
This is the test image that I will use in this writeup to illustrate each part of the code.

![alt text][image1]

## Calibrating the Camera and Undistorting the Image 
The code for this step is contained in the second code cell of the IPython notebook located in "LLA_code.ipynb". I started by listing the number of inside corners in the x and y directions in the chessboard calibration images. `object points` holds the (x, y, z) coordinates of the chessboard corners in the world, assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image. `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time all chessboard corners are detected in a test image. `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.

Then, using the `cv2.calibrateCamera()` function along with `objpoints` and `imgpoints`, I calculated the distortion coefficients. The undistorted 

![alt text][image2]
