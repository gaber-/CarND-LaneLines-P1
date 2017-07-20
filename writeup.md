# **Finding Lane Lines on the Road** 

## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 4 steps. First, I applied a gaussian blur to the image (grayscaling as suggested appears to have little
effect on most images and actually causes trouble in the challenge video), then applied canny detection edge and masked the
uninteresting region, and lastly extract the lines with probabilistic hough line transform, averaged and extrapolated them after
removing some noise, and added them on the original image

In order to draw a single line on the left and right lanes, I modified the draw_lines() function by adding some constraint on
the (possibly misidentified) lines, and later averaged them:
* I checked that the candidate line's slope has a likely values, and also used the slope to distinguish left from right lines
* Made sure  that the intersection of the line with the horizon of the picture is somewhere plausible (I assumed it'd be roughly at the center, it may be different if the car is not following the lane)
* Then I removed the lines with outlier intercept (since the slope should have been cheked already)
* Finally I averaged slope and intercept of the remaining lines and drawn the resulting line


![alt text][image1]


### 2. Identify potential shortcomings with your current pipeline


The current pipeline is not very suitable for roads with narrow curves or uneven terrain.
I did not tune the parameters for the slope and horizon intersection checks but only gave generic values that should
be ok for straight or almost straight lanes (but they could be computed having more data on the car direction or even
inferred from the image itself)

It may have issues with low contrast lines (eg due to dirt on the road)

It may be fooled by objects with the same color lined up near the actual lane line, or by lines close
and parallel or nearly parallel to the actual line

It requires a rough guess of the position of the car relative to the road (to calculate the
slope and intersection with the horizon of the lane lines, an also to select the region of interest
meaningfully)


### 3. Suggest possible improvements to your pipeline

The lines could be weighted based, for example longer, with no gaps or better defined lines, could weigh more

Computing clusters of lines based on slope/intercept values could allow to further reduce unwanted lines
(as lines corresponding to the actual road lines tend to be very close to each other)

Region of interest, slopes and horizon intersection can be guessed using data from previous frames, or
if no data is available, they can be computed clustering the lines and using the biggest/most likely
clusters of lines (eg exploiting the fact one line is always left and one always right of the
vehicle and they are parallel so should converge somewhere at the horizon).

The outputted lines could be averaged with previous lines to fix the small flickering that happens due to
single frames' idiosyncracies, and to make the lane detection more robust to possible errors

If the lane lines follow a certain pattern (eg lines closer to the orizon seem to lean towards a side) they
might be part of a curve which can be fitted to their extreme points

Most parameters can be guessed adaptively, for example starting from requiring very high contrast
and stringent parameters at first and loosening up the edge recognition/line selection process
if there are no or too few lines
