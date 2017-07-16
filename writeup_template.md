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

### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when ... 

Another shortcoming could be ...


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to ...

Another potential improvement could be to ...
