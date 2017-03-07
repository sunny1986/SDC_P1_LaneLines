#**Project 1: Finding Lane Lines on the Road** 

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:

In this project we have to identify lane lines on the road using some of the helper functions provided with the project. We start with developing a pipeline on a series of individual images, and later apply the result to a video stream. Once we have this worked out, the next big part is to contribute to the **draw\_lines()** function and try to average and/or extrapolate the line segments we've detected to map out the full extent of the lane lines. The output of the project is an annotated version of the input video stream shown by 2 solid lines annotating the left and right lanes throughout most of the video

Final submissions of the project include:

* Code: Make a pipeline that finds lane lines on the road
* Write up: Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"
[image2]: ./examples/image2.jpg "Image"
[image3]: ./examples/Y_img.jpg "Yellow image"
[image4]: ./examples/W_img.jpg "White image"
[image5]: ./examples/combined.jpg "Combined image"
[image6]: ./examples/canny.jpg "Canny"
[image7]: ./examples/canny_ROI.jpg "Canny ROI"
[image8]: ./examples/line_image.jpg "Line Image"
[image9]: ./examples/final_image.jpg "Final Image"

---

### Reflection

###1. Pipeline description

My pipeline consisted of following steps:

* Read an image/frame from a video and get rid of noise and smoothen the image using Gaussian filter

![alt text][image2]

* Convert from RGB to HSV color space to filter out into images with yellow lane (1st image) and white lane (2nd image) markings and combine both (3rd image)

![alt text][image3] ![alt text][image4] ![alt text][image5]

* Apply Canny on the combined image to find all edges (1st image) and then filter the only ones within the ROI (2nd image)

![alt text][image6] ![alt text][image7]

* Apply Hough transform to find lines in the image (1st image) and then combine (2nd image) the found lines with the originial image frame

![alt text][image8] ![alt text][image9]

Hough transform is applied using **hough\_lines()** function which calls **draw\_lines()** function to draw lines on the image frames. A good portion of the project is dedicated to modifying the **draw\_lines()** function to annotate the resulting video with 2 solid lines on top of left and right lanes.

##### Breakdown of draw\_lanes() function

* The function mainly receives the image frame and the Hough lines as parameters. Looping through these lines we calculate slopes and find the centers of each line

* In the next step we filter these lines on the basis of slopes that make sense, getting rid of outliers like horizontal lines or vertical lines. Further testing on videos gave me a range of valid limits for slopes.

* Slopes and centers of these filtered lines were stored 

* Average slopes and intercepts for these lines were calculated

* Further testing also made sense to filter out the intercepts which did not make sense while calcuating average intercepts

* Having information on average slope, centers and intercepts for left and right lanes, the function returns two parameters : pos_line and neg_line. These lines are basically points that are returned to the **hough\_lines** function that draws the 2 solid lines on left and right lanes.


###2. Identify potential shortcomings with your current pipeline

1. In draw\_lines() function we filter the Hough lines based on a range of slope values which are fixed. Hence the code might break when the car travels through sharp turns as the lanes have a much steeper slope.

2. Another shortcoming would be code performance to drop a lot when there are no lane markings on the road, eg. in a street. 

3. When lighting conditions change drastically like on a bright sunny day and then the car drives through an underpass or the weather is patchy with clouds and sunshine

4. Also like seen in the challenge video, the pipeline seems to sturggle where there are variations in lighting. 

5. Also changes in the color of the road varying while transitioning from driving on the road to driving on a bridge which is mostly made up of concrete and is lighter in shade compared to road

6. Not suitable for night driving

7. No vehicle should be in front occluding the lanes

8. At intersections the pipeline might not function well since it might not see any lanes

9. For portions of driving when lane changes occur, the pipeline will perform poorly

10. Any conditions outside which the code has been trained on. Lots of parameters are fixed and for any situations outside these conditions the pipeline is prone to break.

###3. Suggest possible improvements to your pipeline

1. In order to improve the 1st shortcoming, we can detect when there is a sharp turn. One way could be to find out when there is a reduction in the number of Hough lines below a certain threshold especially in regions close to the apex of the ROI. Also there will be Hough lines with slopes outside the slope limits. Once this is detected we can make our ROI to be dynamic by pulling the apex of ROI further down towards the bottom of the image

2. Lot of improvements can be done if the parameters and ROI is dynamic based on the situations

3. Also using historical values for lane detection will help in situations when some frames detect no lanes at all. But this will not help when there are no lanes at all on the road

4. Improvements may also include vehicle tracking as well if there is one in front which is occluding the lanes.

5. Further tuning of pipeline can be done for drastically different conditions in lighting, color of lanes, roads, etc. But it can only be limited since the space of variations in much larger than what can be spanned by the information from a camera, thereby requiring other sensory inputs