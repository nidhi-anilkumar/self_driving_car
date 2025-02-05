# **Finding Lane Lines on the Road** 


Overview
---
Identify laneline markings of ego lane

<img src="examples/final_image.jpg" width="480" alt="Combined Image" />

Pipeline
---
Automatic detection of lanelines based on images can be divided in 3 steps

### Step 1  Preprocess images

Convert image to grayscale and blur images. By blurring the image, the objects around get less focused and blend into the scene. Lanelines become more distinct due to the drastic change of color. 

Use built-in opencv function for canny edge detection to get the outline of the objects. 

<img src="test_images/solidWhiteRight.jpg" width="480" alt="Input Image" />
<img src="examples/pre_proc.jpg" width="480" alt="Preprocessed Image" />

### Step 2  Determine lanes

For a car traveling on any straight road (or curvature of large radius), depending on the mount angle of the camera, the lane lines appear at an angle of ~+/-40deg from the bottom of the image. This does not hold true at intersections. With a straight road the angle subtended by both lanes remains the same with respect to x-axis at all points of y till the end of lane (the point of convergence is not visible) With winding roads, the angle subtended by right/left lane starts changing.

In the algorithm I have considered length of road visible till 0.6 x length of image.

Based on these concepts, mask the portion of image of interest. On an empty road, only lane lines are present in the processed image. 

Use built-in opencv hough lines function to determine the end points of straight lines of various lengths. Consider resolution in hough domain of 3pixels distance and 2degrees angle, and line properties as 20pixels with 10 pixel gap.

<img src="examples/roc.jpg" width="480" alt="Combined Image" />
<img src="examples/hough.jpg" width="480" alt="Combined Image" />

### Step 3  Combine and extend lane lines

Divide the lines obtained based on +/- slope as belonging to right lane vs left lane. 

Histogram plot of the slopes of lines categorized as a certain side shows there may be someoutliers in some images. This can be eliminated by choosing lines within 1.5 x mean slope of the category. 

Find the mean slope and intercept. Draw an extended laneline from base of image to 0.6 x height of image

<img src="examples/final_image.jpg" width="480" alt="Final Image" />

## Short-comings

Lanelines are predicted as straight lines.

Challenge video fails in the middle due to indistinguishable lane line edge against concrete road surface.

Apart from the above 2 obvious deficiencies, we notice a jitter in the lane lines. The high frequency jitter can be reduced with a filter. The low frequency change of slope is information on road curvature which can be used to better model lane lines.

<img src="examples/jitter.jpg" width="480" alt="Error" />

## Future Improvements

- [ ] Stabilize lane jitter to within acceptable levels
- [ ] Polynomial line of 2nd degree for lane lines
- [ ] Vary preprocessing parameters to handle different light conditions (day vs night, concrete vs asphalt road surfaces)
- [ ] Tackle edge cases for radius of curvature
- [ ] Use advanced techniques to predict and extend lanes in the presence of cars within the lane

