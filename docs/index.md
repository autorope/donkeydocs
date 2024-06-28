# About Donkey&reg;

![Release](https://img.shields.io/github/v/release/autorope/donkeycar)
[![All Contributors](https://img.shields.io/github/contributors/autorope/donkeycar)](#contributors-)
![License](https://img.shields.io/github/license/autorope/donkeycar)
![Discord](https://img.shields.io/discord/662098530411741184.svg?logo=discord&colorB=7289DA)
----

Donkey is an open source Self Driving Car Platform for remote control cars written in Python.  It is developed for hobbyists and students with a focus on allowing fast experimentation and easy [community contributions](https://github.com/autorope/donkeycar/blob/main/CONTRIBUTING.md).  It supports various kinds of autopilots including autopilots based on neural networks, computer vision and GPS.  It is being actively used at the high school and university level for learning and research.  It offers a [rich graphical interface](utility/ui/) and includes a [simulator](guide/deep_learning/simulator/) so you can experiment with self-driving even before you build a robot.  It is supported by an active [online community](https://discord.gg/PN6kFeA).

![donkeycar](assets/build_hardware/donkey2.png)

### Use Donkeycar if you want to:
* Build a robot and teach it to drive itself.
* Experiment with [autopilots](guide/train_autopilot/), neural networks, computer vision, and GPS.
* Compete in self driving races like [DIY Robocars](http://diyrobocars.com), including [online simulator races](guide/deep_learning/virtual_race_league/) against competitors from around the world.
* Participate in a vibrant [online community](https://discord.gg/PN6kFeA) learning cutting edge techology and having fun doing it.

---------

### Build your own Donkey

Donkey is the standard car that most people build first. The parts cost about $250 to $300 and take 2 hours to assemble. Here are the main steps to build your own car:

1. [Assemble hardware.](guide/build_hardware)
2. [Install software.](guide/install_software)
3. [Create Donkey App.](guide/create_application)
4. [Calibrate your car.](guide/calibrate)
5. [Start driving.](guide/get_driving)
6. [Create an autopilot.](guide/train_autopilot)
7. [Experiment with simulator.](guide/deep_learning/simulator)

---------------
### What do you need to know before starting? (TL;DR nothing)
Donkeycar is designed to be the 'Hello World' of automomous driving; it is simple yet flexible and powerful.  No specific prequisite knowledge is required, but it helps if you have some knowledge of:

- **[Python](https://docs.python.org/3.11/) programming**.  You do not have to do any programming to use Donkeycar.  The file that you edit to configure your car, `myconfig.py`, is a Python file.  You mostly just uncomment the sections you want to change and edit them; you can avoid common mistakes if you know how Python [comments](https://www.w3schools.com/python/python_comments.asp) and [indentation](https://www.w3schools.com/python/python_syntax.asp) works.
- **Raspberry Pi**.  The Raspberry Pi is the preferred on-board computer for a Donkeycar.  It is helpful to have setup and used a Raspberry Pi, but it is not necessary.  The Donkeycar documentation describes how to install the software on a RaspberryPi OS, but the specifics of how to install the RaspberryPi OS using [Raspberry Pi Imager](https://www.raspberrypi.com/software/) and how to configure the Raspberry Pi using [raspi-config](https://www.raspberrypi.com/documentation/computers/configuration.html) is left to the Raspberry Pi documentation, which is extensive and quite good. I would recommend setting up your Raspberry Pi using the Raspberry Pi documentation and then play with it a little; use the browser to visit websites and watch YouTube videos, like this one taken at the [very first outdoor race](https://youtu.be/tjWmrCIKgnE) for a Donkeycar.  Use a text editor to write and save a file.  Open a terminal and learn how to navigate the file system (see below). If you are comfortable with the Raspberry Pi then you won't have to learn it and Donkeycar at the same time.
- **The Linux [command line shell](https://magpi.raspberrypi.com/articles/terminal-help)**.  The command line shell is also often called the terminal.  You will type commands into the terminal to install and start the Donkeycar software.  The Donkeycar documentation describes how this works.  It is also helpful to know how navigate the file system and how to list, copy and delete files and directories/folders. You may also access your car [remotely](https://www.raspberrypi.com/documentation/computers/remote-access.html); so you will want to know how to enable and connect WIFI and how to enable and start an [SSH](https://www.raspberrypi.com/documentation/computers/remote-access.html#ssh) terminal or [VNC](https://www.raspberrypi.com/documentation/computers/remote-access.html#vnc) session from your host computer to get a command line on your car.

## Get driving.
After [building a Donkeycar](guide/build_hardware/) and [installing](guide/install_software/) the Donkeycar software you can choose your autopilot [template](guide/create_application/) and [calibrate](guide/calibrate/) your car and [get driving](guide/get_driving/)!

## Modify your car's behavior.
Donkeycar includes a number of pre-built [templates](guide/create_application/) that make it easy to get started by just changing configuration. The pre-built templates are all you may ever need, but if you want to go farther you can change a template or make your own. A Donkeycar template is organized as a pipeline of software [parts](parts/about/) that run in order on each pass through the vehicle loop, reading inputs and writing outputs to the vehicle's software memory as they run.  A typical car has a parts that:

- Get images from a camera. Donkeycar supports lots of different kinds of [cameras](parts/cameras/), including 3D cameras and [lidar](parts/lidar/).
- Get position readings from a GPS receiver.
- Get steering and throttle inputs from a [game controller](parts/controllers/) or RC controller.  Donkeycar supports PS3, PS4, XBox, WiiU, Nimbus and Logitech bluetooth game controllers and any game controller that works with RaspberryPi.  Donkeycar also implements a WebUI that allows any browser compatible game controller to be connected and also offers an onscreen touch controller that works with phones.
- Control the car's [drivetrain](parts/actuators/) throttle and steering. Donkeycar supports various drivetrains including the [ESC+Servo](parts/actuators/#standard-rc-with-esc-and-steering-servo) configuration that is common to most RC cars.  It also supports various [differential drive](parts/actuators/#differential-drive-cars) configurations.
- Save telemetry [data](parts/stores/) such as camera images, steering and throttle inputs, lidar data, etc.
- Drive the car on autopilot.  Donkey supports three kinds of [autopilots](guide/train_autopilot/); a [deep-learning](guide/deep_learning/train_autopilot/) autopilot, a [gps autopilot](guide/path_follow/path_follow/) and a [computer vision](guide/computer_vision/computer_vision/) autopilot.  The Deep Learning autopilot supports Tensorflow, Tensorflow Lite, and Pytorch and many model [architectures](parts/keras/).

If there isn't a Donkeycar part that does what you want then write your own [part](parts/about/#parts) and add it to a vehicle [template](parts/about/).

```python
#Define a vehicle to take and record pictures 10 times per second.

import time
from donkeycar import Vehicle
from donkeycar.parts.cv import CvCam
from donkeycar.parts.tub_v2 import TubWriter
V = Vehicle()

IMAGE_W = 160
IMAGE_H = 120
IMAGE_DEPTH = 3

#Add a camera part
cam = CvCam(image_w=IMAGE_W, image_h=IMAGE_H, image_d=IMAGE_DEPTH)
V.add(cam, outputs=['image'], threaded=True)

#add tub part to record images
tub = TubWriter(path='./dat', inputs=['image'], types=['image_array'])
V.add(tub, inputs=['image'], outputs=['num_records'])

#start the drive loop at 10 Hz
V.start(rate_hz=10)
```

See the [home page](http://donkeycar.com)
or join the [Discord server](http://www.donkeycar.com/community.html) to learn more.

Enjoy

-----------------------

### Why the name Donkey?

The ultimate goal of this project is to build something useful. Donkey's were
one of the first domesticated pack animals, they're notoriously stubborn, and
they are kid safe. Until the car can navigate from one side of a city to the
other, we'll hold off naming it after some celestial being.
