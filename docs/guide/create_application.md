# Create your car application

If you are not already, please [ssh into your vehicle](/guide/robot_sbc/setup_raspberry_pi/#step-5-connecting-to-the-pi).

## Create Donkeycar from Template

Create a set of files to control your Donkey with this command:

```bash
donkey createcar --path ~/mycar
```

That creates a car using the default deep learning template.  You can also create a car the uses the gps path follow template;

```bash
donkey createcar --template=path_follow --path ~/mycar
```

See also more information on [createcar.](/utility/donkey/#create-car)

## Configure Options

Look at __myconfig.py__ in your newly created directory, ~/mycar
```bash
cd ~/mycar
nano myconfig.py
```

Each line has a comment mark. The commented text shows the default value. When you want to make an edit to over-write the default, uncomment the line by removing the # and any spaces before the first character of the option.

example:

```python
# STEERING_LEFT_PWM = 460
```

becomes:

```python
STEERING_LEFT_PWM = 500
```

when edited. You will adjust these later in the [calibrate](/guide/calibrate/) section.

### Configure I2C PCA9685

If you are using a PCA9685 servo driver board, make sure you can see it on I2C.

**Jetson Nano**:

```bash
sudo usermod -aG i2c $USER
sudo reboot
```

After a reboot, then try:

```bash
sudo i2cdetect -r -y 1
```

**Raspberry Pi**:

```bash
sudo apt-get install -y i2c-tools
sudo i2cdetect -y 1
```

This should show you a grid of addresses like:

```text
     0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f
00:          -- -- -- -- -- -- -- -- -- -- -- -- --
10: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
20: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
30: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
40: 40 -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
50: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
60: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
70: 70 -- -- -- -- -- -- --
```

In this case, the 40 shows up as the address of our PCA9685 board. If this does not show up, then check your wiring to the board. On a pi, ensure I2C is enabled in menu of ```sudo raspi-config``` (notice, it suggest reboot).

If you have assigned a non-standard address to your board, then adjust the address in the `myconfig.py` under variable `PCA9685_I2C_ADDR`. If your board is on another bus, then you can specify that with the `PCA9685_I2C_BUSNUM`.

**Jetson Nano**: set ```PCA9685_I2C_BUSNUM = 1``` in your __myconfig.py__ . For the pi, this will be auto detected by the Adafruit library. But not on the Jetson Nano.

## Sombrero Setup

Set ```HAVE_SOMBRERO = True``` in your __myconfig.py__ if you have a sombrero board.

## Robo HAT MM1 Setup

Set ```HAVE_ROBOHAT = True``` in your __myconfig.py__ if you have a Robo HAT MM1 board. Also set the following variables according to your setup.  Most people will be using the below values, however, if you are using a Jetson Nano, please set `MM1_SERIAL_PORT = '/dev/ttyTHS1'`


```python3
#ROBOHAT MM1
HAVE_ROBOHAT = True            # set to true when using the Robo HAT MM1 from Robotics Masters.  This will change to RC Control.
MM1_STEERING_MID = 1500         # Adjust this value if your car cannot run in a straight line
MM1_MAX_FORWARD = 2000          # Max throttle to go fowrward. The bigger the faster
MM1_STOPPED_PWM = 1500
MM1_MAX_REVERSE = 1000          # Max throttle to go reverse. The smaller the faster
MM1_SHOW_STEERING_VALUE = False
# Serial port 
# -- Default Pi: '/dev/ttyS0'
# -- Jetson Nano: '/dev/ttyTHS1'
# -- Google coral: '/dev/ttymxc0'
# -- Windows: 'COM3', Arduino: '/dev/ttyACM0'
# -- MacOS/Linux:please use 'ls /dev/tty.*' to find the correct serial port for mm1 
#  eg.'/dev/tty.usbmodemXXXXXX' and replace the port accordingly
MM1_SERIAL_PORT = '/dev/ttyS0'  # Serial Port for reading and sending MM1 data (raspberry pi default)

# adjust controller type as Robohat MM1
CONTROLLER_TYPE='MM1'
# adjust drive train for web interface
DRIVE_TRAIN_TYPE = 'MM1'
```

The Robo HAT MM1 uses a RC Controller and CircuitPython script to drive the car during training. You must put the CircuitPython script onto the Robo HAT MM1 with your computer before you can continue.

1.  Download the CircuitPython Donkey Car Driver for Robo HAT MM1 to your computer from [here](https://github.com/autorope/donkeycar/blob/dev/donkeycar/contrib/robohat/code.py)
2.  Connect the MicroUSB connector on the Robo HAT MM1 to your computer's USB port.
3.  A __CIRCUITPY__ device should appear on the computer as a USB Storage Device
4.  Copy the file downloaded in Step 1 to the __CIRCUITPY__ USB Storage Device.  The file should be named __code.py__. It should be at the top level of the drive, not in any folder.
5.  Download the Adafruit logging library python file, *adafruit_logging.py*, [here](https://github.com/adafruit/Adafruit_CircuitPython_Logging/blob/master/adafruit_logging.py)
6.  Copy the *adafruit_logging.py* file into the CIRCUITPY "lib" folder
7.  Unplug USB Cable from the Robo HAT MM1 and place on top of the Raspberry Pi, as you would any HAT.


You may need to enable the hardware serial port on your Raspberry Pi.  On your Raspberry Pi...

1.  Run the command ```sudo raspi-config```
2.  Navigate to the __5 - Interfaceing options__ section.
3.  Navigate to the __P6 - Serial__ section.
4.  When asked: __Would you like a login shell to be accessible over serial?__  NO
5.  When asked: __Would you like the serial port hardware to be enabled?__ YES
6.  Close raspi-config
7.  Restart


If you would like additional hardware or software support with Robo HAT MM1, there are a few guides published on Hackster.io.  They are listed below.

[Raspberry Pi + Robo HAT MM1](https://www.hackster.io/wallarug/autonomous-cars-with-robo-hat-mm1-8d0e65)

[Jetson Nano + Robo HAT MM1](https://www.hackster.io/wallarug/donkey-car-with-jetson-nano-robo-hat-mm1-e53e21)

[Simulator + Robo HAT MM1](https://www.hackster.io/wallarug/donkey-car-simulator-with-real-rc-controller-628e77)


## Joystick setup

If you plan to use a joystick, take a side track over to [here](/parts/controllers/#joystick-controller).

## Camera Setup

If you are using the default deep learning template then you will need a camera.  By default __myconfig.py__ assumes a RaspberryPi camera.  You can change this by editing the __myconfig.py__ file in your `~/mycar` folder.  

If you are using the gps path follow template then you do not need, and may not want, a camera.  In this case you can change the camera type to mock; `CAMERA_TYPE = "MOCK"`.

**Raspberry Pi**:

If you are on a raspberry pi and using the recommended pi camera ("PICAM"), then no changes are needed to your __myconfg.py__.

**Jetson Nano**:

When using a Sony IMX219 based camera, and you are using the default car template, then you will want edit your __myconfg.py__ to have:
`CAMERA_TYPE = "CSIC"`.
For flipping the image vertically set `CSIC_CAM_GSTREAMER_FLIP_PARM = 6` - this is helpful if you have to mount the camera in a rotated position.
Set `IMAGE_W = 224` and also `IMAGE_H = 224`.

CVCAM is a camera type that has worked for USB cameras when OpenCV is setup. This requires additional setup for [OpenCV for Nano](/guide/robot_sbc/setup_jetson_nano/#step-4-install-opencv) or [OpenCV for Raspberry Pi](https://www.learnopencv.com/install-opencv-4-on-raspberry-pi/).

**USB Cameras**

You can also use a USB camera if you prefer.  If you have installed the optional OpenCV dependencies then you can use OpenCV to connect to the camera by editing the camera type to `CAMERA_TYPE = "CVCAM"`. If you have installed the optional pygame library then you can connect to the camera by editing the camera type to `CAMERA_TYPE = "WEBCAM"`.  See the required additional setup for [pygame](https://www.pygame.org/wiki/GettingStarted).

We are adding other cameras over time, so read the camera section in __myconfig.py__ to see what options are available.

## Troubleshooting

If you are having troubles with your camera, check out our [Discord hardware channel](https://discord.gg/zcyzK69S) for more help.

## Upgrade Donkey Car Software

Make all config changes to __myconfig.py__ and they will be preserved through an update. When changes occur that you would like to get, you can pull the latest code, then issue a:

```bash
cd projects/donkeycar
git pull
donkey createcar --path ~/mycar --overwrite
```

If you created a car with the gps path follow template then remember to include the --template argument;

```bash
donkey createcar --template=path_follow --path ~/mycar --overwrite
```


Your ~/mycar/manage.py, ~/mycar/config.py and other files will change with this operation, but __myconfig.py__ will not be touched. Your __data__ and __models__ dirs will not be touched.

-------

## Next 
[Calibrate your car](/guide/calibrate)
