## Advanced Lane Finding Project


---

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[image1]: ./examples/undistort_output.png "Undistorted"
[image2]: ./test_images/test1.jpg "Road Transformed"
[image3]: ./examples/binary_combo_example.jpg "Binary Example"
[image4]: ./examples/warped_straight_lines.jpg "Warp Example"
[image5]: ./examples/color_fit_lines.jpg "Fit Visual"
[image6]: ./examples/example_output.jpg "Output"
[video1]: ./project_video.mp4 "Video"
[im1]:./camera_cal_output/opcalibration1.jpg "undistorted_output"
[im3]:./perspective_transform_output/test1.jpg "Lane detection"
[imfinal]:./output_images/test1.jpg "Lane detection"



### Overview of the implementation

In the [initial](https://github.com/deveshchatuphale7/CarND-LaneLines-P1) project, where I was successfully able to detect lane lines by the help of Canny edge detection and Hough transform but,
The approach was failing to detect curved lines as well as lane lines with different colors. So in this project I have implemented advanced
Computer Vison techniques to detect lane lines using openCV library with Python.

---

### Camera Calibration

In this step, I implemented  ```cv2.findChessboardCorners() ``` OpenCV function along with set of images of 9 x 6 chessboard.
As the chessboard images will be of grid size 9 x 6, I preapared a  list of tuple for object points containg chess board corners (x,y,z),
with value of z = 0 as chessboard is considerd as 2D plane. Then I appended object points to an image points list on successful
detection of corners.  
I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result:

 
![alt text][im1]

### Pipeline

#### 1 distortion-corrected image.

I applied distortion corretion to the test images based on the distortion coefficients returned by `cv2.calibrateCamera()`  :

#### 2. color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a perspective trasnform to convert view of image into a bird eye view to get perspective transform iamge and used calculated directional gradient using OpenCV
`cv2.Sobel()` function. To calculate directional gradient I converted image to HLS format and passed lightness value to sobel functions



![alt text][im3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `warper()`, which appears in lines 1 through 8 in the file `example.py` (output_images/examples/example.py) (or, for example, in the 3rd code cell of the IPython notebook).  The `warper()` function takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points.  I chose the hardcode the source and destination points in the following manner:

```python
src = np.float32(
    [[(img_size[0] / 2) - 55, img_size[1] / 2 + 100],
    [((img_size[0] / 6) - 10), img_size[1]],
    [(img_size[0] * 5 / 6) + 60, img_size[1]],
    [(img_size[0] / 2 + 55), img_size[1] / 2 + 100]])
dst = np.float32(
    [[(img_size[0] / 4), 0],
    [(img_size[0] / 4), img_size[1]],
    [(img_size[0] * 3 / 4), img_size[1]],
    [(img_size[0] * 3 / 4), 0]])
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 580, 460      | 200, 0        | 
| 270, 670      | 200, 720      |
| 1100, 670     | 1080, 720      |
| 740, 460      | 1080, 0        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.


#### 4. Identify lane-line pixels and fit their positions with a polynomial

I prepared histogram from the bottom half part of the image to detect lane line co-ordinates from the transformed image
`bottom_half = img[img.shape[0]//2:,:]`
`histogram = np.sum(bottom_half, axis=0)`


#### 5. Radius of curvature of the lane and the position of the vehicle with respect to center.

I got co-ordinate values of start and end of the road from histogram plot and used margin of 80 to draw 9 sliding windows over detected lane 
pixels.Then in the next step I calculated radius of curvature by by finding changing of curvature with respect to lane line lixels   

#### 6. Final image with detected lane.


![alt text][imfinal]

---

### Pipeline (video)


Here's a [link to my video result](./project_video_output.mp4)

---

### Challenges faced  

In this process, a challenge to me was finding radius of curvature from the histogram values.To understand more about 
geometry and formulae I refered [this]('https://www.intmath.com/applications-differentiation/8-radius-curvature.php') link.

In my opinion the pipeline might fail if there is sharp turn in the road. As the histogram will not be skewed and it would be 
challenging to detect lane lines.

In this scenario taking sublclip of the video or taking 1/4th or 1/3rd of bottom part of histogram image to detect lane lines  
