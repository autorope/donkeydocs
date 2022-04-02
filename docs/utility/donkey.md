# Donkey Command-line Utilities

The `donkey` command is created when you install the donkeycar Python package. This is a Python script that adds some important functionality. The operations here are vehicle independent, and should work on any hardware configuration.

## Create Car

This command creates a new dir which will contain the files needed to run and train your robot.

Usage:

```bash
donkey createcar --path <dir> [--overwrite] [--template <donkey2>]
```

* This command may be run from any dir
* Run on the host computer or the robot
* It uses the `--path` as the destination dir to create. If `.py` files exist there, it will not overwrite them, unless the optional `--overwrite` is used.
* The optional `--template` will specify the template file to start from. For a list of templates, see the `donkeycar/templates` dir. This source template will be copied over the `manage.py` for the user.

## Find Car

This command attempts to locate your car on the local network using nmap.

Usage:

```bash
donkey findcar
```

* Run on the host computer
* Prints the host computer IP address and the car IP address if found
* Requires the nmap utility:

```bash
sudo apt install nmap
```

## Calibrate Car

This command allows you to manually enter values to interactively set the PWM values and experiment with how your robot responds.
See also [more information.](/guide/calibrate/)

Usage:

```bash
donkey calibrate --channel <0-15 channel id>
```

* Run on the host computer
* Opens the PWM channel specified by `--channel`
* Type integer values to specify PWM values and hit enter
* Hit `Ctrl + C` to exit

## Clean data in Tub

Opens a web server to delete bad data from a tub.

Usage:

```bash
donkey tubclean <folder containing tubs>
```

* Run on pi or host computer.
* Opens the web server to delete bad data.
* Hit `Ctrl + C` to exit

## Train the model
**Note:** _This section only applies to version >= 4.1_
This command trains the model.

```bash
donkey train --tub=<tub_path> [--config=<config.py>] [--model=<model path>] [--type=(linear|categorical|inferred)] [--transfer=<transfer model path>]
```
* Uses the data from the `--tub` datastore
* Uses the config file from the `--config` path (optionally)
* Saves the model into path provided by `--model`. Auto-generates a model name if omitted. _**Note:**_ There was a regression in version 4.2 where you only had to provide the model name in the model argument, like `--model mypilot.h5`. This got resolved in version 4.2.1. Please update to that version.
* Uses the model type `--type`
* Allows to continue training a model given by `--transfer`
* Supports filtering of records using a function defined in the variable 
  `TRAIN_FILTER` in the `my_config.py` file. For example: 

```bash
def filter_record(record):
    return record.underlying['user/throttle'] > 0

TRAIN_FILTER = filter_record
```
  
  only uses records with positive throttle in training.
 
* In version 4.3.0 and later all 3.x models are supported again:

```bash
donkey train --tub=<tub_path> [--config=<config.py>] [--model=<model path>] [--type=(linear|categorical|inferred|rnn|imu|behavior|localizer|3d)] [--transfer=<transfer model path>]
```

In addition, a Tflite model is automatically generated in training. This can be suppressed by setting `CREATE_TF_LITE = False` in your config. Also Tensorrt models can now be generated. To do so, you set `CREATE_TENSOR_RT = True`.

* Note: The `createcar` command still creates a `train.py` file for backward  compatibility, but it's not required for training.


## Make Movie from Tub

This command allows you to create a movie file from the images in a Tub.

Usage:

```bash
donkey makemovie --tub=<tub_path> [--out=<tub_movie.mp4>] [--config=<config.py>] [--model=<model path>] [--model_type=(linear|categorical|inferred|rnn|imu|behavior|localizer|3d)] [--start=0] [--end=-1] [--scale=2] [--salient]
```

* Run on the host computer or the robot
* Uses the image records from `--tub` dir path given
* Creates a movie given by `--out`. Codec is inferred from file extension. Default: `tub_movie.mp4`
* Optional argument to specify a different `config.py` other than default: `config.py`
* Optional model argument will load the keras model and display prediction as lines on the movie
* model_type may optionally give a hint about what model type we are loading. Categorical is default.
* optional `--salient` will overlay a visualization of which pixels excited the NN the most
* optional `--start` and/or `--end` can specify a range of frame numbers to use.
* scale will cause ouput image to be scaled by this amount


## Plot Predictions

This command allows you to plot steering and throttle against predictions coming from a trained model.

Usage:

```bash
donkey tubplot --tub=<tub_path> --model=<model_path> [--limit=<end_index>] [--type=<model_type>] 
```

* This command may be run from `~/mycar` dir
* Run on the host computer
* Will show a pop-up window showing the plot of steering values in a given tub compared to NN predictions from the trained model
* Optional `--limit=<end_index>` will use all records up to that index, defaults to 1000.
* Optional `--type=<model_type>` will use a different model type than the `DEFAULT_MODEL_TYPE`


## Tub Histogram

**_Note_**: Requires version >= 4.3

This command allows you to plot tub data (usually steering and throttle) as a histogram.

Usage:

```bash
donkey tubhist --tub=<tub_path> --record=<record_name> --out=<output_filename>
```

* This command may be run from `~/mycar` dir
* Run on the host computer
* Will show a pop-up window showing the histogram plot of tub values in a given tub
* Optional `--record=<record_name>` will only show the histogram of a certain data series, for example "user/throttle"
* Optional `--out=<output_filename>` saves histogram under that name, otherwise the name is auto-generated from the tub path

## Continuous Rsync

This command uses rsync to copy files from your pi to your host. It does so in a loop, continuously copying files. By default, it will also delete any files
on the host that are deleted on the pi. This allows your PS3 Triangle edits to affect the files on both machines.

Usage:

```bash
donkey consync [--dir = <data_path>] [--delete=<y|n>]
```

* Run on the host computer
* First copy your public key to the pi so you don't need a password for each rsync:

```bash
cat ~/.ssh/id_rsa.pub | ssh pi@<your pi ip> 'cat >> .ssh/authorized_keys'
```

* If you don't have a id_rsa.pub then google how to make one
* Edit your config.py and make sure the fields `PI_USERNAME`, `PI_HOSTNAME`, `PI_DONKEY_ROOT` are setup. Only on windows, you need to set `PI_PASSWD`.
* This command may be run from `~/mycar` dir

## Continuous Train

This command fires off the keras training in a mode where it will continuously look for new data at the end of every epoch.

Usage:

```bash
donkey contrain [--tub=<data_path>] [--model=<path to model>] [--transfer=<path to model>] [--type=<linear|categorical|rnn|imu|behavior|3d>] [--aug]
```

* This command may be run from `~/mycar` dir
* Run on the host computer
* First copy your public key to the pi so you don't need a password for each rsync:

```bash
cat ~/.ssh/id_rsa.pub | ssh pi@<your pi ip> 'cat >> .ssh/authorized_keys'
```

* If you don't have a id_rsa.pub then google how to make one
* Edit your config.py and make sure the fields `PI_USERNAME`, `PI_HOSTNAME`, `PI_DONKEY_ROOT` are setup. Only on windows, you need to set `PI_PASSWD`.
* Optionally it can send the model file to your pi when it achieves a best loss. In config.py set `SEND_BEST_MODEL_TO_PI = True`.
* Your pi drive loop will autoload the weights file when it changes. This works best if car started with `.json` weights like:

```bash
python manage.py drive --model models/drive.json
```

## Joystick Wizard

This command line wizard will walk you through the steps to create a custom/customized controller.  

Usage:

```bash
donkey createjs
```

* Run the command from your `~/mycar` dir
* First make sure the OS can access your device. The utility `jstest` can be useful here. Installed via: `sudo apt install joystick`  You must pass this utility the path to your controller's device.  Typically this is `/dev/input/js0`  However, it if is not, you must find the correct device path and provide it to the utility.  You will need this for the createjs command as well.
* Run the command `donkey createjs` and it will create a file named my_joystick.py in your `~/mycar` folder, next to your manage.py
* Modify myconfig.py to set `CONTROLLER_TYPE="custom"` to use your my_joystick.py controller

## Visualize CNN filter activations

Shows feature maps of the provided image for each filter in each of the convolutional layers in the model provided. Debugging tool to visualize how well feature extraction is performing.

Usage:

```bash
donkey cnnactivations [--tub=<data_path>] [--model=<path to model>]
```

This will open a figure for each `Conv2d` layer in the model.

Example:

```bash
donkey cnnactivations --model models/model.h5 --image data/tub/1_cam-image_array_.jpg
```

## Show Models database

**_Note:_** This is only available in donkeycar >= 4.3.1.

This lists the models that are stored in `models/database.json`. Displays information
like model type, model name, tubs used in training, transfer model and a comment if
`--comment` was used in training or the model was trained in the UI.


Usage:

```bash
donkey models [--group] 
```

* Run from your `~/mycar` directory
* If the optional `--group` flag is given, then the tub path info is combined into groups, 
if different models used different tubs. Useful, if you use multiple tubs, and the models 
have used different tub combinations because it compresses the output information. 
* You need to install `pandas` first if you want to run it on the car 


## Donkey UI

**Note:** _This section only applies to version >= 4.2.0_


Usage:

```bash
donkey ui
```

This opens a UI to analyse tub data supporting following features:

* show selected data fields live as values and graphical bars
* delete or un-delete records
* try filters for data selection
* plot data of selected data fields

The UI is an alternative to the web based `donkey tubclean`.

![Tub UI](../assets/ui-tub-manager.png)

A full documentation of the UI is [here.](./ui.md)
