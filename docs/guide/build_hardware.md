# How to Build a Donkey&reg;

&nbsp;

* [Overview](build_hardware.md#overview)
* [Parts Needed](build_hardware.md#parts-needed)
* [Hardware:](build_hardware.md#hardware)
  * [Step 1: Print Parts](build_hardware.md#step-1-print-parts)
  * [Step 2: Clean up parts](build_hardware.md#step-2-clean-up-parts)
  * [Step 3: Assemble Top plate and Roll Cage](build_hardware.md#step-3-assemble-top-plate-and-roll-cage)
  * [Step 4: Connect Servo Shield to Raspberry Pi](build_hardware.md#step-4-connect-servo-shield-to-raspberry-pi)
  * [Step 5: Attach Raspberry Pi to 3D Printed bottom plate](build_hardware.md#step-5-attach-raspberry-pi-to-3d-printed-bottom-plate)
  * [Step 6: Attach Camera](build_hardware.md#step-6-attach-camera)
  * [Step 7: Put it all together](build_hardware.md#step-7-put-it-all-together)
* [Software](install_software.md)

## Overview

The latest version of the software installation instructions are maintained in the [software instructions](install_software.md) section.   Be sure to follow those instructions after you've built your car. 

## Choosing a Car

There are 4 fully supported chassis all made under the "Exceed" Brand, all are very similar and should be considered equivalent.  Note, often some of these are out of stock, so go through the links to find one that is in stock.  If they are out of stock on Amazon, you can find the cars at the [Exceed Website](https://www.nitrorcx.com/1rcelca.html)

*  Exceed Magnet [Blue](https://amzn.to/2BkBRka), [Red](https://amzn.to/3qKksIC)
*  Exceed Desert Monster [Green](https://amzn.to/2MhOfn6)
*  Exceed Short Course Truck  [Green](https://amzn.to/2Bek0ew),  [Red](https://amzn.to/3cmCNkE)
*  Exceed Blaze [Blue](https://amzn.to/3cnP9ci), [Yellow](https://amzn.to/2zOGthR), [Wild Blue](https://amzn.to/3dkI4uD), [Max Red](https://amzn.to/2TWOxE8)

These cars are electrically identical but have different tires, mounting and other details.  It is worth noting that the Desert Monster, Short Course Truck and Blaze all require adapters which can be easily printed or purchased from the donkey store.  These are the standard build cars because they are mostly plug and play, both have a brushed motor which makes training easier, they handle rough driving surfaces well and are inexpensive.

Here is a [video](https://youtu.be/UucnCmCAGTI) overview of the different cars and how to assemble them.

For advanced users there 3 more cars supported under the "Donkey Pro" name.  These are 1/10 scale cars which means that they are bigger, perform a little better and are slightly more expensive.  They can be found here:

*  HobbyKing Trooper (not pro version) [found here](https://hobbyking.com/en_us/turnigy-trooper-sct-4x4-1-10-brushless-short-course-truck-arr.html?affiliate_code=XFPFGDFDZOPWEHF&_asc=9928905034)
*  HobbyKing Mission-D [found here](https://hobbyking.com/en_us/1-10-hobbykingr-mission-d-4wd-gtr-drift-car-arr.html?affiliate_code=XFPFGDFDZOPWEHF&_asc=337569952)
*  Tamaya TT01 or Clone [commonly used knockoff found here](https://amzn.to/2MdNLhZ) - found worldwide but usually has to be built as a kits.  The other two cars are ready to be donkified, this one, however is harder to assemble.  

Here is a [video](https://youtu.be/K-REL9aqPE0) that goes over the different models.  The Donkey Pro models are not yet very well documented, just a word of warning.  

For more detail and other options, follow the link to: [supported cars](/supported_cars)

![donkey](../assets/build_hardware/donkey.png)

## Roll Your Own Car

Alternatively If you know RC or need something the standard Donkey does not support, you can roll your own.  Here is a quick reference to help you along the way.  [Roll Your Own](../roll_your_own)

## Video Overview of Hardware Assembly

This [video](https://www.youtube.com/watch?v=OaVqWiR2rS0&t=48s) covers how to assemble a standard Donkey Car, it also covers the Sombrero, the Raspberry Pi and the nVidia Jetson Nano.  

[![IMAGE ALT TEXT HERE](../assets/HW_Video.png)](https://www.youtube.com/watch?v=OaVqWiR2rS0&t=48s)

## Parts Needed

The following instructions are for the Raspberry Pi, below in Optional Upgrades section, you can find the NVIDIA Jetson Nano instructions.  

### Option 1: Buying through an official Donkey Store

There are two official stores:

If you are in the US, you can use the [Donkey store](https://store.donkeycar.com).  The intention of the Donkey Store is to make it easier and less expensive to build the Donkey Car.  The Donkey Store is run by the original founders of donkey car and profits are used to fund development of the donkey cars.  Also it is worth noting the design of the parts out of the Donkey store is slightly improved over the standard build as it uses better parts that are only available in large quantities or are harder to get.  The Donkey Store builds are open source like all others.   

If you are in Asia, the DIYRobocars community in Hong Kong also sells car kits at [Robocar Store](https://www.robocarstore.com/products/donkey-car-starter-kit).  They are long term Donkey community members and use proceeds to support the R&D efforts of this project. It is worth noting they can also sell to Europe and the US but it is likely less cost effective.  

| Part Description                                                                    | Link                                                                                  | Approximate Cost |
|-------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|------------------|
| Exceed Magnet, Desert Monster, Blaze, or Short Course Truck                                                                       | See links above                                     | ~$90              |
| USB Battery with microUSB cable (any battery capable of 2A 5V output is sufficient) | [Anker 6700 mAh](https://amzn.to/2AlMQJz) or [Anker Powercore II](https://amzn.to/3hPPHeX)                                           | $22              |
| Raspberry Pi 3b+                                                                      | [Pi 3b+](https://amzn.to/3gCUcZL)                                          | $42              |
| MicroSD Card (many will work, we strongly recommend this one)             | 64GB [https://amzn.to/2XP7UAa](https://www.amazon.com/SanDisk-128GB-Extreme-microSD-Adapter/dp/B07FCMKK5X?tag=donkeycar-20)                            | $11.99           |
| Donkey Partial Kit                                                      | [KIT](https://store.donkeycar.com/collections/frontpage)                                        | $82 to $125              |

### Option 2: Bottoms Up Build

If you want to buy the parts yourself, want to customize your donkey or live outside of the US, you may want to choose the bottoms up build.  Keep in mind you will have to print the donkey car parts which can be found [here](https://www.thingiverse.com/thing:2566276)

| Part Description                                                                    | Link                                                                                  | Approximate Cost |
|-------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|------------------|
| Magnet Car or alternative                                                                        | see cars above under 'choosing a car'                                         | $92              |
| M2x6 screws (8)                                                                     | [Amazon](https://amzn.to/2ZSKa0D) or [Donkey Store](https://store.donkeycar.com/collections/accessories/products/plastic-thread-forming-screw-kit)                                          | $4.89 &ast;          |
| M3x10 screws (3)                                                                  | [Amazon](https://amzn.to/3gBQuzE)  or [Donkey Store](https://store.donkeycar.com/collections/accessories/products/plastic-thread-forming-screw-kit)                                                    | $7.89 &ast;          |
| USB Battery with microUSB cable (any battery capable of 2A 5V output is sufficient) | [Anker 6700 mAh](https://amzn.to/2AlMQJz)                                           | $22              |
| Raspberry Pi 3b+                                                                      | [Pi 3B+](https://amzn.to/3gCUcZL)                                          | $38              |
| MicroSD Card (many will work, I like this one because it boots quickly)             | [64GB](https://amzn.to/2XP7UAa)                                         | $18.99           |
| Wide Angle Raspberry Pi Camera                                                      | [Amazon](https://amzn.to/2TYCo1s) or [Donkey Store](https://store.donkeycar.com/collections/accessories/products/wide-angle-raspberry-pi-camera-for-donkey)                                       | $25              |
| Female to Female Jumper Wire                                                        | [Amazon](https://amzn.to/36RiMlo) or [Donkey Car Store](https://store.donkeycar.com/collections/accessories/products/servo-driver-pca-9685-with-jumper-cables)                                         | $7 &ast;             |
| (Optional if you don't want to use RPi GPIO pins to control the car's servo and throttle directly) Servo Driver PCA 9685                                                               | [Amazon](https://amzn.to/2BbVYkj) or [Donkey Car Store](https://store.donkeycar.com/collections/accessories/products/servo-driver-pca-9685-with-jumper-cables)                                          | $12 &ast;&ast;           |
| 3D Printed roll cage and top plate.                                                 | Purchase: [Donkey Store](https://store.donkeycar.com/collections/plastics-and-screws/products/standard-donkey-chassis-includes-screws) Files: [thingiverse.com/thing:2260575](https://www.thingiverse.com/thing:2566276) | $50                 |

&ast; If it is hard to find these components, there is some wiggle room. Instead of an M2 you can use an M2.2, m2.3 or #4 SAE screw.  Instead of an M3 a #6 SAE screw can be used.  Machine screws can be used in a pinch.  

&ast;&ast; This component can be purchased from Ali Express for ~$2-4 if you can wait the 30-60 days for shipping.

### Optional Upgrades

* **NVIDIA JetsonNano Hardware Options**  The NVIDIA Jetson Nano is fully supported by the donkey Car.  To assemble the Donkey Car you will need a few parts including the Wifi card, Antennas and camera.  In addition you will need this [Adapter](https://store.donkeycar.com/products/jetson-donkey-adapter). If you want to print it yourself, it is on the Thingiverse page for the project. Due to the higher power usage and consumption you should consider the 10Ahr 3A USB battery pack listed below and a good cable rated for 3A.

![adapter](../assets/Jetson_Adapter.jpg)

Plug in the Servo driver the same as the Raspberry Pi, just keep in mind that the Jetson pinout is reversed and that the Sombrero is not supported.

![Jetson Servo](../assets/Servo_Wiring.png)

Finally this is the Donkey Assembled.  

![Jetson Assembled](../assets/Jetbot_Assembled.png)

| Part Description                                      | Link                                                              | Approximate Cost |
|-------------------------------------------------------|-------------------------------------------------------------------|------------------|
| Nvidia Jetson Nano | [Amazon](https://amzn.to/2TTGHLK)| $99 |
| Jetson Nano Adapter | [Donkey Store](https://store.donkeycar.com/products/jetson-donkey-adapter) | $7          |
| Camera Module | [Donkey Store](https://store.donkeycar.com/products/nvidia-jetson-camera-for-donkey)| $27 |
| WiFi Card | [Amazon](https://amzn.to/2UcHszJ) | $18|
| Antennas | [Donkey Store](https://store.donkeycar.com/products/2x-molex-wifi-antennas-for-jetson-nano)|$7|


For other options for part, feel free to look at the jetbot documentation [here](https://github.com/NVIDIA-AI-IOT/jetbot).

* **Sombrero Hat** NOTE: the Sombrero is out of stock at any stores - we are looking at other options or will place another order.  The sombrero hat replaces the Servo driver and the USB battery and can be purchased at the Donkeycar store [here](https://store.donkeycar.com/collections/accessories/products/sombrero) and video instructions can be found [here](https://www.youtube.com/watch?v=vuAXdrtNjpk). Implementing the Sombrero hat requires a LiPo battery (see below).  Documentation is in [Github](https://github.com/autorope/Sombrero-hat).

![sombrero](../assets/Sombrero_assembled.jpg)

* **LiPo Battery and Accessories:** LiPo batteries have significantly better energy density and have a better dropoff curve.  See below (courtesy of Traxxas).

![donkey](../assets/build_hardware/traxxas.png)

| Part Description                                      | Link                                                              | Approximate Cost |
|-------------------------------------------------------|-------------------------------------------------------------------|------------------|
| LiPo Battery                                          | [hobbyking.com/en_us/turnigy-1800mah-2s-20c-lipo-pack.html](https://hobbyking.com/en_us/turnigy-1800mah-2s-20c-lipo-pack.html?affiliate_code=XFPFGDFDZOPWEHF&_asc=1096095044) or [amazon.com/gp/product/B0072AERBE/](https://www.amazon.com/gp/product/B0072AERBE/) | $8.94 to $~17           |
| Lipo Charger (takes 1hr to charge the above battery)  | [charger](https://amzn.to/3gE6DVe)                                               | $13              |
| Lipo Battery Case (to prevent damage if they explode) | [lipo safe](https://amzn.to/2XkUhcV)                                               | $8               |

## Hardware

If you purchased parts from the Donkey Car Store, skip to step 3.

### Step 1: Print Parts

If you do not have a 3D Printer, you can order parts from [Donkey Store](https://store.donkeycar.com/collections/plastics-and-screws/products/standard-donkey-chassis-includes-screws), [Shapeways](https://www.shapeways.com/) or [3dHubs](https://www.3dhubs.com/).  I printed parts in black PLA, with 2mm layer height and no supports.  The top roll bar is designed to be printed upside down.   Remember that you need to print the adapters unless you have a "Magnet"

I printed parts in black PLA, with .3mm layer height with a .5mm nozzle and no supports.  The top roll bar is designed to be printed upside down.  

### Step 2: Clean up parts

Almost all 3D Printed parts will need clean up.  Re-drill holes, and clean up excess plastic.

![donkey](../assets/build_hardware/2a.png)

In particular, clean up the slots in the side of the roll bar, as shown in the picture below:

![donkey](../assets/build_hardware/2b.png)

### Step 3: Assemble Top plate and Roll Cage

If you have an Exceed Short Course Truck, Blaze or Desert Monster watch this [video](https://youtu.be/UucnCmCAGTI)

This is a relatively simple assembly step.   Just use the 3mm self tapping screws to scew the plate to the roll cage.  

When attaching the roll cage to the top plate, ensure that the nubs on the top plate face the roll-cage. This will ensure the equipment you mount to the top plate fits easily.

### Step 4: Connect Servo Shield to Raspberry Pi

***note: this is not necessary if you are using direct control with [RaspberyPi GPIO pins](https://docs.donkeycar.com/parts/rc/) or are using a Robohat MM1 board***

The PCA9685 Servo controller can control up to 16 PWM devices like servos, motor controllers, LEDs or almost anything that uses a PWM signal.  It is connected to the RaspberryPi (or Jetson Nano) 40 pin GPIO bus via the I2C pins.

*   GPIO I2C bus 1
    *   SDA is board pin 03
    *   SCL is board pin 05
*   Wiring
    *   SDA and SCL may be through a shared bus rather than a direct connection between nano and PCA9685 if other devices are using the I2C bus (like an OLED display)
    *   3.3v VCC power may be provided by a 3.3v pin on the GPIO bus (typically board pin 01).
    *   5v VIN should NOT be provided by the GPIO bus because motors/servos may draw too much power.  Most Electronic Speed Controllers actually provide the necessary power via the 3 pin cables that get plugged into the PCA9685, so it is generally not necessary to provide power directly to VIN.
    *   All GND must be common ground.  On the GPIO it is usually easiest to use GPIO board pin 06 for ground.  Once again the 3 pin cables from the ESC carry ground and the PCA9685 connects this to the GPIO via the GND pin.

```
---
    GPIO   ... PCA9685  ... 5v ... ESC ... Servo
    pin-03 <---> SDA
    pin-05 <---> SCL
    GND-06 <---> GND 
    3v3-01 <---> VCC
                 VIN  <---> 5v   optional, see above
                 GND  <---> GND
                 CH-0 <---------> ESC
                 CH-1 <------------------> Servo
---
```


*   checking connections
    *   The PCA9685 should appear on I2C bus 1 at address 0x40
    *   ssh into the Donkeycar and use i2cdetect to read bus 1.  A device should exist at address 0x40


```
---
    $ i2cdetect -y -r 1
         0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f
    00:          -- -- -- -- -- -- -- -- -- -- -- -- -- 
    10: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
    20: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
    30: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
    40: 40 -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
    50: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
    60: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
    70: UU -- -- -- -- -- -- --                         
---
```

You could do this after attaching the Raspberry Pi to the bottom plate, I just think it is easier to see the parts when they are laying on the workbench.  Connect the parts as you see below:

![donkey](../assets/build_hardware/4a.png)

For reference, below is the Raspberry Pi Pinout for reference.  You will notice we connect to 3.3v, the two I2C pins (SDA and SCL) and ground:

![donkey](../assets/build_hardware/4b.png)

### Step 5: Attach Raspberry Pi to 3D Printed bottom plate

Before you start, now is a good time to insert the already flashed SD card and bench test the electronics.  Once that is done, attaching the Raspberry Pi and Servo is as simple as running screws through the board into the screw bosses on the top plate.  The M2.5x12mm screws should be the perfect length to go through the board, the plastic and still have room for a washer.  The “cap” part of the screw should be facing up and the nut should be on the bottom of the top plate.  The ethernet and USB ports should face forward.  This is important as it gives you access to the SD card and makes the camera ribbon cable line up properly.

Attach the USB battery to the underside of the printed bottom plate using cable ties or velcro.

![donkey](../assets/build_hardware/5ab.png)

### Step 6: Attach Camera

Slip the camera into the slot, cable end first.  However, be careful not to push on the camera lens and instead press the board.
![donkey](../assets/build_hardware/assemble_camera.jpg)

If you need to remove the camera the temptation is to push on the lens, instead push on the connector as is shown in these pictures.  
![donkey](../assets/build_hardware/Remove--good.jpg) ![donkey](../assets/build_hardware/Remove--bad.jpg)

Before using the car, remove the plastic film or lens cover from the camera lens.

![donkey](../assets/build_hardware/6a.png)

It is easy to put the camera cable in the wrong way so look at these photos and make sure the cable is put in properly.  There are loads of tutorials on youtube if you are not used to this.

![donkey](../assets/build_hardware/6b.png)

### Step 7: Put it all together

*** Note if you have a Desert Monster Chassis see 7B section below ***
The final steps are straightforward.  First attach the roll bar assembly to the car.  This is done using the same pins that came with the vehicle.  

![donkey](../assets/build_hardware/7a.png)

Second run the servo cables up to the car.  The throttle cable runs to channel 0 on the servo controller and steering is channel 1.

![donkey](../assets/build_hardware/7b.png)

Now you are done with the hardware!!

### Step 7b: Attach Adapters (Desert Monster only)

The Desert monster does not have the same set up for holding the body on the car and needs two adapters mentioned above.  To attach the adapters you must first remove the existing adapter from the chassis and screw on the custom adapter with the same screws as is shown in this photo:

![adapter](../assets/build_hardware/Desert_Monster_adapter.png)

Once this is done, go back to step 7

## Software

Congrats!  Now to get your get your car moving, see the [software instructions](install_software.md) section.

![donkey](../assets/build_hardware/donkey2.png)

> We are a participant in the Amazon Services LLC Associates Program, an affiliate advertising program designed to provide a means for us to earn fees by linking to Amazon.com and affiliated sites.
