## Donkeycar RC Hat
![RC Hat for RaspberryPi](../assets/rc_hat.jpg "The Donkey RC Hat for RaspberryPi")

If you started with a ready-to-run RC car, it probably came with a RC controller. Good news: you can use it with Donkeycar, using the RC controller for manual driving. You can also plug in the car's servo and motor controller directly into the RaspberryPi without the need for a special motor/servo controller board. 

To do so, you can either wire up it up manually as shown in [this tutorial](rc.md) (which works, but has a lot of fiddly wires that can fall off) or do it far more neatly with the Donkeycar RC hat, shown above, which handles all the wiring for you, along with including an OLED screen and a fan. 

The Donkeycar RC hat can be purchased from the [Donkeycar Store](https://store.donkeycar.com/products/donkey-car-rc-hat). Note that it only works with the RaspberryPi, not the Jetson Nano, due to limitations with the way the Jetson handles its I/O pins. 

To use the hat, just use the included 3-wire cables to connect your RC receiver to the RC 1 and RC 2 pins (corresponding to the RC receiver's Channel 1 and Channel 2). Then plug your car's servo into the Servo pins and the Motor Controller into the Motor pins. In all cases, make sure you plug them in the right way, noting the +,- and S (Signal) markings. Typically the black wire is "-", the red wire in the middle is "+" and the white wire is "S". 

If you're using a standard [wheel encoder](odometry.md), you can plug it into the "Encoder" pins. You can also power the RaspberryPi from this board if you have a 5V source with the "Optional 5v power in" pins

Once you've plugged in all the cables, you can move to the software setup

## Software Setup

First, on the command line enter this to set the PIGPIO daemon to always run on startup:

```bash
sudo systemctl enable pigpiod & sudo systemctl start pigpiod
```

Next, in your `mycar` directory, edit the myconfig.py files as follows:

* For RC input, select `pigpio_rc` as your controller type in your myconfig.py file. Uncomment the line (remove the leading `#`) and edit it as follows:

```python
CONTROLLER_TYPE = 'pigpio_rc'
```

Also set `use joystick` to True

```python
USE_JOYSTICK_AS_DEFAULT = True
```

* For RC output, select `PWM_STEERING_THROTTLE` as your drive train type in your myconfig.py file. Uncomment the line (remove the leading `#`) and edit it as follows:

```python
DRIVE_TRAIN_TYPE =  "PWM_STEERING_THROTTLE"
```

For both of these, there are additional settings you can change, such as reversing the direction of output or the pins connected: 

Input options:
 
```python
#PIGPIO RC control
STEERING_RC_GPIO = 26
THROTTLE_RC_GPIO = 20
DATA_WIPER_RC_GPIO = 19
PIGPIO_STEERING_MID = 1500         # Adjust this value if your car cannot run in a straight line
PIGPIO_MAX_FORWARD = 2000          # Max throttle to go fowrward. The bigger the faster
PIGPIO_STOPPED_PWM = 1500
PIGPIO_MAX_REVERSE = 1000          # Max throttle to go reverse. The smaller the faster
PIGPIO_SHOW_STEERING_VALUE = False
PIGPIO_INVERT = False
PIGPIO_JITTER = 0.025   # threshold below which no signal is reported
```

If you are using the RC hat then the PWM output pins shown below (and defaulted in myconfig.py) must be used.
If you are not using the RC hat then you are free to choose different PWM output pins.
NOTE: you must install pigpio to use this configuration.  See [PIGPIO](pins.md#PIGPIO)

Output options:

```python
PWM_STEERING_PIN = "PIGPIO.BCM.13"           # PWM output pin for steering servo
PWM_THROTTLE_PIN = "PIGPIO.BCM.18"           # PWM output pin for ESC

STEERING_LEFT_PWM = int(4096 * 1 / 20)       # pwm value for full left steering (1ms pulse)
STEERING_RIGHT_PWM = int(4096 * 2 / 20)      # pwm value for full right steering (2ms pulse)

THROTTLE_FORWARD_PWM = int(4096 * 2 / 20)    # pwm value for max forward (2ms pulse)
THROTTLE_STOPPED_PWM = int(4096 * 1.5 / 20)  # pwm value for no movement (1.5ms pulse)
THROTTLE_REVERSE_PWM = int(4096 * 1 / 20)    # pwm value for max reverse throttle (1ms pulse)
```

## Troubleshooting

If one channel is reversed (steering left goes right, etc), either reverse that channel on your RC transmitter (that's usually a switch or setting) or change it in the output options shown above by channging the PWM_INVERTED value for that channel to `True`.

## OLED setup

Enable the display in `myconfig.py`.

```python
# SSD1306_128_32
USE_SSD1306_128_32 = True     # Enable the SSD_1306 OLED Display
SSD1306_128_32_I2C_BUSNUM = 1 # I2C bus number
SSD1306_RESOLUTION = 1 # 1 = 128x32; 2 = 128x64
```
## Showing your IP address on startup. 

One of the cool things about having an OLED screen is that you can show your car's IP address on startup, so you can connect to it. Instructions to set that up are [here](https://diyrobocars.com/2021/12/29/show-your-raspberrypi-ip-address-on-startup-with-an-oled/)

## Troubleshooting

If you are unable to start the car, ensure that the `Adafruit_SSD1306` package is installed in your virtual environment. This should automatically be installed, if you are using a recent version of `donkeycar`.

```bash
pip install Adafruit_SSD1306
```

