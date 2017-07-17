# **Finding Lane Lines on the Road** 

## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[solidWhiteCurve-segments]: ./test_images_output/solidWhiteCurve-segments.jpg "solidWhiteCurve-segments"
[solidWhiteRight-segments]: ./test_images_output/solidWhiteRight-segments.jpg "solidWhiteRight-segments"
[solidYellowCurve-segments]: ./test_images_output/solidYellowCurve-segments.jpg "solidYellowCurve-segments"
[solidYellowCurve2-segments]: ./test_images_output/solidYellowCurve2-segments.jpg "solidYellowCurve2-segments"
[solidYellowLeft-segments]: ./test_images_output/solidYellowLeft-segments.jpg "solidYellowLeft-segments"
[whiteCarLaneSwitch-segments]: ./test_images_output/whiteCarLaneSwitch-segments.jpg "whiteCarLaneSwitch-segments"

[solidWhiteCurve]: ./test_images_output/solidWhiteCurve.jpg "solidWhiteCurve"
[solidWhiteRight]: ./test_images_output/solidWhiteRight.jpg "solidWhiteRight"
[solidYellowCurve]: ./test_images_output/solidYellowCurve.jpg "solidYellowCurve"
[solidYellowCurve2]: ./test_images_output/solidYellowCurve2.jpg "solidYellowCurve2"
[solidYellowLeft]: ./test_images_output/solidYellowLeft.jpg "solidYellowLeft"
[whiteCarLaneSwitch]: ./test_images_output/whiteCarLaneSwitch.jpg "whiteCarLaneSwitch"

[solidWhiteCurve-weighted]: ./test_images_output/solidWhiteCurve-weighted.jpg "solidWhiteCurve-weighted"
[solidWhiteRight-weighted]: ./test_images_output/solidWhiteRight-weighted.jpg "solidWhiteRight-weighted"
[solidYellowCurve-weighted]: ./test_images_output/solidYellowCurve-weighted.jpg "solidYellowCurve-weighted"
[solidYellowCurve2-weighted]: ./test_images_output/solidYellowCurve2-weighted.jpg "solidYellowCurve2-weighted"
[solidYellowLeft-weighted]: ./test_images_output/solidYellowLeft-weighted.jpg "solidYellowLeft-weighted"
[whiteCarLaneSwitch-weighted]: ./test_images_output/whiteCarLaneSwitch-weighted.jpg "whiteCarLaneSwitch-weighted"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline followed the steps taught in the lesson, as follows:

1. Convert the image to grayscale

2. Apply a gaussian blur to the image

`kernel_size = 3`

3. Use Canny edge detection to isolate the edges

```
low_threshold = 50
high_threshold = 150
```

4. Apply a mask to the image

`vertices = (0,imshape[0]), (imshape[1]/2-20, imshape[0]/2+55), (imshape[1]/2+20, imshape[0]/2+55), (imshape[1],imshape[0])`

This created a trapezoidal mask with vertices at (0, 540), (460, 325), (500, 325), and (960, 540). 

5. Find lines in the masked image using the Hough transform

```
rho = 2
theta = np.pi/180
threshold = 20
min_line_length = 10
max_line_gap = 10
```

The initial pipeline was straightforward to implement, as I used the helper functions provided. I spent a bit of time trying to optimize the parameters to achieve the best result, and I ultimately settled on the values indicated above. The resulting images before modification of the `draw_lines` functions are below.

![alt text][solidWhiteCurve-segments]
![alt text][solidWhiteRight-segments]
![alt text][solidYellowCurve-segments]
![alt text][solidYellowCurve2-segments]
![alt text][solidYellowLeft-segments]
![alt text][whiteCarLaneSwitch-segments]

In order to draw a single line on the left and right lanes, I modified the `draw_lines` function as follows. For each line segment, I found the slope and the value of b such that the I had an equation of a line in the form of `y = mx + b`. If the value of the slope was between 0.5 and 1.5, I designated this line as part of the left lane. If the value of the slop was between -1.5 and -0.5, then it was part of the right lane.

I already had the minimum and maximum y values for the left lane line segment and the right lane line segment, which I set as the minimum and maximum values of the mask. I then just had to calculate the x values for each line segment at the minimum and maximum y values using the m and b values we found earlier. For each line segment, I added all of these x values into an array, and averaged them once we had processed all of the line segments.

This gave me the results below.

![alt text][solidWhiteCurve]
![alt text][solidWhiteRight]
![alt text][solidYellowCurve]
![alt text][solidYellowCurve2]
![alt text][solidYellowLeft]
![alt text][whiteCarLaneSwitch]

I applied the pipeline to the video files, and while the lane lines seemed to be correctly identified, the lines were jittery and jumped around, more so than the example video. To attempt to fix this, I modified the `draw_lines` function to use a weighted average instead of just an average. The new algorithm gives added weight to longer lines, rather than equal weight to all lines regardless of length. This seemed to improve the lane-finding algorithm, and improved the jitter on the videos. The results on the images are below. Compared to the original results, these lines seemed to be more accurate.

![alt text][solidWhiteCurve-weighted]
![alt text][solidWhiteRight-weighted]
![alt text][solidYellowCurve-weighted]
![alt text][solidYellowCurve2-weighted]
![alt text][solidYellowLeft-weighted]
![alt text][whiteCarLaneSwitch-weighted]

The videos using the weighted average algorithm are suffixed with `-weighted`.

However, there was still a jitter apparent on the videos. I decided to implement a rolling average of the x values using the latest 10 values of x, combined with using the weighted average algorithm to get the x values for each new frame. This resulted in a very nice, smooth result in the videos.

The videos using the buffer and weighted average are suffixed with `-buffered`.

### 2. Identify potential shortcomings with your current pipeline

The algorithm worked very well on the `solidWhiteRight.mp4` video and the `solidYellowLeft.mp4` video. It seemed to work adequately on the `challenge.mp4` video. However, there were still some moments where the algorithm would be confused by the presence of other lines within the mask, and the lane lines would veer out of place.

The algorithm also relied on set values for the mask polygon and for the range of slopes to determine if a line was on the left or right side. Setting these values makes this algorithm likely to be unsuitable for situations other than when a car is within a lane that fits within the mask.

### 3. Suggest possible improvements to your pipeline

The algorithm can potentially be improved as follows:

- Using a color filter early on in the pipeline to isolate lane lines. This might help with the challenge video, since there were shadows and other lines that tended to skew the result.

- Doing additional tweaking of the parameters to find an optimal set of parameters for the mask size, slope ranges, gaussian blur, edge detection, and hough transform algorithms.

- Cosmetically, the algorithm can be adjusted so that the drawn lines do not overlap, as they do in the challenge result.

- The buffer currently is implemented extremely naively, in that all the historical values of x are stored. The buffer can be implemented to discard unneeded values to free up memory.
