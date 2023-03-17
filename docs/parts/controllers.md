# Controller Parts

## Web Controller

The default controller to drive the car with your phone or browser. This has a web live preview of camera. Control options include:

1. A virtual joystick
2. The tilt, when using a mobile device with supported accelerometer
3. A physical joystick using the web adapter. Support varies per browser, OS, and joystick combination.
4. Keyboard input via the 'ikjl' keys.

> Note: Recently iOS has [disabled default Safari](https://www.macrumors.com/2019/02/04/ios-12-2-safari-motion-orientation-access-toggle/) access to motion control. 

## RC Controller
If you bought an RC car then it might have come with a standard 2.4GHz car radio and receiver as shown in picture below. This can be used to drive the car. There are a few ways this can be accomplished.

* You can use a Teensy or Arduino microcontroller that can emulate a USB HID device and use this [arduino sketch](https://github.com/n6wxd/wireless-rc-adapter) to make your RC controller emulate a game controller.  Instructions for wiring and using the sketch can be found in the associated [wiki](https://github.com/wireless-rc-adapter/wireless-rc-adapter/wiki).  Once that is setup you would choose `CONTROLLER_TYPE = "rc3"` as the controller type in your `myconfig.py` configuration.  
 
* You can wire your RC car's receiver directly to the Raspberry Pi's gpio pins to read the length of the PWM steering and throttle signals being sent by your RC controller.  Note that we don't recommend this for Jetson Nano users; the gpio support is not adequate. You can also use the RaspberryPi pins to output PWM directly to the car's servo and ESC, without the need for an I2C servo driver board. You will need to install the PiGPIO driver on you RaspberryPi to to make this work; it is not installed by default.   If you wire your RC receiver to gpio pins, then you would choose `CONTROLLER_TYPE = pigpio_rc` in your `myconfig.py` configuration. A full tutorial on implementing the RC controller and servo/esc control via gpio pins is [here](rc.md).

* Finally you can use the Donkeycar RC Hat.  This board plugs into your RaspberryPi's gpio header and exposes connections for your RC Reciever's servo and throttle channels.  It can also be used to control the car's steering servo and ESC, so you don't need a PCA9685 board.  It includes a very handy OLED display that can be used to show your car's IP address on startup; see this [DIYRobocars article](https://diyrobocars.com/2021/12/29/show-your-raspberrypi-ip-address-on-startup-with-an-oled/).  See the [docs](rc_hat.md) for how to setup the RC Hat.  The Donkeycar RC Hat can be purchased in the [Donkeycar Store](https://store.donkeycar.com/collections/accessories/products/donkey-car-rc-hat).

![RC Hat for RaspberryPi](../assets/rc_hat.jpg "The Donkey RC Hat for RaspberryPi")

## Joystick Controller

Many people find it easier to control the car using a game controller. There are several parts that provide this option.

The default web controller may be replaced with a one line change to use a physical joystick part for input. This uses the OS device /dev/input/js0 by default. In theory, any joystick device that the OS mounts like this can be used. In practice, the behavior will change depending on the model of joystick ( Sony, or knockoff ), or XBox controller and the Bluetooth driver used to support it. The default code has been written and tested with a [Sony brand PS3 Sixaxis controller](https://www.ebay.com/sch/i.html?_nkw=Sony+Playstation+Dualshock+PS3+controller+OEM). Other controllers may work, but will require alternative Bluetooth installs, and tweaks to the software for correct axis and buttons.

### These joysticks are known to work:

* [Logitech Gamepad F710](https://www.amazon.com/Logitech-940-000117-Gamepad-F710/dp/B0041RR0TW)
* [Sony PS3 Sixaxis OEM](https://www.ebay.com/sch/i.html?&_nkw=Sony+PS3+Sixaxis+OEM) (Not compatible with Jetson Nano)
* [Sony PS4 Dualshock OEM](https://www.ebay.com/sch/i.html?&_nkw=Sony+PS4+Dualshock+OEM)
* [WiiU Pro](https://www.amazon.com/Nintendo-Wii-U-Pro-Controller-Black/dp/B00MUY0OFU)
* [XBox Controller](https://www.amazon.com/Xbox-Wireless-Controller-Blue-one/dp/B01M0F0OIY)

These can be enabled by finding the CONTROLLER_TYPE in your myconfig.py and setting it to the correct string identifier ( after disabling the comment ).

These can be used plugged in with a USB cable. It's been much more convenient to setup Bluetooth for a wireless control.

There are controller specific setup details below.

> Note: If you have a controller that is not listed below, or you are having troubles getting your controller to work or you want to map your controller differently, see [Creating a New or Custom Game Controller](./#creating-a-new-or-custom-game-controller). 

### Change myconfig.py or run with --js

```bash
python manage.py drive --js
```

Will enable driving with the joystick. This disables the live preview of the camera and the web page features. If you modify myconfig.py to make `USE_JOYSTICK_AS_DEFAULT = True`, then you do not need to run with the `--js`.


## PS3 Controller

### Bluetooth Setup

Follow [this guide](https://pythonhosted.org/triangula/sixaxis.html). You can ignore steps past the 'Accessing the SixAxis from Python' section. I will include steps here in case the link becomes stale.

``` bash
sudo apt-get install bluetooth libbluetooth3 libusb-dev
sudo systemctl enable bluetooth.service
sudo usermod -G bluetooth -a pi
```

Reboot after changing the user group.

Plug in the PS3 with USB cable. Hit center PS logo button. Get and build the command line pairing tool. Run it:

```bash
wget http://www.pabr.org/sixlinux/sixpair.c
gcc -o sixpair sixpair.c -lusb
sudo ./sixpair
```

Use bluetoothctl to pair

```bash
bluetoothctl
agent on
devices
trust <MAC ADDRESS>
default-agent
quit
```

Unplug USB cable. Hit center PS logo button.

To test that the Bluetooth PS3 remote is working, verify that `/dev/input/js0` exists:

```bash
ls /dev/input/js0
```

#### Troubleshooting

In case the BT connection on the Raspberry Pi does not work, you see might something like this in `bluetoothctl`:

```text
[NEW] Controller 00:11:22:33:44:55 super-donkey [default]
[NEW] Device AA:BB:CC:DD:EE:FF PLAYSTATION(R)3 Controller
[CHG] Device AA:BB:CC:DD:EE:FF Connected: yes
[CHG] Device AA:BB:CC:DD:EE:FF Connected: no
[CHG] Device AA:BB:CC:DD:EE:FF Connected: yes
[CHG] Device AA:BB:CC:DD:EE:FF Connected: no
[CHG] Device AA:BB:CC:DD:EE:FF Connected: yes
...
[CHG] Device AA:BB:CC:DD:EE:FF Connected: yes
[CHG] Device AA:BB:CC:DD:EE:FF Connected: no
[bluetooth]#
```

Try updating the Linux kernel and firmware by running:

```bash
sudo rpi-update
```

And then reboot:

```bash
sudo reboot
```

### Charging PS3 Sixaxis Joystick

For some reason, they don't like to charge in a powered USB port that doesn't have an active Bluetooth control and OS driver. This means a phone type USB charger will not work. Try a powered Linux or mac laptop USB port. You should see the lights blink after plugging in and hitting center PS logo.

After charging, you will need to plug-in the controller again to the Pi, hit the PS logo, then unplug to pair again.

### New Battery for PS3 Sixaxis Joystick

Sometimes these controllers can be quite old. Here's a link to a [new battery](http://a.co/5k1lbns). Be careful when taking off the cover. Remove 5 screws. There's a tab on the top half between the hand grips. You'll want to split/open it from the front and try pulling the bottom forward as you do, or you'll break the tab off as I did.

### PS3 Mouse problems on Linux

Sometimes when you plug-in the PS3 joystick it starts taking over your mouse. If you want to prevent that, you can run this:

```bash
xinput set-prop "Sony PLAYSTATION(R)3 Controller" "Device Enabled" 0
```

## PS4 DualShock 4 Wireless Gamepad Controller
The following instructions are intended for use with the Raspberry Pi 3 or 4 running Raspberry Pi OS Buster.
The DS4 gamepad will be connected via bluetooth without installing any additional software.  Bluetoothd is a system service
that runs as a daemon automatically on boot.  Bluetoothctl is a program to manage connection and pairing devices.

#### Configure your user account to use Bluetoothctl without sudo
Add the pi user to the bluetooth group. And then reboot so that the change takes effect properly.

```bash
sudo usermod -a -G bluetooth pi
sudo reboot
```

#### Scan for your PS4 gamepad
After reboot, run bluetoothctl, turn on scanner to find bluetooth devices.  See below for an example response. Note that
the actual HEX characters will be different for your devices!


```bash
bluetoothctl
<response> Agent registered
<response> [bluetooth]# 
scan on
<response>
[CHG] Controller BB:22:EE:77:BB:CC Discovering: yes
[NEW] Device 10:20:30:40:50:60 10-20-30-40-50-60
[NEW] Device 10:20:30:40:50:70 10-20-30-40-50-70
[NEW] Device 10:20:30:40:50:80 10-20-30-40-50-80
[NEW] Device 10:20:30:40:50:90 10-20-30-40-50-90
[NEW] Device 20:AA:88:44:BB:10 WHSCL1
```

Wait a couple of minutes for the scanner to find all your existing bluetooth devices. Now set your gamepad in pairing mode
by holding down the share button and the playstation button together until the light double flashes. You should see a
new entry for a Wireless Controller.

```bash
<response>
[NEW] Device 1C:AA:BB:99:DD:AA Wireless Controller
```
Turn off scanning to stop the status reporting
```bash
scan off
```

#### Connect to your PS4 gamepad
You will now connect, pair and trust the PS4 gamepad wireless controller. Trusting the paired devices will allow you to reconnect to the device after the Raspberry Pi reboots. Copy the wireless controller address. You will
type CONNECT "your wireless controller address", TRUST "your wireless controller address". In this case, "your wireless controller address" is 1C:AA:BB:99:DD:AA

```bash
connect 1C:AA:BB:99:DD:AA
<response>
Attempting to connect to 1C:AA:BB:99:DD:AA
[CHG] Device 1C:AA:BB:99:DD:AA Connected: yes
[CHG] Device 1C:AA:BB:99:DD:AA UUIDs: 00001124-0000-1000-8000-00805f9b34fb
[CHG] Device 1C:AA:BB:99:DD:AA UUIDs: 00001200-0000-1000-8000-00805f9b34fb
[CHG] Device 1C:AA:BB:99:DD:AA ServicesResolved: yes
[CHG] Device 1C:AA:BB:99:DD:AA Paired: yes
Connection successful
```
The PS4 gamepad light should now be solid. Now TRUST the PS4 gamepad wireless controller.
```bash
trust 1C:AA:BB:99:DD:AA
<response>
[CHG] Device 1C:AA:BB:99:DD:AA Trusted: yes
Changing 1C:AA:BB:99:DD:AA trust succeeded
```
Type devices to see the paired-devices.

```bash
paired-devices
<response>
Device 1C:AA:BB:99:DD:AA Wireless Controller
```

Type quit or exit to quit the program bluetoothctl
```bash
quit
```

#### Use your PS4 gamepad wireless controller
After booting your pi, press playstation button once.  The light will flash for about 5 seconds and then turn solid.  If
the light goes off, try again. If this does work, run bluetoothctl and verify devices and paired-devices.

```bash
devices
<response>
Device 1C:AA:BB:99:DD:AA Wireless Controller

paired-devices
<response>
Device 1C:A0:B8:9B:DB:A2 Wireless Controller
```

If it fails to connect, while running bluetoothctl, press the playstation button once.  A good response will be:
```bash
<response>
[CHG] Device 1C:AA:BB:99:DD:AA Connected: yes
```

To disconnect the controller from the Raspberry Pi, press and hold the playstation button for 10 seconds.

## PS4 Controller (for Raspian Stretch)

The following instructions are based on [RetroPie](https://github.com/RetroPie/RetroPie-Setup/wiki/PS4-Controller#installation) and [ds4drv](https://github.com/chrippa/ds4drv).

#### Install `ds4drv`

Running on your pi over ssh, you can directly install it:

```bash
sudo /home/pi/env/bin/pip install ds4drv
```

#### Grant permission to `ds4drv`

```bash
sudo wget https://raw.githubusercontent.com/chrippa/ds4drv/master/udev/50-ds4drv.rules -O /etc/udev/rules.d/50-ds4drv.rules
sudo udevadm control --reload-rules
sudo udevadm trigger
```

#### Run `ds4drv`

```bash 
ds4drv --hidraw --led 00ff00
```

If you see `Failed to create input device: "/dev/uinput" cannot be opened for writing`, reboot and retry. Probably granting permission step doesn't take effect until rebooting.
Some controllers don't work with `--hidraw`. If that's the case try the command without it. `--led 00ff00` changes the light bar color, it's optional.

#### Start controller in pairing mode

Press and hold **Share** button, then press and hold **PS** button until the light bar starts blinking. If it goes **green** after a few seconds, pairing is successful.

#### Run `ds4drv` in background on startup once booted

```bash
sudo nano /etc/rc.local
```

paste:

```text
/home/pi/env/bin/ds4drv --led 00ff00
```

Save and exit. Again, with or without `--hidraw`, depending on the particular controller you are using.

To disconnect, kill the process `ds4drv` and hold **PS** for 10 seconds to power off the controller.

## XBox One Controller

### bluetooth pairing

This code presumes the built-in linux driver for 'Xbox Wireless Controller'; this is pre-installed on Raspbian, so there is no need to install any other drivers.  This will generally show up on /dev/input/js0.  There is another userland driver called xboxdrv; this code has not been tested with that driver.

The XBox One controller requires that the bluetooth disable_ertm parameter be set to true; to do this:

#### **Jetson Nano**
Adapted from: https://www.roboticsbuildlog.com/hardware/xbox-one-controller-with-nvidia-jetson-nano

1. Install these python libraries before we disable ertm.
```
sudo apt-get install nano
```

2. Add Non-root access to your input folder:
```
sudo usermod -a -G dialout $USER
sudo reboot
```

3. Install sysfsutils
```
sudo apt-get install sysfsutils
```
4.  Edit the config to disable bluetooth ertm
```
sudo nano /etc/sysfs.conf
```
- Append this to the end of the config
```
/module/bluetooth/parameters/disable_ertm=1
```
5. Reboot your computer
```
sudo reboot
```
6. Re-pair the Xbox One Bluetooth Controller
- Unpair (forget) the controller first if you already tried to pair it, then pair it again.  You can do this with the Bluetooth Manager GUI appliation that ships with Jetpack or if you are using command line, then use bluetoothctl:

  - Open terminal and type:
    ```
    bluetoothctl
    ```
  - then you should see the list of devices you have paired with and their corresponding MAC address. If you do not, type:
    ```
    paired-devices
    ```
  - To un-pair a device type (replace aa:bb:cc:dd:ee:ff with the MAC address of the device to un-pair):
    ```
    remove aa:bb:cc:dd:ee:ff
    exit
    ```
- Pair your device using either Bluetooth Manager GUI or bluetoothctl (see RaspberryPi OS instruction starting with `sudo bluetoothctl`)

Once paired you should have a solid light on the xbox button and a stable bluetooth connection.


#### **RaspberryPi OS**

* edit the file `/etc/modprobe.d/xbox_bt.conf`  (that may create the file; it is commonly not there by default)
* add the line: `options bluetooth disable_ertm=1`
* reboot so that this takes affect.
* after reboot you can verify that `disable_ertm` is set to true entering this
  command in a terminal:

  ```bash
  cat /sys/module/bluetooth/parameters/disable_ertm
  ```

* the result should print 'Y'. If not, make sure the above steps have been done correctly.

Once that is done, you can pair your controller to your Raspberry Pi using the bluetooth tool. Enter the following command into a bash shell prompt:  

```bash
sudo bluetoothctl
```

That will start blue tooth pairing in interactive mode. The remaining commands will be entered in that interactive session. Enter the following commands:

```text
agent on
default-agent
scan on
```

That last command will start the Raspberry Pi scanning for new bluetooth devices. At this point, turn on your XBox One controller using the big round 'X' button on top, then start the pairing mode by pressing the 'sync' button on the front of the controller.  Within a few minutes, you should see the controller show up in the output something like this;

```text
[NEW] Device B8:27:EB:A4:59:08 XBox One Wireless Controller
```

Write down the MAC address, you will need it for the following steps.  Enter this command to pair with your controller:

```text
connect YOUR_MAC_ADDRESS
```

where `YOUR_MAC_ADDRESS` is the MAC address you copied previously.  If it does not connect on the first try, try again.  It can take a few tries.  If your controller connects, but then immediately disconnects, your disable_ertm setting might be wrong (see above).  

Once your controller is connected, the big round 'X' button on the top of your controller should be solid white.  Enter the following commands to finish:

```text
trust YOUR_MAC_ADDRESS
quit
```

Now that your controller is trusted, it should automatically connect with your Raspberry Pi when they are both turned on.  If your controller fails to connect, run the bluetoothctl steps again to reconnect.

## Creating a New or Custom Game Controller

To discover or modify the button and axis mappings for your controller, you can use the [Joystick Wizard](/utility/donkey/#joystick-wizard). The Joystick Wizard will write a custom controller named 'my_joystick.py' to your mycar folder.  To use the custom controller, set `CONTROLLER_TYPE="custom"` in your myconfig.py.
