# Train an autopilot with Keras

Now that you're able to drive your car reliably you can use [Keras](https://keras.io/) to train a
neural network to drive like you. Here are the steps.

## Collect Data

 Make sure you collect good data.

1. Practice driving around the track a couple times.
2. When you're confident you can drive 10 laps without mistake, restart the python mange.py process to create a new tub session. Press `Start Recording` if using web controller. The joystick will auto record with any non-zero throttle.
3. If you crash or run off the track press Stop Car immediately to stop recording. If you are using a joystick tap the Triangle button to erase the last 5 seconds of records.
4. After you've collected 10-20 laps of good data (5-20k images) you can stop
your car with `Ctrl-c` in the ssh session for your car.
5. The data you've collected is in the data folder in the most recent tub folder.

## Transfer data from your car to your computer

Since the Raspberry Pi is not very powerful, we need to transfer the data
to a PC computer to train. The Jetson nano is more powerful, but still quite slow to train. If desired, skip this transfer step and train on the Nano.

## Training the easy, GUI way

The easiest way to do the training on your desktop is by using the [Donkey UI application](https://docs.donkeycar.com/utility/ui/).
 ![Tub_manager UI](../assets/ui-tub-manager.png)

If, however, you want to do the training with the command line, read on....

## Training with the command line

In a new terminal session on your host PC use rsync to copy your cars
folder from the Raspberry Pi.

```bash
rsync -rv --progress --partial pi@<your_pi_ip_address>:~/mycar/data/  ~/mycar/data/
```

## Train a model

* In the same terminal you can now run the training script on the latest tub by passing the path to that tub as an argument. You can optionally pass path masks, such as `./data/*` or `./data/tub_?_17-08-28` to gather multiple tubs. For example:

```bash
donkey train --tub <tub folder names comma separated> --model ./models/mypilot.h5
```

* You can create different model types with the `--type` argument during training. You may also choose to change the default model type in myconfig.py `DEFAULT_MODEL_TYPE`. When specifying a new model type, be sure to provide that type when running the model, or using the model in other tools like plotting or profiling. For more information on the different model types, look here for [Keras Parts](/parts/keras). The model will be placed into the folder `models/`. You can as well omit the `--model` flag and the model name will be auto created using the pattern `pilot_YY-MM-DD_N.h5`. 

* If you run with version >= 4.3.0, the model will be automatically created in tflite format for fast inferencing, generating a `./models/mypilot.tflite` file, too. Tflite creation can be suppressed, by setting `CREATE_TF_LITE = False` in your `myconfig.py` file. In addition, a tensorrt model is produced if you set `CREATE_TENSOR_RT = True`, which is `False` by default. That setting produces a `./models/mypilot.trt` file that should work on all platforms. On RPi, the tflite model will be the fastest. 

> _**Note:**_ There was a regression in version 4.2 where you only had to provide the model name in the model argument, like `--model mypilot.h5`. This got resolved in version 4.2.1. Please update to that version.

* **Image Augmentation** With version >= 4.3.0 you also have access to image augmentations for training. Image augmentation is a technique where data, in this case images, is changed (augmented) to create variation in the data.  The purpose is two-fold.  First it can help extend your data when you don't have a lot of data.  Second it can create a model that is more resilient to variations in the data at inference time.  In our case, we want to handle various lighting conditions.  Currently supported are `AUGMENTATIONS = ['MULTIPLY', 'BLUR']` in the settings which generate brightness modifications and apply a Gaussian blur. These can be used individually or together. Augmentations are only applied during training; they are not applied when driving on autopilot.

* **Image Transformation** With version >= 4.3.0 you also have access to image transformations like cropping or trapezoidal masking.  **Cropping and masking** are similar; both 'erase' pixels on the image.  This is done to **remove pixels that are not important** and that may add unwanted detail that can make the model perform poorly under conditions where that unwanted detail is different.  Cropping can erase pixels on the top, bottom, left and/or right of the image.  Trapezoidal masking is a little more flexible in that it can mask pixels using a trapezoidal mask that can account for perspective in the image.  To crop the image or apply a trapezoidal mask you can provide `TRANSFORMATIONS = ['CROP']` or `TRANSFORMATIONS = ['TRAPEZE']`. Generally you will use either cropping or trapezoidal masking but not both.  **Transformations must be applied in the same way in training and when driving on autopilot**; make sure the transformation configuration is the same on your training machine and on your Donkey Car. 

> _**Note**_ The library used for image augmentations and transformations has an incompatibility on the Jetson Nano.  We are working on an alternative, but currently image augmentation and transformation will not work on the Jetson Nano. 


## Copy model back to car

* In previous step we managed to get a model trained on the data. Now is time to move the model back to Rasberry Pi, so we can use it for testing it if it will drive itself.

* Use rsync again to move your trained model pilot back to your car.

```bash
rsync -rv --progress --partial ~/mycar/models/ pi@<your_ip_address>:~/mycar/models/
```

* Ensure to place car on the track so that it is ready to drive.

* Now you can start your car again and pass it your model to drive.

```bash
python manage.py drive --model ~/mycar/models/mypilot.h5
```

* However, you will see better performance if you start with the tflite mode.

```bash
python manage.py drive --model ~/mycar/models/mypilot.tflite --type tflite_linear
```

* The car should start to drive on its own, congratulations!

## [Optional] Use TensorRT on the Jetson Nano

Read [this](/guide/robot_sbc/tensorrt_jetson_nano) for more information.

## Training Tips

<iframe width="560" height="315" src="https://www.youtube.com/embed/4fXbDf_QWM4" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

----

1. **Mode & Pilot**: Congratulations on getting it this far. The first thing to note after running the command above, is to look at the options in the Mode & Pilot menu. It can be pretty confusing. So here's what the different options mean:

  a. **User** : As you guessed, this is where you are in control of both the steering and throttle control.

  b. **Local Angle** : Not too obvious, but this is where the trained model (mypilot from above) controls the steering. The _Local_ refers to the trained model which is locally hosted on the raspberry-pi.

  c. **Local Pilot** : This is where the trained model (mypilot) assumes control of both the steering and the throttle. As of now, it's purportedly not very reliable.

  Be sure to also check out the **Max Throttle** and **Throttle Mode** options, and play around with a few settings. Can help with training quite a lot.

2. **Build a Simple Track** : This isn't very well-documented, but the car should (theoretically) be able to train against any kind of track. To start off with, it might not be necessary to build a two-lane track with a striped center-lane. Try with a single lane with no center-line, or just a single strip that makes a circuit! At the least, you'll be able to do an end-to-end testing and verify that the software pipeline is all properly functional. Of course, as the next-step, you'll want to create a more standard track, and compete at a [meetup](https://diyrobocars.com/local-meetup-groups/) nearest to you!

3. **Get help** : Try to get some helping hands from a friend or two. Again, this helps immensely with building the track, because it is harder than it looks to build a two-line track on your own! Also, you can save on resources (and tapes) by using a [ribbon](https://www.amazon.com/gp/product/B00L2MLCNO) instead of tapes. They'll still need a bit of tapes to hold them, but you can reuse them and they can be laid down with a lot less effort (Although the wind, if you're working outside, might make it difficult to lay them down initially).

## Training Behavior Models

<iframe width="560" height="315" src="https://www.youtube.com/embed/aLFuHGlU0CM" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

----

### How to train a Behavior model

 * Make sure ```TRAIN_BEHAVIORS = True``` in myconfig.py when training and when running on the robot.

* Setup an RGB led on robot to indicate which state is active. Enable in config.py. Verify when running robot that L1 PS3 button changes state led indicator. (that's the left upper shoulder button)

* By default there are two states. If you like, adjust the number of states in bottom of config.py. Rename or change `BEHAVIOR_LIST` to an arbitrary number of labels. Make sure same number of rgb colors in `BEHAVIOR_LED_COLORS`. Make sure to reflect any changes to both PC and Robot.

* Now for training: Activate any state with L1 shoulder button. Then drive as you wish the car to drive when in that state. Switch states and then transition to the new steady state behavior.

* For the two lane case. Drive 33% in one lane, 33% in the other, and 33% transitioning between them. It's important to trigger the state transition before changing lanes.

* Check the records in the tub. Open a .json. In addition to steering and throttle, you should also have some additional state information about your behavior vector and which was was activate on that frame. This is crucial to training correctly.

* Move data to PC and train as normal, ensuring ```TRAIN_BEHAVIORS = True``` in myconfig.py on PC, otherwise extra state information will be ignored.

* Move trained model back to robot. Now place the robot in the location of the initial state. Start the robot with the given model

```bash
python manage.py drive --model=models/my_beh.h5 --type=behavior
```

* Now press select to switch to desired AI mode. Constant throttle available as well as trained throttle.

As it drives, you can now toggle states with L1 and see whether and how much it can replicate your steady state behaviors and transitions.

Be sure to include quite a lot of example of transitions from one state to another. At least 50, but more like 100. 
