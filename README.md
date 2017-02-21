#**Finding Lane Lines on the Road** 

[image1]: ./examples/01_input.jpg "Input image"
[image2]: ./examples/02_grayscale.jpg "Grayscale image"
[image3]: ./examples/03_gaussian_blur.jpg "Blured image"
[image4]: ./examples/04_canny.jpg "Canny image"
[image5]: ./examples/05_polygon_of_interest.jpg "Polygon of interest"
[image6]: ./examples/06_hough.jpg "Hough transform"
[image7]: ./examples/07_hough_extrapolate.jpg "Extrapolation"
[image8]: ./examples/08_output.jpg "Output image"

### Reflection

###1. Pipeline description.

The pipeline consists of 8 basic steps.

####Step 1. Input.

Provide image(frame) with width = 960px and length = 540px, RGB.

matplotlib.image used for this step.

![alt text][image1]

####Step 2. Apply the Grayscale transform.
<pre><code>cv2.cvtColor(img, cv2.COLOR_RGB2GRAY)</code></pre>
* img - input image

![alt text][image2]

####Step 3. Apply Gaussian smoothing.

On this step we need to reduce noises by applying Gaussian blur with kernel size 5 (must be an odd number (3, 5, 7...)).

Note: cv2.Canny() on Step 4 actually applies Gaussian smoothing internally, but it included here because it's possible to get a different result by applying further smoothing (and it's not a changeable parameter within cv2.Canny()).
<pre><code>cv2.GaussianBlur(img, (kernel_size, kernel_size), 0)</code></pre>
* img - input image

![alt text][image3]

####Step 4. Apply the Canny transform.

Canny transform used to find the edges of the lane lines in an image of the road.
<pre><code>cv2.Canny(img, low_threshold, high_threshold)</code></pre>
For this step used parameters:
* low_threshold = 110
* high_threshold = 160
* img - input image

![alt text][image4]

####Step 5. Region masking.

There are some other objects detected around the periphery that aren't lane lines. Assume that the front facing camera that took the image is mounted in a fixed position on the car, such that the lane lines will always appear in the same general region of the image.
From this step, pipeline considers pixels for color selection in the region where it expects to find the lane lines.

![alt text][image5]

####Step 6. Define the Hough transform parameters and run Hough on edge detected image.

In Hough space, we can represent "x vs. y" line as a point in "m vs. b" instead. The Hough Transform is just the conversion from image space to Hough space. So, the characterization of a line in image space will be a single point at the position (m, b) in Hough space.

Tuned parameters for Hough transform:
<pre><code>rho = 2                # distance resolution in pixels of the Hough grid
theta = np.pi/180      # angular resolution in radians of the Hough grid
threshold = 40         # minimum number of votes (intersections in Hough grid cell)
min_line_length = 10   # minimum number of pixels making up a line
max_line_gap = 10      # maximum gap in pixels between connectable line segments
</code></pre>

Run Hough transform:
<pre><code>lines = cv2.HoughLinesP(img, rho, theta, threshold, np.array([]), minLineLength=min_line_len, maxLineGap=max_line_gap)
</code></pre>

![alt text][image6]

####Step 7. Apply extrapolation.

In order to draw a single line on the left and right lanes, I modified the draw_lines() function by applying extrapolation approach.

We have a region with known y parameters:
* y_top = 320 (approximately)
* y_bottom = 0
Formula of x-extrapolation: x = x1 + ((y-y1)*(x2-x1))/(y2-y1)

We still have a bunch of lines coordinates, but we need to draw only 2 lines. For this task we need to devide x-coordinates for 4 arrays (left_top, right_top, left_bottom, right_bottom). After tuning of parameters I've got follow values: 
* top y-coordinate = 320
* bottom y-coordinate = 0 
* mid-x point = 485

Knowing described coordinates it's easy to find average coordinates:
<pre><code>left_btm_avg = int(sum(left_btm)/(len_lb))
right_btm_avg = int(sum(right_btm)/(len_rb))
left_top_avg = int(sum(left_top)/(len_lt))
right_top_avg = int(sum(right_top)/(len_rt))</code></pre>

Draw left and right lane lines:
<pre><code>cv2.line(img, (left_btm_avg, y_btm), (left_top_avg, y_top), color, thickness)
cv2.line(img, (right_btm_avg, y_btm), (right_top_avg, y_top), color, thickness)</code></pre>

![alt text][image7]

####Step 8. Get result image.
Combine input image and image with detected lane lines:
<pre><code>cv2.addWeighted(initial_img, alpha, img, beta, gamma)</code></pre>

####Test case #1:
Finding Lane Lines on the Road (part 01)

[![](https://img.youtube.com/vi/3UnX4SPkwJY/0.jpg)](https://youtu.be/3UnX4SPkwJY)

####Test case #2:
Finding Lane Lines on the Road (part 02)

[![](https://img.youtube.com/vi/2dAZRNz1--w/0.jpg)](https://youtu.be/2dAZRNz1--w)


###2. Identify potential shortcomings with your current pipeline

####Problem 1. Different resolution of input video (e.g. Optional challenge) - solved
OpenCV "resize" used for this step.
<pre><code>thumbnail = cv2.resize(image, (width, height), interpolation = cv2.INTER_CUBIC)</code></pre>

####Problem 2. Different light conditions are the cause of false lines detection.

More preprocessing operations for input data is a required.


###3. Suggest possible improvements to your pipeline

A possible improvements:

1. Unified requirements for input video (resolution, brightness, contrast etc.)

2. Even if video coming with standard preset it is possible that it recorded during different light conditions. So, it requires preprocessing that depends on parameters of input frame (e.g. if input image is dark then we need to raise the level of brightness).

3. In the bottom part of some frames (Optional challenge) possible to see part of the cowl that also could be the cause of False detections. So this part of image must be cropped.

Optional challenge result (this case requires an additional preprocessing for stability improvement):

####Test case #3:
Finding Lane Lines on the Road (part 03)

[![](https://img.youtube.com/vi/dg7BHswQIw4/0.jpg)](https://youtu.be/dg7BHswQIw4)

