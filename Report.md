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
Using `cv2.cvtColor(img, cv2.COLOR_BGR2HSV)` we convert the blurred image from RGB color scheme to HSV color scheme, because it is easier to filter out skin color in HSV. We then create the mask for the skin color portions using `cv2.inRange(hsv, lower, upper)`. Here we used 2 `numpy.ndarray`s of length 3, which correspond to orangish tints in HSV, similar to skin color. All colors between these values are "whitened out" in our mask.

So now we have a mask, which is white in all regions where there is the hand (assuming the hand to be the only skin colored object), and black otherwise.

So now we have our final mask, which ideally contains only the hand, but at the very least we can almost safely say that if there are more objects then the largest one out of them is the hand. 
```
cnts = cv2.findContours(thresh, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)[0]
```
Now, from our mask `thresh`, we get our list of contours (roughly speaking, the borders surrounding each object) using the above line of code. The flag `cv2.RETR_EXTERNAL` means that if, says, the object is of a donut shape, only the outer boundary is considered as the contour. The flag `cv2.CHAIN_APPROX_SIMPLE` means that redundant points are removed (for e.g. using 4 points for a rectangle rather than a large number of points) thus saving memory. Note that the method itself actually gives a tuple, and the list of contours is actually at one of the indices. This index at which the list of contours lies is different in different OpenCV versions, for the (as of now) latest OpenCV 4.x, the index is 0. We store the list of contours in the variable `cnts`.

