# **Finding Lane Lines on the Road** 

## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report

[//]: # (Image References)

[init]: ./writeup_images/00_start.png "Initial picture"
[cannied]: ./writeup_images/03_cannied.png "Canny Edge detection"
[region]: ./writeup_images/04_region_selected.png "Unnecessary parts removed"
[line]: ./writeup_images/05_line_image.png "Hough transform lines reduced to two lines"
[overlay]: ./writeup_images/06_overlay.png "Lines and initial picture combined"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

The examples at the steps are based on the following initial picture:
![init]

## The pipeline
My pipeline consisted of the following steps:

1. Converting to grayscale
2. Slight Gaussian blur to eliminate noise
3. Canny Edge detection was used to detect edges in the picture. Though lanes look like they are already found they are still just pixels without any order. ![cannied]
4. Unnecessary part of the resulting picture was eliminated, which helps the lane detection in next step. ![region]
5. Hough transformation was applied to the previous picture. This generates many lines (depending on the visibility and detectability) of the lanes.
6. Based on the 5th step the position of the lanes are calculated to display only two lines. ![line].
7. Combine initial picture with the previous lane image. ![overlay]

## draw_lines() - simplification of the output of Hough

Since the goal is to have two lanes, but there are many lines in the output of Hough transformation, some of them may not even be part of the lanes, they need to be simplified.

Currently there are two separate methods in draw_lines()   (and actually there are two draw_lines() - the first is overwritten with the current implementation). The second is short and is not bad, but the first one is much better and that is what can be seen on the videos/images, it was just an idea.

But both start with the same preparations:
1. Determine the angle (from axis Y) and length of each lines, which are also sorted based on the sign of angle to "left", and "right" lines.

2. Determine the weighted (based on the length of the lines) average of the angles for both left, and right.


Now the two separate method are the following ("0", and "1"):
1. Mode "0" (used in images, videos):
    1. To help prevent glitches, the two set of lines are filtered so only the lines which differ only slightly from the weighted average are kept.
    2. Per side minimum and maximum X/Y values of lines are computed (min/max X is swapped between sides as it is supposed to detect the endpoints at the bottom of the picture, and for the right lane "min x" is the top, therefore it should be switched)
    3. Left/right lines are extrapolated to be drawn from the bottom to the near center
   
2. Mode "1": 
    1. "Center" point of the camera on the horizon was manually determined (where based on the sample images most of the left/right lanes intersect if they were longer)
    2. Using the average angles from the previous preparation two lines were drawn from the centerpoint downwards.



### 2. Identify potential shortcomings with your current pipeline

- in some frames the lanes are incorrectly jumping a little bit more inside
- lines don't match up with lanes exactly (due to the min/max X they usually start at the outer part of the lanes)
- not smooth

### 3. Suggest possible improvements to your pipeline

There are many improvements to be done.
Just to name a few:
- images are not connected, yet earlier observations would be useful to correct any mistakes
- other sensor (3d gyro, steered wheel angle, speed, ...) input could be used ("dead reckoning") to further improve precision
- perspective correction

etc.
