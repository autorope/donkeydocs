# Computer Vision Autopilot

The computer vision autopilot, like the deep learning autopilot, interprets camera images in order to determine steering and throttle values.  However, rather that deep learning models, the computer vision autopilot utilizes traditional computer vision algorithms, such as Canny edge detection, to interpret images of the track.  The computer vision autopilot is specifically designed to make it easy to write your own algorithm and use it in place of the built-in algorithm.

The built-in algorithm is a line following algorithm; it expects the track to have a center line, preferably solid, that it can detect.  The expected color of the line can be tuned with configuration, but by default it expects a yellow line.  The algorithm calculates the distance of the line from the center of hte image and a PID controller uses that value to calculate a steering value.  If the car is to the left of the line then it will turn right.  If the car is to the right of the line then it will turn left.  The chosen steering angle is proportional to the distance from the line.  The chosen throttle is inversely proportional to the steering angle so that the car will go faster on a straight path and slow down for turns.  More details on the algorithm and the configuration parameters are discussed below.

What if your track does not have a center line; what if it just has a left and right lane boundary lines; you don't want to have to drive on a boundary.  What if it is a sidewalk?  What if you simply want to make your own algorithm?  The computer vision template is designed to make the pretty easy.  You can write you own part in Python to use as the autopilot and simply change the configuration in your myconfig.py to point to it.  Your part can utilize computer vision parts in [cv.py](https://github.com/autorope/donkeycar/blob/main/donkeycar/parts/cv.py) or you can call OpenCV's python api directly.  We present a simplified example below.

>> IMPORTANT: The computer vision template requires that opencv is installed.  Opencv is pre-installed on the Jetson Nano, but it must be explicitly installed on the Raspberry Pi. See Raspberry Pi installation [Step 9](/guide/robot_sbc/setup_raspberry_pi/#step-9-optional-install-opencv-dependencies) and [Step 11](/guide/robot_sbc/setup_raspberry_pi/#step-11-install-donkeycar-python-code).  

## The Line Follower

The built-in algorithm can follow a line using the camera.  By default it is tuned for a yellow line, but the color that it tracks can be configured.  Many other aspects of the algorithm can be tuned.  Below is as description of the algorithm and how it uses the configuraton values.  The values themselves are listed and described afterwards.


0. If TARGET_PIXEL is None, then use steps 1 to 5 to estimate the target (expected) position of the line.
1. Get copy of the image rows at `SCAN_Y` and `SCAN_HEIGHT` pixels height.  So the result is a block of pixels as wide as the image and `SCAN_HEIGHT` high.
2. Convert the pixels from `RBG` red-gree-blue color space to `HSV` hue-saturation-value color space.  

>> This [pyimagesearch article](https://pyimagesearch.com/2021/04/28/opencv-color-spaces-cv2-cvtcolor/) and accompanying video describe the various color spaces and their characteristics.  

3. The algorithm then identifies all the pixels in the block that have an HSV color between COLOR_THRESHOLD_LOW and COLOR_THRESHOLD_HIGH.
4. Once the pixels with our color target are isolated then a histogram is created that creates counts of yellow pixels from left to right for each 1 pixel wide by SCAN_HEIGHT pixel high slice.
5. The x value (horizontal offset) of the slice with the most yellow pixels is chosen.  This is where we think the yellow line is.
6. The difference between this x-value and the TARGET_PIXEL value is used as the value the PID algorithm uses to calculate a new steering.  If the value is to the left of the TARGET_PIXEL more than TARGET_THRESHOLD pixels then the car steers right; if hte value is to the right of TARGET_PIXEL more than TARGET_THRESHOLD pixels the the car steers left.  If the value is withing TARGET_THRESHOLD pixels of TARGET_PIXEL then steering is not changed.
7. The steering value is used to decide if the car should speed up or slow down.  If steering is not changed then the throttle is increased by THROTTLE_STEP, but not over THROTTLE_MAX.  If steering is changed then throttle is decreased by THROTTLE_STEP, but not below THROTTLE_MIN.


```
# configure which part is used as the autopilot - change to use your own autopilot
CV_CONTROLLER_MODULE = "donkeycar.parts.line_follower"
CV_CONTROLLER_CLASS = "LineFollower"
CV_CONTROLLER_INPUTS = ['cam/image_array']
CV_CONTROLLER_OUTPUTS = ['pilot/steering', 'pilot/throttle', 'cv/image_array']
CV_CONTROLLER_CONDITION = "run_pilot"

# LineFollower - line color and detection area
SCAN_Y = 120          # num pixels from the top to start horiz scan
SCAN_HEIGHT = 20      # num pixels high to grab from horiz scan
COLOR_THRESHOLD_LOW  = (0, 50, 50)    # HSV dark yellow (opencv HSV hue value is 0..179, saturation and value are both 0..255)
COLOR_THRESHOLD_HIGH = (50, 255, 255) # HSV light yellow (opencv HSV hue value is 0..179, saturation and value are both 0..255)

# LineFollower - target (expected) line position and detection thresholds
TARGET_PIXEL = None   # In not None, then this is the expected horizontal position in pixels of the yellow line.
                      # If None, then detect the position yellow line at startup;
                      # so this assumes you have positioned the car prior to starting.
TARGET_THRESHOLD = 10 # number of pixels from TARGET_PIXEL that vehicle must be pointing
                      # before a steering change will be made; this prevents algorithm
                      # from being too twitchy when it is on or near the line.
CONFIDENCE_THRESHOLD = (1 / IMAGE_W) / 3  # The fraction of total sampled pixels that must be yellow in the sample slice.
                                          # The sample slice will have SCAN_HEIGHT pixels and the total number
                                          # of sampled pixels is IMAGE_W x SCAN_HEIGHT, so if you want to make sure
                                          # that all the pixels in the sample slice are yellow, then the confidence
                                          # threshold should be SCAN_HEIGHT / (IMAGE_W x SCAN_HEIGHT) or (1 / IMAGE_W).
                                          # If you keep getting `No line detected` logs in the console then you
                                          # may want to lower the threshold.

# LineFollower - throttle step controller; increase throttle on straights, descrease on turns
THROTTLE_MAX = 0.3    # maximum throttle value the controller will produce
THROTTLE_MIN = 0.15   # minimum throttle value the controller will produce
THROTTLE_INITIAL = THROTTLE_MIN  # initial throttle value
THROTTLE_STEP = 0.05  # how much to change throttle when off the line

# These three PID constants are crucial to the way the car drives. If you are tuning them
# start by setting the others zero and focus on first Kp, then Kd, and then Ki.
PID_P = -0.01         # proportional mult for PID path follower
PID_I = 0.000         # integral mult for PID path follower
PID_D = -0.0001       # differential mult for PID path follower

OVERLAY_IMAGE = True  # True to draw computer vision overlay on camera image in web ui
                      # NOTE: this does not affect what is saved to the data

```

### Choosing Parameters for the LineFollower
The computer vision template is a little different than than the deep learning and path follow templates; there is no data recording.  After setting your configuration parameters you just put your car on the track that has the line that you want to follow and then chage from user mode to one of the auto-pilot modes; full-auto or auto-steering.

**SCAN_Y and SCAN_HEIGHT**
The rectangular area that will be scanned for the line is determined with the SCAN_Y and SCAN_HEIGHT.

**COLOR_THRESHOLD_LOW, COLOR_THRESHOLD_HIGH**
The color threshold values represent the range of colors used to detect the line; they should be chosen to include the colors in the line in the area that it passes through the detection bar and ideally they should not include any other colors.  The color threshold values are in HSV (Hue, Saturation, Color) format, not RGB format.  `RGB` color space is how a computer shows colors.  `HSV` color space is closer to how human's perceive color.  For our purposes the 'hue' part is the 'pure' color without regard for shadows or lighting.  This makes it easier to find a color because it is one number, rather than combination of 3 numbers.  

>> There are many online converters between RGB and HSV.  This one was used when creating this documentation; https://www.peko-step.com/en/tool/hsvrgb_en.html  I like that tool because it will allow the Saturation and Value to be output in the range of 0.255, which is what we need.  IMPORTANT: The online tools use the standard way of representing HSV, which is a Hue value of 0 to 359 degrees, Saturation of 0 to 100%, Value of 0 to 100%.  OpenCV, which our code is based on, use a Hue value of 0 to 179, Saturation of 0 to 255 and value of 0 to 255; so be aware.

When choosing the threshold colors it is important to take into account what the camera will see including the lighting conditions.  Donkeycar includes a script to make this easy to do.  the hsv_picker.sh script allows you tove view what that camera sees or alternatively to choose an image.  So if you have a full linux desktop on your car then you can run the script and view the camera image.  If you do not have a full linux desktop, so you can view graphics on your car, then you can run the car and open the web view in a browser on your host laptop at take a screen shot save save it.  In either case, arrange the car on the course so it can see the line as it would see it when you drive in autopilot so you are getting a realistic view.  

You can run the hsv_picker.sh script to view a screen shot image; with the donkey python environment activated run the script from the root of your donkeycar repo folder;
```
python scripts/hsv_picker.sh --file=<path-to-image>
```

To view the camera stream, again with donkey python environment activated, run the script from the root of your donkeycar repo folder;
```
python scripts/hsv_picker.sh
```
If you have more than one camera and it is not showing the correct one, you can choose the camera index and/or set the image size

```
python scripts/hsv_picker.sh --camera=2  --width=320 --height=240
```

![A screenshot in hsv_script.sh](/assets/hsv_picker_no_mask.png)

The image above shows the hsv_script.sh with a web ui screenshot loaded.  The blue line in the center of the image is the line that we want to follow.  


The bottom of the screen has 6 trackbar to select the 3 parts of the low HSV value and the 3 parts of the high HSV value that are used to create a mask to pull out the pixels in the line.  You can move those scrollbars manually to try to find the right range to pick out the line.  As you change them the resulting mask will be applied to the image and you will see pixels start to turn back.  The Hue value is typically the most important value to get the range correct on.  You can reset the trackbars and clear the mask anytime by selecting the Escape key on the keyboard.

Using the scrollbars works, but there is an easier way.  You can also just select a rectangular area by clicking-dragging-releasing and the pixels in that area will be search for a low and high value and the trackbars will by updated with those low and high values.  So the easier way to find the mask for the line is to select a rectangular area _on the line_ itself.  You can fine-tune the selected mask using the trackbars.

The image below shows the mask that was created by selecting a rectangular area within the blue line.

![A masked screenshot in the hsv_picker.sh script](/assets/hsv_picker_mask.png)

- change the low and high mask values using the taskbars at the bottom of the screen.
- set the low and high mask values by selecting a rectangular area on the image using click-drag-release.
- select the Escape key on the keyboard to clear the mast.
- select the 'p' key to print the current mask values to the console.
- select the 'q' key to print the final mask values and quit.


**TARGET_PIXEL**
The TARGET_PIXEL value is the horizontal position of the line to follow in the image.  The line follow algorithm will adjust steering to try to keep the line at that position in the image.  More specifically, the difference between the TARGET_PIXEL value and where the line follow algorithm detects that actual line is in the image is used by a PID controller to adjust steering (see [The PID Controller](#the-pid-controller)) below.

If you are the only car on the course, then you probably want to the car to drive directly on the line to follow.  In this case setting TARGET_PIXEL to (IMAGE_2 / 2) means the auto-pilot assumes the line to follow should be directly in the middle of the image and so the car will try to stay in the middle.  So if your car actually starts to the left or right of that line, it will quickly move the the line and stay on it.

However, if you are on a course where two cars drive at the same time (there are two lanes separated by a line), then you probably want your car to stay in it's lane.  In that case you would set TARGET_PIXEL to None, which will cause the car to detect the location of the line at startup.  The auto-pilot will then assume the line should stay at that position in the image, and so it will the try to keep the car it it's lane to make that true.


## The PID Controller
It is very common to use a Proportional Integral Derivative (PID) controller to control throttle and/or steering in a wheeled robot.  The path follow autopilot uses a PID algorithm to modify steering based on how far away from the desired path the robot is.  The built-in Line Follower algorithm uses a PID in a similar way; the line follow algorithm outputs a value that is proportional to how far the car is from the center line and whose sign indicates which side of the line it is on.  The PID controller uses the magnitude and sign of the distance from the center line to calculate a steering value that will move the car towards the center line.  

>> The path_follow autopilot also uses a PID controller.  There is a good description of how to tune a controller for driving at [Determining PID Coefficients](/guide/path_follow/path_follow.md/#determining-pid-coefficients)


## Writing a Computer Vision Autopilot
You can use the `CV_CONTROLLER_*` configuration values to point to a python file and class the implements you computer vision autopilot part; it must conform to the [donkeycar part](/parts/about.md) standard.  You can also determine the name of the input values, output values and run_condition.  By default these point the the included LineFollower part.  It takes the camera image as an input and outputs the autopilot's throttle and steering values as well as an image with data that can be shown in the web ui.

It is worth studying the implementation of the LineFollower part to illustrate how a computer vision part is designed.  The python file is located at https://github.com/autorope/donkeycar/blob/main/donkeycar/parts/line_follower.py and is copied below.

```
import cv2
import numpy as np
from simple_pid import PID
import logging

logger = logging.getLogger(__name__)


class LineFollower:
    '''
    OpenCV based controller
    This controller takes a horizontal slice of the image at a set Y coordinate.
    Then it converts to HSV and does a color thresh hold to find the yellow pixels.
    It does a histogram to find the pixel of maximum yellow. Then is uses that iPxel
    to guid a PID controller which seeks to maintain the max yellow at the same point
    in the image.
    '''
    def __init__(self, pid, cfg):
        self.overlay_image = cfg.OVERLAY_IMAGE
        self.scan_y = cfg.SCAN_Y   # num pixels from the top to start horiz scan
        self.scan_height = cfg.SCAN_HEIGHT  # num pixels high to grab from horiz scan
        self.color_thr_low = np.asarray(cfg.COLOR_THRESHOLD_LOW)  # hsv dark yellow
        self.color_thr_hi = np.asarray(cfg.COLOR_THRESHOLD_HIGH)  # hsv light yellow
        self.target_pixel = cfg.TARGET_PIXEL  # of the N slots above, which is the ideal relationship target
        self.target_threshold = cfg.TARGET_THRESHOLD # minimum distance from target_pixel before a steering change is made.
        self.confidence_threshold = cfg.CONFIDENCE_THRESHOLD  # percentage of yellow pixels that must be in target_pixel slice
        self.steering = 0.0 # from -1 to 1
        self.throttle = cfg.THROTTLE_INITIAL # from -1 to 1
        self.delta_th = cfg.THROTTLE_STEP  # how much to change throttle when off
        self.throttle_max = cfg.THROTTLE_MAX
        self.throttle_min = cfg.THROTTLE_MIN

        self.pid_st = pid


    def get_i_color(self, cam_img):
        '''
        get the horizontal index of the color at the given slice of the image
        input: cam_image, an RGB numpy array
        output: index of max color, value of cumulative color at that index, and mask of pixels in range
        '''
        # take a horizontal slice of the image
        iSlice = self.scan_y
        scan_line = cam_img[iSlice : iSlice + self.scan_height, :, :]

        # convert to HSV color space
        img_hsv = cv2.cvtColor(scan_line, cv2.COLOR_RGB2HSV)

        # make a mask of the colors in our range we are looking for
        mask = cv2.inRange(img_hsv, self.color_thr_low, self.color_thr_hi)

        # which index of the range has the highest amount of yellow?
        hist = np.sum(mask, axis=0)
        max_yellow = np.argmax(hist)

        return max_yellow, hist[max_yellow], mask


    def run(self, cam_img):
        '''
        main runloop of the CV controller
        input: cam_image, an RGB numpy array
        output: steering, throttle, and the image.
        If overlay_image is True, then the output image
        includes and overlay that shows how the 
        algorithm is working; otherwise the image
        is just passed-through untouched. 
        '''
        if cam_img is None:
            return 0, 0, False, None

        max_yellow, confidence, mask = self.get_i_color(cam_img)
        conf_thresh = 0.001

        if self.target_pixel is None:
            # Use the first run of get_i_color to set our relationship with the yellow line.
            # You could optionally init the target_pixel with the desired value.
            self.target_pixel = max_yellow
            logger.info(f"Automatically chosen line position = {self.target_pixel}")

        if self.pid_st.setpoint != self.target_pixel:
            # this is the target of our steering PID controller
            self.pid_st.setpoint = self.target_pixel

        if confidence >= self.confidence_threshold:
            # invoke the controller with the current yellow line position
            # get the new steering value as it chases the ideal
            self.steering = self.pid_st(max_yellow)

            # slow down linearly when away from ideal, and speed up when close
            if abs(max_yellow - self.target_pixel) > self.target_threshold:
                # we will be turning, so slow down
                if self.throttle > self.throttle_min:
                    self.throttle -= self.delta_th
                if self.throttle < self.throttle_min:
                    self.throttle = self.throttle_min
            else:
                # we are going straight, so speed up
                if self.throttle < self.throttle_max:
                    self.throttle += self.delta_th
                if self.throttle > self.throttle_max:
                    self.throttle = self.throttle_max
        else:
            logger.info(f"No line detected: confidence {confidence} < {self.confidence_threshold}")

        # show some diagnostics
        if self.overlay_image:
            cam_img = self.overlay_display(cam_img, mask, max_yellow, confidence)

        return self.steering, self.throttle, cam_img

    def overlay_display(self, cam_img, mask, max_yellow, confidense):
        '''
        composite mask on top the original image.
        show some values we are using for control
        '''

        mask_exp = np.stack((mask, ) * 3, axis=-1)
        iSlice = self.scan_y
        img = np.copy(cam_img)
        img[iSlice : iSlice + self.scan_height, :, :] = mask_exp
        # img = cv2.cvtColor(img, cv2.COLOR_RGB2BGR)

        display_str = []
        display_str.append("STEERING:{:.1f}".format(self.steering))
        display_str.append("THROTTLE:{:.2f}".format(self.throttle))
        display_str.append("I YELLOW:{:d}".format(max_yellow))
        display_str.append("CONF:{:.2f}".format(confidense))

        y = 10
        x = 10

        for s in display_str:
            cv2.putText(img, s, color=(0, 0, 0), org=(x ,y), fontFace=cv2.FONT_HERSHEY_SIMPLEX, fontScale=0.4)
            y += 10

        return img
```

Let's create a simpler custom computer vision part.  It won't be much of an autopilot because it will just output a constant throttle and steering value and an image that counts that frames.

TODO
