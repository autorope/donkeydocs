# Calibrate your Car

The point of calibrating your car is to make it drive consistently.  If you have a steering servo then donkey needs to know the PWM values associated with full left and full right turns.  If you have an ESC, then donkey needs to know the PWM values for full forward throttle, stopped and full reverse throttle.  You figure out those values in the calibration process, then save them to your myconfig.py file so they can be used then the car is driving.

Some kinds of drivetrains do not need to be calibrated.  If you are using any drivetrain that uses an L298N motor controller or similar, then no calibraton is necessary; those drivetrains do not use PWM; they use a duty cycle that does not need to be calibrated.  Most of the differential drivetrains are of that type.  If your drivetrain uses an L298N motor controller or similar for throttle, but uses a servo for steering, then you will need to calibrate steering, but not throttle.

There is a more complete discussion of drivetrains in [Actuators](../parts/actuators.md)

## How to adjust your car's settings

>You will need to ssh into your Pi to do the calibration.

All of the car's default settings are in the `config.py`.  You can override the default settings by editing the `myconfig.py` script in your car directory.  This was generated when you ran the `donkey createcar --path ~/mycar` command. You can edit this file on your car by running:

```bash
nano ~/mycar/myconfig.py
```

## Steering Calibration

> **Make sure your car is off the ground to prevent a runaway situation.**  

1. Turn on your car.
2. Find the servo's 3-pin cable and make sure it is connected property to the PWM output pins. 
    - if using the Donkey Hat, the 3-pin servo cable will be plugged into the hat.  See [Controlling ESC and Steering Servo with RC Hat](../parts/rc_hat.md/#controlling-esc-and-steering-servo-with-rc-hat) for a complete description on the connections.
    - if using a PCA9685, the servo cable will be connected to one of the PCA9685's 3-pin input channels.  See [Connect Servo Shield to Raspberry Pi](build_hardware.md/#step-4-connect-servo-shield-to-raspberry-pi) for a description.
3. Run `donkey calibrate ...` and provide it with arguments to specify which pin will produce the PWM
    - When calibrating a drivetrain that uses pin specifiers, like `PWM_STEERING_THROTTLE`, then use `--pwm-pin` argument to specify the target pin,  like `RPI_GPIO.BOARD.33` or `PCA9685.1:40.13`.  If you are using the [Donkey Hat](../parts/rc_hat.md/#controlling-esc-and-steering-servo-with-rc-hat) then you would use `donkey calibrate --pwm-pin=PIGPIO.BCM.13` to calibrate steering.  See [Pins](../parts/pins.md) for a more complete discussion of pins and pin specifiers.
    - When using a legacy PCA9685 drivetrain, like `I2C_SERVO`, then specify the PCA9685 channel (the index of the 3-pin connector that cable in connected to) and the I2C bus the PCA9685 is connected to; `donkey calibrate --channel <your_steering_channel> --bus=<your_i2c_bus>`
4. First **find the value that turns the tires all the way to the left** extreme. When calibrating steering you want to **choose the value that just turns the wheels to the maximum**; the wheels should turn all the way but **the servo should _NOT_ make a whining noise**.  Try the value `360` and you should see the wheels on your car move slightly. If not try `400` or `300`.  Next enter values +/- 10 from your starting value to find the PWM setting that makes your car turn all the way left, again making sure the motor is not making a whining sound. Remember this value.
5. Next **find the value that turns the tires all the way to the right** extreme.  Enter values +/- 10 from your starting value to find the PWM setting that makes your car turn all the way right, again making sure the motor is not making a whining sound. Remember this value.

**Edit the `myconfig.py` script on your car** and enter these values as `STEERING_LEFT_PWM` and `STEERING_RIGHT_PWM` respectively.

- `STEERING_LEFT_PWM`  = PWM for full left turn
- `STEERING_RIGHT_PWM` = PWM value for full right turn

## Throttle Calibration

1. Find the ESC's 3-pin cable and make sure it is connected property to the PWM output pins. 
    - if using the Donkey Hat, the ESC's 3-pin cable will be plugged into the hat.  See [Controlling ESC and Steering Servo with RC Hat](../parts/rc_hat.md/#controlling-esc-and-steering-servo-with-rc-hat) for a complete description on the connections.
    - if using a PCA9685, the ESC's 3-pin cable will be connected to one of the PCA9685's 3-pin input channels.  See [Connect Servo Shield to Raspberry Pi](build_hardware.md/#step-4-connect-servo-shield-to-raspberry-pi) for a description.
2. Run `donkey calibrate ...` and provide it with arguments to specify which pin will produce the PWM
    - When calibrating a drivetrain that uses pin specifiers, like `PWM_STEERING_THROTTLE`, then use `--pwm-pin` argument to specify the target pin, like `RPI_GPIO.BOARD.33` or `PCA9685.1:40.13`.  If you are using the [Donkey Hat](../parts/rc_hat/#controlling-esc-and-steering-servo-with-rc-hat) then you would use `donkey calibrate --pwm-pin=PIGPIO.BCM.18` to calibrate throttle.  See [Pins](../parts/pins.md) for a more complete discussion of pins and pin specifiers.
    - When using a legacy PCA9685 drivetrain, like `I2C_SERVO`, then specify the PCA9685 channel (the index of the 3-pin connector that cable in connected to) and the I2C bus the PCA9685 is connected to; `donkey calibrate --channel <your_throttle_channel> --bus=<your_i2c_bus>`
3. Enter `370` when prompted for a PWM value.
4. You should hear your ESC beep indicating that it's calibrated.
5. Enter `400` and you should see your cars wheels start to go forward. If not,
its likely that this is reverse, try entering `330` instead.
6. Keep trying different values until you've found a reasonable max speed and remember this PWM value.

Reverse on RC cars is a little tricky because the ESC must receive a reverse pulse, zero pulse, reverse pulse to start to go backwards. To calibrate a reverse PWM setting...

1. Use the same technique as above set the PWM setting to your zero throttle.
2. Enter the reverse value, then the zero throttle value, then the reverse value again.
3. Enter values +/- 10 of the reverse value to find a reasonable reverse speed. Remember this reverse PWM value.

Now open your `myconfig.py` script and enter the PWM values for your car into the throttle_controller part:

* `THROTTLE_FORWARD_PWM` = PWM value for full throttle forward
* `THROTTLE_STOPPED_PWM` = PWM value for zero throttle
* `THROTTLE_REVERSE_PWM` = PWM value at full reverse throttle

## Fine tuning your calibration

![fine calibration](../assets/fine_calibration.gif)

Now that you have your car roughly calibrated you can try driving it to verify that it drives as expected. Here's how to fine tune your car's calibration.

First and most importantly, **make sure your car goes perfectly straight** when no steering input is applied.

1. Start your car by running `python manage.py drive`.
2. Go to `<your_cars_hostname.local>:8887` in a browser.
3. Press the `i` key on your keyboard a couple of times to get the car to move forward.  This is best done if you have your car on a very flat floor with some kind of grid, so you can guage if it going straight.  Be careful not to confuse driving off at an angle versus driving along an arc. Driving at an angle may simply mean you pointed the car at an angle when starting it.  Driving a curved arc indicates the car is steering.
4. If your car tends to turn left without steering applied then update the `STEERING_LEFT_PWM` in your **myconfig.py** file so it is closer to neutral.  For example, if `STEERING_LEFT_PWM` is 460 and `STEERING_RIGHT_PWM` is 290, then reduce `STEERING_LEFT_PWM` a little, maybe 458.
5. If your car tends to steer right with no steering applied, then update `STEERING_RIGHT_PWM` in your **myconfig.py** file so it is closer to neutral.  For example, if `STEERING_LEFT_PWM` is 460 and `STEERING_RIGHT_PWM` is 290, then increase `STEERING_RIGHT_PWM` a little, maybe 292.
6. Repeat this process a couple of times until you have your car driving straight.

Next, try to make it so that a full left turn and a full right turn are the same turn angle (they make the same diameter circle when driven all the way around). 

> Note : optional

1. Start your car by running `python manage.py drive`.
2. Go to `<your_cars_hostname.local>:8887` in a browser.
3. Press `j` until the cars steering is all the way right.
4. Press `i` a couple times to get the car to go forward.
5. Measure the diameter of the turn and record it on a spreadsheet.
6. Repeat this measurement for different steering values for turning each direction.
7. Chart these so you can see if your car turns the same in each direction.

Corrections:

* If your car turns the same amount at an 80% turn and a 100% turn, change the PWM setting for that turn direction to be the PWM value at 80%.
* If your car is biased to turn one direction, change the PWM values of your turns in the opposite direction of the bias.

After you've fine tuned your car the steering chart should look something like this.

![calibration graph](../assets/calibration_graph.png)

You may need to iterate making sure the car is driving straight and that the left and right turns are the same to get those both to work.  Prioritize making sure the car drives straight.

### Next let's [get driving!](/guide/get_driving)
