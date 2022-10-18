# Lidar

A Lidar sensor can be used with Donkeycar to provide obstacle avoidance or to help navigate on tracks with walls. It records data along with the camera during training and this can be used for training

NOTE: Lidar is currently only supported in the Dev branch. To use it, after you git clone donkeycar, do a `git checkout dev`

![Donkey lidar](../assets/lidar.jpg) 
## Supported Lidars

We currently only support the RPLidar series of sensors, but will be adding support for the similar YDLidar series soon. 

We recommend the [$99 A1M8](https://amzn.to/3vCabyN) (12m range) 


## Hardware Setup

Mount the Lidar underneath the camera canopy as shown above (the RPLidar A2M8 is used there, but the A1M8 mounting is the same). You can velcro the USB adapter under the Donkey plate and use a short USB cable to connect to one of your RPi or Nano USB ports. It can be powered by the USB port so there's no need for an additional power supply.

## Software Setup

Lidar requires the glob library to be installed. If you don't already have that, install it with `pip3 install glob2`

Also install the Lidar driver: `pip install Adafruit_CircuitPython_RPLIDAR`


Then go to the lidarcar directory and edit the myconfig.py file to ensure that the Lidar is turned on. The upper and lower limits should be set to reflect the areas you want your Lidar to "look at", omitting the areas that are blocked by parts of the car body. An example is shown below. For the RPLidar series, 0 degrees is in the direction of the motor (in the case of the A1M8) or cable (in the case of the A2M8)

```
# LIDAR
USE_LIDAR = True
LIDAR_TYPE = 'RP' #(RP|YD)
LIDAR_LOWER_LIMIT = 90 # angles that will be recorded. Use this to block out obstructed areas on your car and/or to avoid looking backwards. Note that for the RP A1M8 Lidar, "0" is in the direction of the motor 
LIDAR_UPPER_LIMIT = 270
```
![Lidar limits](../assets/lidar_angle.png) 

## Template support
Neither the [deep learning template](/guide/train_autopilot/#deep-earning-autopilot) nor the [path follow template](/guide/path_follow/path_follow/) supports Lidar data directly.  There is an issue to [add Lidar data to the deep learning template](https://github.com/autorope/donkeycar/issues/910).  Lidar would also be very useful in the [path follow template](/guide/path_follow/path_follow/) for obstacle detection and avoidance.  If you are interested in working on such projects, please [join the discord community](https://www.donkeycar.com/community.html) and let us know; we will be happy to provide you with support.



