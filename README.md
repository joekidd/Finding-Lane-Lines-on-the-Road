# Finding Lane Lines on the-Road

The project provides a simplistic lane lines detection algorithm, which applies methods described in the first class, of Udacity Self-Driving Car Nanodegree Program. All the videos from the original task description were process by the algorithm and the result is presented in the video below.

[![Click to view the video](https://img.youtube.com/vi/K4ZBT96A8VQ/0.jpg)](https://www.youtube.com/watch?v=K4ZBT96A8VQ)

Every step of the algorithm's pipeline is presented at the top of the video. It helps both in debugging, analysing and understanding what is actually happening.

## Pipeline

The algorithm consists of the following steps: crop region of interest, run color based segmentation, run canny edge detection, combine results of color based segmentation and canny edge detection, run hough transformation to detect lines, fit linear regression to define the lane lines. Each step is described in more details below.

### Crop ROI

In the task of lane lines detection, we are only interested in the road surface part of the image, therefore, I define a region of interest, which covers this area. I can simply forget about the rest of the picture for now.

### Color based segmentation

The images in the video, are mostly free of noises (except the challenge video), therefore it's quite straight forward to use lane lines colors for segmentation. In order to that, I build HSV models of yellow and white colors, which represent the lines. Then I keep on the image only those pixels, that are described by those models.

### Canny edge detection

Just as described in the class, this step takes as its input the gray and smoothed version of the original image an applies canny edge detection.

### Combine Canny and color segmentation (bitwise_and)

I am interested only in the edges, that relate to the lines of the specific colors. On the other hand, I am also interested only in the white and yellow shapes, whose edges meet some conditions. By combining Canny edge detection, with color segmentation, I am sure, that mentioned requirements are met.

### Hough transform for lines detection

In order to detect actuall lines, we I run hough transformation and extract the lines.

The above steps are pretty basic and doesn't guarantee the algoritm to be robust in each situation. Therefore, instead of processing every video frame separetely, I am using hough lines detected within an arbitrary number of consecutives video frames. It helps to collect more 'line points' and makes the algorithm more robust.

In the simplistic case of the task, I can assume, that there are only two lines, that I should worry about - left and right line of the current lane. It's quite easy to observe, that both lines can be approximated by a linear function and left lane has a different slope than the right one.

Therefore, each detected line is assigned to 'positive slope' lines, or 'negative slope' lines. The slope is simply the parameter 'a' of the basic linear function equation y = a*x + b.

### Linear regression and drawing lines

In this step, I have lines sorted by their slope. For each line I get the 'start' and 'ending' points and those points are then used to approximate the 'solid' line, that is then displayed on the image. Simple linear regression is used to approximate the functions of the lines.

## Potential issues and fixes

1. Not well suited for curvy lines 
2. Color based
3. It won't work well when there is an obstacle over a line
4. 



