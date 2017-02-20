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

The pipeline consists of 7 steps.

####Step 1. Input.

Provide image(frame) with width = 960px and length 540, RGB.

matplotlib.image used for this step.

![alt text][image1]

####Step 2. Apply the Grayscale transform.
<pre><code>cv2.cvtColor(img, cv2.COLOR_RGB2GRAY)</code></pre>
* img - input image

![alt text][image2]

####Step 3. Apply Gaussian smoothing.

On this step we need to reduce noises by applying Gaussian blur with kernel size 5 (must be an odd number (3, 5, 7...)).
<pre><code>cv2.GaussianBlur(img, (kernel_size, kernel_size), 0)</code></pre>
* img - input image

![alt text][image3]

####Step 4. Apply the Canny transform.

Canny transform uses to find the edges of the lane lines in an image of the road.
<pre><code>cv2.Canny(img, low_threshold, high_threshold)</code></pre>
For this step used parameters:
* low_threshold = 110
* low_threshold = 160
* img - input image

![alt text][image4]

####Step 5. Region masking.

There are some other objects detected around the periphery that aren't lane lines. Assume that the front facing camera that took the image is mounted in a fixed position on the car, such that the lane lines will always appear in the same general region of the image.
From this step, pipeline considers pixels for color selection in the region where it expects to find the lane lines.

![alt text][image5]

####Step 6. Define the Hough transform parameters and run Hough on edge detected image.

In Hough space, we can represent "x vs. y" line as a point in "m vs. b" instead. The Hough Transform is just the conversion from image space to Hough space. So, the characterization of a line in image space will be a single point at the position (m, b) in Hough space.

Tuned parameters for Hough transform:
<pre><code>
rho = 2                # distance resolution in pixels of the Hough grid
theta = np.pi/180      # angular resolution in radians of the Hough grid
threshold = 40         # minimum number of votes (intersections in Hough grid cell)
min_line_length = 10   # minimum number of pixels making up a line
max_line_gap = 10      # maximum gap in pixels between connectable line segments
</code></pre>

Run Hough transform:
<pre><code>
lines = cv2.HoughLinesP(img, rho, theta, threshold, np.array([]), minLineLength=min_line_len, maxLineGap=max_line_gap)
</code></pre>

![alt text][image6]

####Step 7. 

In order to draw a single line on the left and right lanes, I modified the draw_lines() function by ...


###2. Identify potential shortcomings with your current pipeline

####Problem 1. Different resolution of input video (e.g. Optional challenge) - solved
OpenCV "resize" used for this step.
<pre><code>thumbnail = cv2.resize(image, (width, height), interpolation = cv2.INTER_CUBIC)</code></pre>

####Problem 2. 

####Problem 3.


###3. Suggest possible improvements to your pipeline

A possible improvements:

1. Unified requirements for input video (resolution, brightness, contrast etc.)

2. Even if video coming with standart preset it is possible that it recorded during different light conditions. So, it needs for preprocessing depends on parameters of input frame (e.g. if input image is dark we need to up brightnrss level)
