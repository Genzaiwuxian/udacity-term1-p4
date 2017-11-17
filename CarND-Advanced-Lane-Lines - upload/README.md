## Advanced Lane Finding
[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)


In this project, your goal is to write a software pipeline to identify the lane boundaries in a video, but the main output or product we want you to create is a detailed writeup of the project.  Check out the [writeup template](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) for this project and use it as a starting point for creating your own writeup.  

Creating a great writeup:
---
A great writeup should include the rubric points as well as your description of how you addressed each point.  You should include a detailed description of the code used in each step (with line-number references and code snippets where necessary), and links to other supporting documents or external references.  You should include images in your writeup to demonstrate how your code works with examples.  

All that said, please be concise!  We're not looking for you to write a book here, just a brief description of how you passed each rubric point, and references to the relevant code :). 

You're not required to use markdown for your writeup.  If you use another method please just submit a pdf of your writeup.

The Project
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

The images for camera calibration are stored in the folder called `camera_cal`.  The images in `test_images` are for testing your pipeline on single frames.  If you want to extract more test images from the videos, you can simply use an image writing method like `cv2.imwrite()`, i.e., you can read the video in frame by frame as usual, and for frames you want to save for later you can write to an image file.  

To help the reviewer examine your work, please save examples of the output from each stage of your pipeline in the folder called `ouput_images`, and include a description in your writeup for the project of what each image shows.    The video called `project_video.mp4` is the video your pipeline should work well on.  

The `challenge_video.mp4` video is an extra (and optional) challenge for you if you want to test your pipeline under somewhat trickier conditions.  The `harder_challenge.mp4` video is another optional challenge and is brutal!

If you're feeling ambitious (again, totally optional though), don't stop there!  We encourage you to go out and take video of your own, calibrate your camera and show us how you would implement this project from scratch!

## How to write a README
A well written README file can enhance your project and portfolio.  Develop your abilities to create professional README files by completing [this free course](https://www.udacity.com/course/writing-readmes--ud777).

# Writeup / README
## Camera Calibration
- Firstly, read into the image by './camera_cal/calibration*.jpg', that "*" represent each file started as calibration, such as calibration01, calibration02...
- Turn image into gray image
- get ret, corners by using cv2.findChessboardCorners
- get ret, mtx, dist, rvecs, tvecs by using cv2.calibrateCamera
- change image into undistor image by using cv2.undistort, last setp we already get mtx and dist matrix
- store the mtx, dist for next example image and pipeline, then vasualizing the distorted and undistorted image
 ![image](https://github.com/Genzaiwuxian/term1-p4/blob/master/CarND-Advanced-Lane-Lines%20-%20upload/output_images/undistorted_image.jpg)
 
## Pipeline (single images)
### 1. Provide an example of a distortion-corrected image.
- Read into one example image, and using mtx, dist to undistort it
 ![image](https://github.com/Genzaiwuxian/term1-p4/blob/master/CarND-Advanced-Lane-Lines%20-%20upload/output_images/undistorted_image1.jpg)

### 2. Using color transforms, gradients methods to create a thresholded binary image.
- define a function called 'abs_sobel_thresh(img, orient='x', sobel_kernel=3, thresh_sobel=(0, 255))' to calculate sobel image
- define a function called 'hls_transform(img, thresh_hls=(90,255))' to change image into HLS S channel and calculating and compare it with thresholds
here are the images:
 ![image](https://github.com/Genzaiwuxian/term1-p4/blob/master/CarND-Advanced-Lane-Lines%20-%20upload/output_images/stacked_thresholds.jpg)
 
### 3. Perspective transform and an example of a transformed image
- define the source and destination points like this, w represent the weight of image (w= img.shape()[1])
src = np.float32([(560,460),
                  (750,460), 
                  (260,700), 
                  (1130,700)])
dst = np.float32([(450,0),
                  (w-450,0),
                  (450,h),
                  (w-450,h)])
- Using the defined function 'unwarp(img, src, dst)' change the img into unwarped image
 ![image](https://github.com/Genzaiwuxian/term1-p4/blob/master/CarND-Advanced-Lane-Lines%20-%20upload/output_images/warped_image.jpg)
 
 ### 4. identify lane-line pixels and fit their positions with a polynomial
 - first, change the warped image into combined(method in item 2) binary image
 - using the historgram to search the maximum pixel column by a pixel box from the bottom line
 - move the box to the next search line, the point is calculated based on the center/maximum pixel point, and start search as above
 - after searching the height of image, using polynomial to fit these selected piexl
here is the picture:
![image](https://github.com/Genzaiwuxian/term1-p4/blob/master/CarND-Advanced-Lane-Lines%20-%20upload/output_images/out_img.jpg)
left red line is left lane line, and yellow one is right lane line, green box is searching box, green line is polynomial line

### calculate the radius of curvature and visulaizing it in the image
using those to calculte the radius of curcature:
left_curverad = ((1 + (2*left_fit[0]*y_eval + left_fit[1])**2)**1.5) / np.absolute(2*left_fit[0])
right_curverad = ((1 + (2*right_fit[0]*y_eval + right_fit[1])**2)**1.5) / np.absolute(2*right_fit[0])
show the curvature date on image
cv2.putText(new_img, text, (40,70), font, 1.5, (200,255,155), 2, cv2.LINE_AA)
![image](https://github.com/Genzaiwuxian/term1-p4/blob/master/CarND-Advanced-Lane-Lines%20-%20upload/output_images/image_output.png)

## Video
the video link is:
https://github.com/Genzaiwuxian/term1-p4/blob/master/CarND-Advanced-Lane-Lines%20-%20upload/project_video_output.mp4

## Discussion
during the video, the curvatue of lane line ks not very good, some number is too big, i think it's maybe not very good during the unwarp process.
Besides, the lane line finding is based on the maximum pixel, so there is a problem, if the lane line is weak, such as dashed line side, during the search process, it may search some non-laneline pixel, such as white car, other side of laneline (near to the dashed line). this need more reliable method.
