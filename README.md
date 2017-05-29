# Finding Lane Lines on the-Road

An implementation of the first project of the Udacity Self-Driving Car Nanodegree Program. All the videos from the task description were process by the algorithm and the final result is presented in the video below.

[![Click to view the video](https://img.youtube.com/vi/K4ZBT96A8VQ/0.jpg)](https://www.youtube.com/watch?v=K4ZBT96A8VQ)

At the top of the video, you may find a visualization of each step of the pipeline.

## Pipeline

Each step of the algorithm is described below.

#### Region of interest

In the task of lane lines detection, we are only interested in the road surface part of the image, therefore, I define a region of interest, which covers this area. I can simply forget about the rest of the picture for now.

#### Color based segmentation

The images in the videos, are mostly free of noises (except the 'challenge' video). Therefore, it seems reasonable to use lane lines colors for segmentation. In order to do that, I build HSV models of yellow and white colors, which represent the lines. Then, I leave on the image only those pixels, that are described by those models.

#### Canny edge detection

Just as described in the class, this step takes as its input the gray and smoothed version of the original image an applies canny edge detection.

#### Combine Canny and color segmentation

At the moment, I have two different images. The first, containing only pixels with colors of interests, the second, containing only edges.

However, I am interested only in the edges, that relate to the lines of the specific colors. On the other hand, I am also interested only in the white and yellow shapes, whose edges meet some conditions. By combining Canny edge detection, with color segmentation, I am sure, that mentioned requirements are met.

#### Hough transform for lines detection

The output of the previous step is a direct input to the current one - edges representing shapes with specific colors.
In order to detect actual lines, I I run hough transformation.

For the purpose of the task, I can assume, there are only two lines, that I should worry about - left and right line of the current lane. Both lines can be approximated by a linear functions, where left line function has a different slope, than the right one.

Therefore, each line detected by hough transform, is assigned to 'positive slope' lines, or 'negative slope' lines. The slope is simply the parameter 'a' of the basic linear function equation y = a*x + b.

The above steps are pretty basic and doesn't guarantee the algoritm to be robust in each situation. Therefore, instead of processing every video frame separetely, I am using hough lines detected within an arbitrary number of consecutives video frames. It helps to collect more 'line points' and makes the algorithm more robust.


#### Linear regression and drawing lines

In this step, I have lines sorted by their slope. For each line I get the 'start' and 'ending' points and those points are then used to approximate the 'solid' line, that is then displayed on the image. Simple linear regression is used to approximate the functions of the lines.

## Potential issues and fixes

1. Not well suited for curvy lines 
2. Color based
3. It won't work well when there is an obstacle over a line
4. What if the car changes lines?



