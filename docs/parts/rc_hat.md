# Donkeycar RC Hat
![RC Hat for RaspberryPi](../assets/rc_hat.jpg "The Donkey RC Hat for RaspberryPi")

If you started with a ready-to-run RC car, it probably came with a RC controller. Good news: you can use it with Donkeycar, using the RC controller for manual driving. You can also plug in the car's servo and motor controller directly into the RaspberryPi without the need for a PCA9685 motor/servo controller board. 

Note that you will want your RC controller to be well trimmed prior to using it with the RC hat.  You want the throttle trim, steering trim and steering range to be well adjusted; see this [video](https://www.youtube.com/watch?v=NuVQz7FCAZk) for how to do that.

To do so, you can either wire up it up manually as shown in [this tutorial](rc.md) (which works, but has a lot of fiddly wires that can fall off) or do it far more neatly with the Donkeycar RC hat, shown above, which handles all the wiring for you, along with including an OLED screen. 

The Donkeycar RC hat can be purchased from the [Donkeycar Store](https://www.diyrobocars.com/shop/).

If you're using a standard [wheel encoder](odometry.md), you can plug it into the "Encoder" pins. You can also power the RaspberryPi from this board if you have a 5V source with the "Optional 5v power in" pins

Once you've plugged in all the cables, you can move to the software setup. Instructions for using it are [here](https://www.diyrobocars.com/2024/12/22/using-the-rc-hat/)


### Troubleshooting

If you are unable to start the car, ensure that the `Adafruit_SSD1306` package is installed in your virtual environment. This should automatically be installed, if you are using a recent version of `donkeycar`.

```bash
pip install Adafruit_SSD1306
```

## Encoder
If you're using a standard [wheel encoder](odometry.md), you can plug it into the "Encoder" pins, then setup the encoder configuration in your myconfig.py to use the pin that is exposed by the RC hat's encoder header.
