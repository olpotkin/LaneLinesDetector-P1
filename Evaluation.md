#**Finding Lane Lines on the Road** 

[image1]: ./examples/01_input.jpg "Input image"


### Reflection

###1. Pipeline description.

The pipeline consisted of 7 steps:

####Step 1. Input.

Provide image(frame) with width = 960px and length 540, RGB.
matplotlib.image used for this step.

![alt text][image1]

####Step 2. Apply the Grayscale transform.
<pre><code>cv2.cvtColor(img, cv2.COLOR_RGB2GRAY)</code></pre>
*img - input image

####Step 3.

####Step 4.

####Step 5.

####Step 6.

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
