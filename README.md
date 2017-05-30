# Finding Lane Lines on the road

[![Click to view the video](https://github.com/joekidd/Finding-Lane-Lines-on-the-Road/blob/master/test_images_output/screen.png)](https://www.youtube.com/watch?v=K4ZBT96A8VQ)

An implementation of the first project of the Udacity Self-Driving Car Nanodegree Program. All the videos from the task description (test_videos directory) were process by the algorithm and the final result is presented in the video above.

## Assumptions and observations

Here is a list of assumptions and observations, which are applied in the lane lines detection algorithm:

1. I consider only current lane lines
2. Lane lines are yellow, or white
3. Current lane lines appear only in certain range of angles
4. Current lane lines might be approximated by a linear function
5. Current lane lines might be distinguished by the slope of linear function
6. I consider only lane lines within defined region of interest

Points (1), (2) and (6) don't need further explanation. Points (3), (4), (5) come from the nature of linear function. As this task, doesn't require considering curvature, linear function y = a * x + b is a sufficient approximation. Left line might be distinguished from the right line by considering signs of slope coefficients. Left line should have positive slope coefficient, right line should have negative one.

The slope coefficient carries also the information about angles. Therefore, they are used to abandon lines, whose angle is further from the mean angle by more than three standard deviations. The angle mean was computed by processing all detected hough lines coressponding to line lanes from each video file. 

## Pipeline

At the top of the video, you may find a visualization of each step of the pipeline. Here you can find a code snippet from the algorithm, which defines the pipeline in a high level way. For more detailed explanation, please scroll down.

```python
def detect(self, image):
  # 1. Color based segmentation
  color_segm_img = self.step_color_segm(image)
  # 2. Detect edges
  canny_edge_img = self.step_canny_edge(image)
  # 3. Combine step 1 and 2
  combined_img = cv2.bitwise_and(color_segm_img, canny_edge_img)
  # 4. Detect hough lines
  hough_lines = self.step_detect_lines(combined_img)
  # 5. Sort lines depending on the sign of the slope 
  n_slope, p_slope = self.step_sort_lines(self.memory)
  # 6. Approximate and display lines
  line_img = self.step_approx_lines(image, n_slope, p_slope)
  weighted_img = cv2.addWeighted(image, 0.8, line_img, 1.0, 0.0)
  # 7. Add annotation if needed
  ...
```

#### Region of interest

In the task of lane lines detection, we are only interested in the road surface part of the image, therefore, I define a region of interest, which covers this area. I can simply forget about the rest of the picture for now. 

#### Color based segmentation

Road surfaces in the videos, are mostly free of noises (except the 'challenge' video). Therefore, it seems reasonable to use lane lines colors for segmentation. In order to do that, I build HSV models of yellow and white colors, which represent the lines. Then, I leave only those pixels, that are described by those models.

#### Canny edge detection

Just as described in the class, this step takes as its input the gray and smoothed version of the original image an applies canny edge detection.

#### Combine Canny and color segmentation

At the moment, I have two different images. The first, containing only pixels with colors of interests, the second, containing only edges.

However, I am interested only in the edges, that relate to the lines of the specific colors. On the other hand, I am also interested only in the white and yellow shapes, whose edges meet some conditions. By combining Canny edge detection, with color segmentation, I am sure, that mentioned requirements are met.

#### Hough transform for lines detection

The output of the previous step is a direct input to the current one - edges representing shapes with specific colors.
In order to detect actual lines, I run hough transformation.

#### Sorting lines

For the purpose of the task, I can assume, there are only two lines, that I should worry about - left and right line of the current lane. Both lines can be approximated by a linear functions, where left line function has a different slope, than the right one.

Therefore, each line detected by hough transform, is assigned to 'positive slope' lines, or 'negative slope' lines. The slope is simply the parameter 'a' of the basic linear function equation y = a*x + b.

The above steps are pretty basic and doesn't guarantee the algoritm to be robust in each situation. Therefore, instead of processing every video frame separetely, I am using hough lines detected within an arbitrary number of consecutives video frames. It helps to collect more 'line points' and makes the algorithm more robust.


#### Linear regression and drawing lines

In this step, I have lines sorted by their slope. For each line I get the 'start' and 'ending' points and those points are then used to approximate the 'solid' line, that is then displayed on the image. Simple linear regression is used to approximate the functions of the lines.

#### Adding annotations
As the last step, that is actually not really a part of the pipeline, I add annotations, that might be seen at the top of the video. It helps analysing, what is really happening under the hood.

## Potential issues and fixes

#### The algorithm is not well suited for curvy lines

The algorithm is approximating the lines using linear functions. Therefore, it won't handle line curvatures. To create more general approach, a different way of aproximating lines would be needed (splines, polynomial, etc).

#### Color based

The color based segmentation is sufficient for the task, however it may be depended on the lightening conditions. Instead, I would suggest to create a filter, that exploits other properties of the lines: 
- contrastive to the road surface
- when transformed to IPM (inverse perspective mapping) the lines become parallel and vertical
- they have similar lenght and width

The filter should detect edges, that meets the above requirements. I hope to achieve that, for the next project of lane finding.

#### Obstacles on lines

When there is a car over a line, the tracing algorithm stops working. Therefore I would need a method, which keeps tracking the lines in such situation. One approach would be to use Kalman filter in order to approximate lines position on the base of previous observation.

#### Tracking only current lane

The algorithm would start creating suspicious results, when the car would try to change lanes. A solution would be to track not only the current lane, but also, adjacent lanes in order to provide smooth lane transition.

#### Highway limited

The algorithm should work well, but only in the conditions when the lines are cleary visible and there are no other artifacts on the road surface (scratches, holes, etc). Also there are no complicated lines patterns, such as appearing in cities.



