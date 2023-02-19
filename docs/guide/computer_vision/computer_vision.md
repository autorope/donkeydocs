# Computer Vision Autopilot

The computer vision autopilot, like the deep learning autopilot, interprets camera images in order to determine steering and throttle values.  However, rather that deep learning models, the computer vision autopilot utilizes traditional computer vision algorithms, such as Canny edge detection, to interpret images of the track.  The computer vision autopilot is specifically designed to make it easy to write your own algorithm and use it in place of the built-in algorithm.

The built-in algorithm is a line following algorithm; it expects the track to have a center line, preferably solid, that it can detect.  The expected color of the line can be tuned with configuration, but by default it expects a yellow line.  The algorithm calculates the distance of the line from the center of hte image and a PID controller uses that value to calculate a steering value.  If the car is to the left of the line then it will turn right.  If the car is to the right of the line then it will turn left.  The chosen steering angle is proportional to the distance from the line.  The chosen throttle is inversely proportional to the steering angle so that the car will go faster on a straight path and slow down for turns.  More details on the algorithm and the configuration parameters are discussed below.

What if your track does not have a center line; what if it just has a left and right lane boundary lines; you don't want to have to drive on a boundary.  What if it is a sidewalk?  What if you simply want to make your own algorithm?  The computer vision template is designed to make the pretty easy.  You can write you own part in Python to use as the autopilot and simply change the configuration in your myconfig.py to point to it.  Your part can utilize computer vision parts in [cv.py](https://github.com/autorope/donkeycar/blob/main/donkeycar/parts/cv.py) or you can call OpenCV's python api directly.  We present a simplified example below.

## The Line Follower

The built-in algorithm can follow a line using the camera.  By default it is tuned for a yellow line, but the color that it tracks can be configured.  Many other aspects of the algorithm can be tuned.  Below is as description of the algorithm and how it uses the configuraton values.  The values themselves are listed and described afterwards.


0. If TARGET_PIXEL is None, then use steps 1 to 5 to estimate the target (expected) position of the line.
1. Get copy of the image rows at `SCAN_Y` and `SCAN_HEIGHT` pixels height.  So the result is a block of pixels as wide as the image and `SCAN_HEIGHT` high.
2. Convert the pixels from `RBG` red-gree-blue color space to `HSV` hue-saturation-value color space.  `RGB` color space is how a computer shows colors.  `HSV` color space is closer to how human's perceive color.  For our purposes the 'hue' part is the 'pure' color without regard for shadows or lighting.  This makes it easier to find a color because it is one number, rather than combination of 3 numbers.

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
COLOR_THRESHOLD_LOW  = (0, 50, 50)    # hsv dark yellow
COLOR_THRESHOLD_HIGH = (50, 255, 255) # hsv light yellow

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
TODO


## The PID Controller
It is very common to use a Proportional Integral Derivative (PID) controller to control throttle and/or steering in a wheeled robot.  The path follow autopilot uses a PID algorithm to modify steering based on how far away from the desired path the robot is.  The built-in Line Follower algorithm uses a PID in a similar way; the line follow algorithm outputs a value that is proportional to how far the car is from the center line and whose sign indicates which side of the line it is on.  The PID controller uses the magnitude and sign of the distance from the center line to calculate a steering value that will move the car towards the center line.  

>> The path_follow autopilot also uses a PID controller.  There is a good description of how to tune a controller for driving at [Determining PID Coefficients](/guide/path_follow/path_follow/#determining-pid-coefficients)


## Writing a Computer Vision Autopilot
