# **Finding Lane Lines on the Road** 

The goal of this project is to develop a pipeline that finds lane lines on the road using image processing techniques.

## Required Files

1. __*Have all project files been included with the submission?*__

	* The IPython Notebook containing the lane detection pipeline can be found at [P1.ipynb](P1.ipynb)
	* Output video files from the lane detection pipeline can be found in [test_videos_output](./test_videos_output)
		1. [solidWhiteRight.mp4](./test_videos_output/solidWhiteRight.mp4)
		1. [solidYellowLeft.mp4](./test_videos_output/solidYellowLeft.mp4)
		1. [challenge.mp4](./test_videos_output/challenge.mp4)

[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---
## Lane Finding Pipeline

1. __*Does the pipeline for line identification take road images from a video as input and return an annotated video stream as output?*__
	
	The output video files in [test_videos_output](./test_videos_output) are an annotated version of the input video file in [test_videos](./test_videos).
	For each frame, the predicted lane lanes are overlayed in red color over the original frame

1. __*Has a pipeline been implemented that uses the helper functions and / or other code to roughly identify the left and right lane lines with either line segments or solid lines?*__

	A pipeline has been implemented in [P1.ipynb](P1.ipynb) in code cell [5]. It applies the following sequence using the predefined helper functions defined in code cell [3] to identify line segments of the lane lines:
	1. `grayscale(img)`: <br>
	Converts the input image to a grayscale image required by the rest of the pipeline

	1. `gaussian_blur(img, kernel_size)`: <br>
	Applies a Gaussian Blur filter to the grayscale image to smooth out unwanted egdes
	
	1. `canny(img, low_threshold, high_threshold)`: <br>
	Applies a Canny transform to the input image according to the given thresholds to detect edges of the lane lines.
	
	1. `region_of_interest(img, vertices):` <br>
	Apply a Region Of Interest Mask on the preprocessed image to remove unneeded details above the horizon and on the sides of the lanes
	
	1. `hough_lines(img, rho, theta, threshold, min_line_len, max_line_gap):` <br>
	Applies a Probabilistic Hough Line Transform to the preprocessed image to detect line segments of lanes.
	

1. __*Have detected line segments been filtered / averaged / extrapolated to map out the full extent of the left and right lane boundaries?*__
	
	The implementation of the helper function `draw_lines(img, lines, color=[255, 0, 0], thickness=10):` has been enhanced to filter the hough lane line segments.
	The function takes in as an input `lines` all the hough lines detected by the pipeline and does the following:
	1. Filters out hough lines whose slopes are less than a certain threshold `HOUGH_LINE_FILTER_MIN_SLOPE = 0.5` = 45 degrees.
	Thus it filters out nearly horizontal lines.
	1. Separates left lane hough lines from right lane hough lines depending on the coordinates of the points defining these lines.
	1. Calculates the average of all filtered hough lines for each lane (left/right)
	1. Draws two solid lines extrapolate the detected hough lines and represent an average of the lane lines.

## Reflection

1. Has a thoughtful reflection on the project been provided in the notebook?

	The implemented pipeline described above has the following shortcomings:
	* It assumes that a lane is a straight line which results in a bad approximation if the lane curvature is above a certain limit. This shows clearly in the challenge video.<br>
	A better solution would be to use a curve fitting function to fit a higher order polynomial to the detected lane lines (points) instead of a 1st order linear polynomial currently implemented.
	* The overlayed lanes are calculated every frame from scratch and some frames have higher errors depending on the location of the dashed lines (closer dashed lines give a lower error). And some frames in the challenge video have one or both lanes missing entirely.<br>
	An enhancement could be to keep a history of previous lane estimations to reduce the estimmation error and to be able provide an estimate even when the lane detection pipeline cannot provide one.
