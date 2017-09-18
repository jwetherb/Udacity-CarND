**Finding Lane Lines on the Road**

Jon Wetherbee
CarND Program
September 8th, 2017 Cohort

### Reflection

### 1. The entrypoint for my pipeline is a method, 'do_color_and_shape_mask' that takes an image of a road, taken from the perspective of a camera atop a vehicle, and returns a new image with the identified lane lines in red superimposed on the original image.

Within this method, I performed the following steps, largely with the help of a second method, 'derive_lane_lines'. My process of deriving the lane lines follows the steps below:

a) Read in the image and convert to grayscale
b) Apply Gaussian smoothing to the image
c) Run Canny to detect edges in the image
d) Clip the image to black out all but the region of interest: the lower region of the image roughly forming a triangle between the left and right bottom corners and the center of the image. Our assumption is the lane lines we are interested in will appear in this region.
e) Apply Hough transformation on the lines in this region to detect the primary straight lines and extrapolate contiguity while filtering out noise.
f) Call sub method 'derive_lane_lines' to perform remainder of processing:
g) Divide the lines into two groups -- positively sloped and negatively sloped -- that will be used to derive the left and right lane lines
h) Calculate the min (top) y value for each set of lines
i) Use 'linregress' from scipy.stats to derive the average slope for the right and left set of lines
j) Find the average (mid-way) point for each lane line
k) For each side, create a line using the derived slope that passes through the mid-way point and is bounded by y values at the top ([left|right]_y_min) and the bottom (ysize)
l) Superimpose these left and right lane lines onto the image and return

Here is an example result from calling my pipeline:

![Example image showing lane lines superimposed][my_example_image.png]


### 2. Identify potential shortcomings with your current pipeline

Some of the problems in my pipeline that remain to be solved include:

a) After dividing the hough lines into left and right lane line candidates, it would be useful to calculate the slope of each line and filter out those that deviate significantly from the mean slope. This would eliminate spurious lines that were caught up in the canny and hough processing but which are not related to the majority of lines which comprise the lane lines.
b) To further smooth out the lines, it would be useful to bias each lane line to include information about the lane line derived for the previous frame(s). This would leverage the knowledge that cars don't generally skip all around the road in split seconds, and that they remain relatively consistent in relation to the lines from one frame to the next.

### 3. Suggest possible improvements to your pipeline

a) Task a) above could be accomplished by finding a more sophisticated interpolation function that could take a set of lines and eliminate the outliers so they don't skew the slope and average points for the remaining, highly consistent, lines.
b) To accomplish b) above, we would want to pass the results from processing the previous image to the current image, to allow for a smoothing bias.
