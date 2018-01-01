# **Finding Lane Lines on the Road**


**Introduction: Finding Lane Lines on the Road**

In this project, I used Python and OpenCV to find lane lines in the images and video included as part of the repository.

The following pipeline is used:
1. Color Selection and Gray Scale Conversions
2. Gaussian Blur
3. Canny Edge Detection
4. Probabilistic Hough Transform Line Detection
5. Averaging and Extrapolating Lines
6. Weighted Image


[//]: # (Image References)

[image1]: ./test_images_output/solidWhiteCurve_lines_edge.jpg "Grayscale"
[image2]: ./test_images_output/solidWhiteRight_lines_edge.jpg "Grayscale"
[image3]: ./test_images_output/solidYellowCurve2_lines_edge.jpg "Grayscale"
[image4]: ./test_images_output/solidYellowCurve_lines_edge.jpg "Grayscale"
[image5]: ./test_images_output/solidYellowLeft_lines_edge.jpg "Grayscale"
[image6]: ./test_images_output/whiteCarLaneSwitch_lines_edge.jpg "Grayscale"


[//]: # (Video References)

[video1]: ./test_videos_output/challenge.mp4 "Grayscale"
[video2]: ./test_videos_output/solidWhiteRight.mp4 "Grayscale"
[video3]: ./test_videos_output/solidYellowLeft.mp4 "Grayscale"



### Reflection

### 1. Lane Length Detection Description

The following pipeline is used:
1. *Color Selection and Gray Scale Conversions*: Using ```cv2.cvtColor``` we can convert the Image different color space. In this program i use two color spaces COLOR_RGB2HSL and COLOR_RGB2GRAY. By using the COLOR_RGB2HSL both the white and yellow lines are recognizable. I then construct two color masks, one for White and one for Yellow by selecting the range of each channels Hue, Saturation and Light. Mask is then combined by using the ```cv2.bitwise_or```. The combined mask returns white (255) when either white or yellow color is detected. ```cv2.bitwise_and to``` apply the combined mask onto the original RGB image. This image is then run the Gray scale conversion using the ```cv2.cvtColor```.
2. *Gaussian Blur*: We should make the edges of the lane smoother. I use the function ```cv2.GaussianBlur``` to smooth out edges.  I used the kernel size of 19 for videos.
3. *Canny Edge Detection*: ```cv2.Canny``` takes two threshold values. Canny recommended a upper:lower ratio between 2:1 and 3:1. I use the lower and higher threshold of 50 and 150 respectively. The smoothed image after Gaussian operation is fed into Canny Edge Detection.
4. *Probabilistic Hough Transform Line Detection*: ```cv2.HoughLinesP``` is used to detect lines in the edge images from Canny Edge detection. Below parameters need to be played with:
  * rho – Distance resolution of the accumulator in pixels.
  * theta – Angle resolution of the accumulator in radians.
  * threshold – lines are returned that get enough votes (> threshold).
  * minLineLength – Line segments shorter than this value are rejected.
  * maxLineGap – Max allowed gap between points on the same line to link them.
Regions of interest has the region of the image defined by the polygon formed from `vertices` which is then passed for Hough Transform Line detection.
5. *Averaging and Extrapolating Lines*: After the Hough Transform Line we have the multiple lines detected for a lane line. Some of these lines are not contiguous. We should come up with an averaged line and extrapolate the line to cover full lane line length. The way to do that is by calculating the slope and intercepts. There are two lane lines: one for the left and the other for the right. The left lane has positive slope, and the right lane has negative slope. Therefore, we'll collect positive slope lines and negative slope lines separately and take averages. I separate the line segments by their slope ((y2-y1)/(x2-x1)) to decide which segments are part of the left line vs. the right line.  Then, you take the ```np.mean``` of the left and right lanes: slope and intercept.  ```cv2.line``` is used to draw the line between the two points.
6. *Weighted Image*: Finally, ```cv2.addWeighted``` is used merge the two images. The result image is computed as follows: initial_img * α + img * β + λ

**Pre-requisites**

$ pip install imageio (if not already installed).

Then create a .py file with example ffmpeg.py:

```python
import imageio
imageio.plugins.ffmpeg.download()
```
Run this script in the terminal $ python ffmpeg .py
It installs ffmpeg.osx in a directory that should be automatically added to your path.

**Output Images**

![alt text][image1]

![alt text][image2]

![alt text][image3]

![alt text][image4]

![alt text][image5]

![alt text][image6]

**Output Videos**
Roughly the pipeline for video is:
VideoFileClip --> Clip --> Image --> Color Selection --> Gray Scale Conversion -->  Gaussian Blur --> Canny Edge Detection --> Hough Transform Line Detection --> Averaging and Extrapolating Lines --> Weighted Image --> Write_videofile

![alt text][video1]

![alt text][video2]

![alt text][video3]


### 2. Identify potential shortcomings with your current pipeline

There are two potential shortcoming:
1. Need to remove the hardcoding of the vertices
2. In the challenge video there are some section where the lane detection goes off.


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to handle the curved lines. This will require instead of drawing straight lines use the poly lines.

Another potential improvement or testing would be look how this code behaves when we have roads which are going up and down since in this case the mask is assumed from the center of the image.

## **Conclusion**

The project was successful in that the video images clearly show the lane lines are detected properly and lines are very smoothly handled in both the videos and the images. In summary, the pipeline described above does:
1. Line identification take road images from a video as input and return an annotated video stream as output. The output video is an annotated version of the input video.
2. Identifies the left and right lane lines with either line segments or solid lines.
3. Line segments has been filtered / averaged / extrapolated to map out the full extent of the left and right lane boundaries.
