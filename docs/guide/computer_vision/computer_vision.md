# Computer Vision Autopilot

The computer vision autopilot, like the deep learning autopilot, interprets camera images in order to determine steering and throttle values.  However, rather that deep learning models, the computer vision autopilot utilizes traditional computer vision algorithms, such as Canny edge detection, to interpret images of the track.  The computer vision autopilot is specifically designed to make it easy to write your own algorithm and use it in place of the built-in algorithm.

The built-in algorithm is a line following algorithm; it expects the track to have a center line, preferably solid, that it can detect.  The expected color of the line can be tuned with configuration, but by default it expects a yellow line.  The algorithm calculates the distance of the line from the center of hte image and a PID controller uses that value to calculate a steering value.  If the car is to the left of the line then it will turn right.  If the car is to the right of the line then it will turn left.  The chosen steering angle is proportional to the distance from the line.  The chosen throttle is inversely proportional to the steering angle so that the car will go faster on a straight path and slow down for turns.  More details on the algorithm and the configuration parameters are discussed below.

What if your track does not have a center line; what if it just has a left and right lane boundary lines; you don't want to have to drive on a boundary.  What if it is a sidewalk?  What if you simply want to make your own algorithm?  The computer vision template is designed to make the pretty easy.  You can write you own part in Python to use as the autopilot and simply change the configuration in your myconfig.py to point to it.  Your part can utilize computer vision parts in [cv.py](https://github.com/autorope/donkeycar/blob/main/donkeycar/parts/cv.py) or you can call OpenCV's python api directly.  We present a simplified example below.

## The Line Follower


## Writing a Computer Vision Autopilot


## Choosing Parameters


## The PID Controller
It is very common to use a Proportional Integral Derivative (PID) controller to control throttle and/or steering in a wheeled robot.  The path follow autopilot uses a PID algorithm to modify steering based on how far away from the desired path the robot is.  The built-in Line Follower algorithm uses a PID in a similar way; the line follow algorithm outputs a value that is proportional to how far the car is from the center line and whose sign indicates which side of the line it is on.  The PID controller uses the magnitude and sign of the distance from the center line to calculate a steering value that will move the car towards the center line.
