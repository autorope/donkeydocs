# Pin Specifiers

Control signals are send and received by pins on the Raspberry Pi, Jetson Nano and connected peripherals, like the PCA9685 Servo controller.  Starting wit version 5.x, Donkeycar uses 'pin specs' to specify pins including various configuration that is specific to the underlying hardware or library implementation.  This allows use to make the underlying logic, like how a motor controller takes throttle values and outputs them to the motor, more independant of the particular hardware or library used to generate the signals.

## Types of Pins

PWM pins generate a square wave, sometimes called a PWM pulse.  This is used to control servo motors, electronic speed controllers, and LEDs.

TTL Output pins can generate either a high value (1) or a low value (0)

TTL Input pins read values as either high (1) or low (0)

## Pin Providers
Donkeycar supports several technologies for specifying pins.  Pins are specified as a string that identifies the provider, the pin number and any techology specific configuration.

#### PCA9685
The PCA9685 Servo controller supports 16 PWM and TTL output pins.  The PCA9685 can only output signals; it does not support input pins.  The pin specifier for a PCA9685 pin includes:

- the I2C bus to which the PCA9685 is attached
- the address in hex of the PCA9685 on the I2C bus
- the channel number 0.15

For example, "PCA9685.1:40.13" specifies channel 13 on the PCA9685 on I2C bus 1 at address 0x40.

For example, "PCA9685.0:60.1" specified channel 1 on the PCA9685 on I2C bus 0 at address 0x60

#### RPI_GPIO
Donkeycar installs the RPi.GPIO library on the RaspberryPi in the default installation.  The Jetson.GPIO library is compatible library installed by default on the Jetson Nano.  Both of these libaries work is a similar fashion to support PWM, input and output pins on the 40 pin GPIO bus of the RaspberryPi or Jetson Nano respectively.  The pin specifier includes:

- The pin addressing scheme
  - "BOARD" indicates the pin number is based on the pin numbers, 0 to 39, printed on the RaspberryPi/Jetson Nano circuit board.
  - "BCM" indicates the pin number is based on the Broadcom GPIO number scheme implemented in the RaspberryPi.  This scheme is emulated in the Jetson library, so "BCM" pin numbers can be used on the Jetson. 
- The pin number, which depends upon the pin addressing scheme.

See details of the RaspberryPi 40 pin header here:  https://www.raspberrypi.com/documentation/computers/os.html#gpio-and-the-40-pin-header

Jetson Nano 40 pin header uses the same board numbering scheme, although the header is physically flipped on the board, so pay attention to the numbers printed on the board.

For example, "RPI_GPIO.BOARD.33" specifies board pin 33 using the Rpi.GPIO library.

For example, "RPI_GPIO.BCM.13" specifies Broadcom GPIO-13 using the Rpi.GPIO library.  If you look at the header diagram linked above you will notice that this is the same physical pin as "RPI_GPIO.BOARD.33"; it is a synonymn for physical pin 33.  

When using the RPI_GPIO pin provider, you can choose to use the BOARD or BCM pin schemes, but all pins must use the same pin scheme.  You cannot mix pin schemes.

#### PIGPIO
RaspberryPi users can optionally install the PiGPIO library and daemon to manage the pins on the 40 pin GPIO header.  Note that this library does NOT work on the Jetson Nano.  The library support PWM, Input and Output pins.

##### Installing and Starting PiGPIO
- Install the system daemon
```bash
sudo apt-get update
sudo apt-get install pigpio
```
- Install python support (with donkey environent activated)
```bash
pip install pigpio
```
- Start the daemon
```bash
sudo systemctl start pigpiod
```
- Enable the daemon on startup
```
sudo systemctl enable pigpiod
```

The PIGPIO pin specifier includes:
- "BCM"  PiGPIO used Broadcom (BCM) pin numbering scheme exclusively, so that is baked into the pin specifier.
- The BCM pin number

For example, "PIGPIO.BCM.13" specifies Broadcom GPIO-13.  As discussed above and shown in the linked header diagram, this is exposed on board pin 33.





