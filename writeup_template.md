## Advanced Lane Finding Project

The goal of this project is to develop a pipeline to process a video stream from a forward-facing camera mounted on the front of a car and output an annotated video which identifies:

    The positions of the lane lines,
    the location of the vehicle relative to the center of the lane,
    the radius of curvature of the road.

    Requirements: 
        1) Compute the camera calibration matrix and distortion coefficients given a set of chessboard images. 
        2) Apply a distortion correction to raw images. 
        3) Apply a perspective transform to rectify image ("birds-eye view"). 
        4) Use color transforms, gradients, etc., to create a thresholded binary image. 
        5) Detect lane pixels and fit to find the lane boundary. 
        6) Determine the curvature of the lane and vehicle position with respect to center. 
        7) Warp the detected lane boundaries back onto the original image. 
        8) Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[image1]: ./output_images/cam_calibration.png "Camera Calibration"
[image2]: ./output_images/distor.png "Distortion correction"
[image3]: ./output_images/perspective.png "Perspective transform"
[image4]: ./output_images/treshold.png "Color (gradient) threshold"
[image5]: ./output_images/curviture.png  "Fit a polynomial"
[image6]: ./output_images/chalange.png  "Video"

### 1. Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the second code cell of the IPython notebook located in "advanced_lane_lines_pipeline.ipynb".

The first step to detect lane line efficiently is to calibrate camera and then remove any distortion from the images after calculating the camera calibration matrix and distortion coefficients using a series of chessboard images. 

![alt text][image1]

## 2. Distortion correction

The code for this step is contained in the fourth code cell of the IPython notebook located in "advanced_lane_lines_pipeline.ipynb".

Here I define a function to undistort images which uses previously calculated camera calibration matrix and distortion coefficients to remove distortions from images and output more realistic undistorted image.

![alt text][image2]

## 3. Perspective transform

The code for this step is contained in the sixth code cell of the IPython notebook located in "advanced_lane_lines_pipeline.ipynb".

In this step I will define a function birdsEye() which transforms the undistorted image of the road to a "birds eye view" which focuses only on the lane lines and displays them in such a way that they appear to be relatively parallel to eachother. This will make it easier later on to fit polynomials to the lane lines and measure the curvature.

![alt text][image3]

## 4. Color (gradient) threshold

The code for this step is contained in the eight code cell of the IPython notebook located in "advanced_lane_lines_pipeline.ipynb".

Here I convert all undistorted warped images into a better color space and then create a binary gradient threshhold which focuses more on the lane lines and highlights them only. Here I represent the best color channels and thresholds I discovered by completing all the the lessons:

The S channel from the HLS color space

Minimum: 180
Maximum: 255
(+) Best for both yellow and white lane lines
(-) Did not pick up 100% of pixles
The L channel from the LUV color space

Minimum: 255
Maximum: 255
(+) Best for white lane lines
(-) Not good for yellow lane lines
The B channel from the LAB color space

Minimum: 155
Maximum: 200
(+) Best for white yellow lines
(-) Not good for white lane lines
In function below, combined binary threshold is used in regard of getting the best results.

![alt text][image4]

## 5 Fit a polynomial to the lane line & Calculate vehicle position & Calculate lane line curviture

![alt text][image5]

# Video Processing Pipeline

First I am going to introduce the class Line() for the lines to store attributes for the lane lines from one frame to the next. Inside the class I will define several functions which will be used to detect the pixels belonging to each lane line. This code is located in some of last cells in the "advanced_lane_lines_pipeline.ipynb".

Next I create a function processVideo() which processes a video frame by frame and outputs an annotated video with lane lines, radius of curvature and distance from center.

The video processing pipeline is very similar to the fillLanes() function established earlier in the report, except that the video pipeline stores information about the lane lines across frames to average the lane positions and ensure a smooth output which is less impacted by outliers.

The video pipeline also knows whether or not the lane line was detected in the previous frame, and if it was, it only checks for lane pixels in a tight window around the previous polynomial, ensuring a high confidence detection. If the lane line was not detected in the previous frames (and for the first 5 frames of the video) The pipeline performs the same search which was performed in the fillLanes() function based on identifying peaks in a histogram.

![alt text][image6]
