# Report

[Here](https://github.com/Zark84010/opencv-report/blob/master/play_game.py) is my code, which is almost similar to [the code](https://github.com/jatinparab98/HandDino/blob/master/camera.py) given in [the blog post](https://medium.com/@sulphurgfx/playing-chromes-dinosaur-game-using-computer-vision-105da2f3114f), the only changes being the removal of redundancy and making it more clear.

First, we import the necessary modules:
```
import pyautogui
import numpy as np
import cv2
```
What our program basically does is that it sees the video feed, looks for a hand, and presses a key when the hand moves accordingly. `pyautogui` is required for pressing the key after detection of that particular movement. `numpy` is used twice in this program only to initialize 2 values (as we will see below). `cv2` is the main module required for all the Computer Vision operations.

```
last = "centre"
cam = cv2.VideoCapture(0)
```
We declared a string `last`, we will see its use later on. In the line `cam = cv2.VideoCapture(0)`, we have created a `VideoCapture` object, which helps us iterating through each frame of the video feed captured by the camera. The `0` refers to the primary camera (web cam for most devices).

```
while True:
  _, img = cam.read()
  height, width, depth = img.shape
 ```
We then start iterating through each frame.
`cam.read()` returns a tuple containing 2 values, the first is the boolean which denotes whether the frame was read correctly or not, and the other value is the frame image, represented in the format of a 3D matrix (`numpy.ndarray`). Note that any RGB image can be represented as a 2D collection of pixels, where is pixel has 3 values, each one of red, green and blue (also note that it is stored in the order BGR instead of RGB in the `ndarray`). If the image is grayscale, it can be represented as 2D collection of pixels where each pixel has a value between `0` and `255`, `0` being "pure black" and `255` being "pure white". We assume that the frame is read correctly so we store the first boolean in `_`, which is not used again. We store the image of each frame in variable `img`. Further, we store the height, width and number of channels of the frame (though no. of channels won't be used).

```
blur = cv2.GaussianBlur(img, (5, 5), 0)
hsv = cv2.cvtColor(img, cv2.COLOR_BGR2HSV)

lower = np.array([0, 10, 60])
upper = np.array([20, 150, 255])
mask = cv2.inRange(hsv, lower, upper)
```
First, we blur the image using Gaussian Blur so that our algorithms can work better as they work on the important elements rather than the noise. In the `cv2.GaussianBlur()` method, our arguments are the image, the kernel size, and the 3rd argument `0` specifies that the values of sigma x (std. deviation in the x-direction) and sigma y are calculated from the kernel. The kernel can be thought of as a "brush" such that when this "brush" sweeps over the image, each pixel is changed in a particular way, which in this case results in the blurring of the image. (5, 5) denotes the size of the blur, higher the values, more is the blur effect.
jknkjnf
