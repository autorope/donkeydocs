# Donkeycar Software Architecture

Donkeycar is very simple; code is organized into parts that take inputs and return outputs.  These parts are added to a vehicle.  The vehicle loop, once started, runs the parts in order.  The parts effectively communicate by reading and mutating the vehicle memory.

A 'template' is a python file that contains code to construct a 'vehicle' and one or more 'parts'.  A part is a Python class that wraps a functional component of a vehicle.  The parts are added to the vehicle.  The parts can take values from the vehicle's memory as inputs and can write values to the vehicle's memory as outputs.  When the vehicle loop is started the parts are run in the order that they were added; getting their inputs from memory and outputing their results to memory.  This continues in a loop until the vehicle is stopped, then all the parts are shutdown and the template exits.

## Templates
When you create your car application using the `donkey createcar ...` command as described in the [Create Donkeycar App](https://docs.donkeycar.com/guide/create_application/) section of the docs, what happens under the hood is that a few files are copied from the `donkeycar/templates` folder into your my car folder.  The two we need to talk about are manage.py and myconfig.py.

The files that are copied to the mycar folder are renamed versions of a pair of template files in the templates folder.  The files are chosen based on the template name you passed in the `--template` argument to the `createcar` command; if you pass nothing then the default is `--template = complete`.  So `donkey createcar --path=~/mycar` is the same as `donkey createcar --path=~/mycar --template=complete`.  In this case then the files that are renamed and copied to `~/mycar/manage.py` and `~/mycar/myconfig.py` are `donkeycar/templates/complete.py` and `donkeycar/templates/cfg_complete.py` respectively. If you create a path follow application by passing `--template=path_follow` to createcar, then the files that are copied are `donkeycar/templates/path_follow.py` and `donkeycar/templates/cfg_path_follow.py`

Now technically another copy of the `donkeycar/template/cfg_xxxx.py` is copied to the mycar folder as `config.py`; this contains the default configuration and should not be edited.  The myconfig.py file is really a commented out version of config.py.  To change your app's configuration (like to choose the kind of camera or drivetrain) uncomment the section you care about in myconfig.py and edit it.

The manage.py file is where that action really is; this is the code that runs your car.  It is organized into a 'vehicle loop' that runs at the rate specified by the `DRIVE_LOOP_HZ` value in your myconfig.py file; that is how often the vehicle loop's 'parts' will get updated.  The donkeycar vehicle loop is a pipeline of what we call 'parts' that get and set state in a hashmap we call 'memory'. 

The complete.py and path_follow.py templates are fairly complex because they are very configurable.  However they are not in anyway special.  You can create your own template to do what you want; or you don't have create or use a template at all; you can write your own `manage.py` directly.  Here is an example of a vehicle loop with a single part that will accept a number, multiply it by a random number and return the result.  As the vehicle loop runs, the value will continue to get randomized.

```python
import random

# the randomizer part
class RandPercent:
    def run(self, x):
        value = x * random.random()
        print(f"{x} -> {value}")
        return value

# create the vehicle and it's internal memory
V = dk.Vehicle()

# initialize the value in the vehicle's memory and give it a name
V.mem['var'] = 4

# add the part to read and write to the same value.
V.add(RandPercent, inputs=['var'], outputs=['var'])

# start the vehicle loop running; quit after 5 loops
V.start(max_loops=5)
```

## Parts
A part is a Python class that wraps a functional component of a vehicle.

These include:

* Sensors - Cameras, Lidar, Odometers, GPS ...
* Actuators - Motor Controllers
* Pilots - Lane Detectors, Behavioral Cloning models, ...
* Controllers - Web based or Bluetooth.
* Stores - Tub, or a way to save data.

Tawn Kramer has created a video (actual two parts) that walks through [how to make a part](https://www.youtube.com/watch?v=YZ4ESrtfShs). Also, there is a video of a presentationat the Arm AIoT conference that shows [how the OLED part was created](https://youtu.be/GOkYPXheWSY?t=1213).

Each part is constructed and then added to the vehicle loop with its named inputs and named outputs and an optional run_condition specified.  The vehicle's parts are (for the most part) executed in the order that they are added to the vehicle loop.  Each time the vehicle loop runs the part's inputs are read from vehicle memory and passed to the part's run() method, the run() method does it's work, and it's return values are assigned to the output values.  If there is a run_condition, then the part's run() method is only called in the value of the run_condition property is True; so if the run_condition property is False then the part is 'turned off'.

- **memory**: Vehicle memory is a hash map of named values.  It is the 'state' of the vehicle.  In includes values uses as inputs, outputs and conditions.  It is shared by all parts.
- **inputs**: inputs are memory values passed to the run() method of a part; they are declared when the part is added to the vehicle loop.  So for the aiLauncher example, when we add the part we include the argument, `inputs=['user/mode', 'pilot/throttle']`. Just before the run method is called, the vehicle loop looks up the input values and then passes them to the part's run() method as arguments. So when the aiLauncher part's run() method is called it will be passed two arguments; the first will be the value of the `user/mode` property in vehicle memory and the second will be the value of the `pilot/throttle` property. Note that the number of inputs declared when the part is added must match the number of arguments in the part's run() method otherwise a runtime error results. 
- **outputs**: outputs are memory values that are returned by the run() method of the part; they are declared when the part is added to the vehicle loop.  After the part's run method is called, the return values are assigned to named output properties. So for the aiLauncher example, when we add the part we include the argument, `outputs=['pilot/throttle']`. When the aiLauncher part finishes running, it will return a single value and that value will be assigned to the `'pilot/throttle'` property in vehicle memory.  Note that the number of outputs declared when the part is added must match the number of returned values in the part's run() method otherwise a runtime error results. 
- **run_condition**: the run_condition is a boolean memory value that can be used to decide if a part's run method is called.  If the condition is True then the part's run() method is called, otherwise it is not called.  This is a way to turn on and off a part.  So for instance, if we only ever wanted aiLauncher to run when in autopilot mode, we would maintain a named value, let's say `'run_pilot'`, that was True when running in autopilot mode and False when running in user (manual) mode.  Then we would pass `run_condition='run_pilot'` to the V.add() method when we added the aiLauncher part to the vehicle. The aiLaucher's run() method would only be called if the named memory value `'run_pilot'` was True.

So you can see that you can control how a part operates by changing the value of its input properties.  One part can affect another parts by outputting values (and so changing them) that are used as inputs or run_conditions by other parts.

Here is an example of adding a part; the [AiLaunch part](https://github.com/autorope/donkeycar/blob/main/donkeycar/parts/launch.py) overides the throttle when then driving mode transitions from manual driving to autopilot; it is used to provide a high throttle for a short time at the very start of a race.  In this case it does not have an explicit run_condition argument, so it defaults to True.  

```python
    aiLauncher = AiLaunch(cfg.AI_LAUNCH_DURATION, cfg.AI_LAUNCH_THROTTLE, cfg.AI_LAUNCH_KEEP_ENABLED)
    V.add(aiLauncher,
          inputs=['user/mode', 'pilot/throttle'],
          outputs=['pilot/throttle'])
```

To implement this 'launch' it needs to know the current driving mode and the current autopilot throttle value; those are its inputs.  If it is not launching then it just passes the throttle value through without modifying it, but when it is launching it outputs a throttle valud equal to cfg.AI_LAUNCH_THROTTLE. So the throttle is it's only output. The par'ts run() method must take in these two inputs in the correct order and return the one output.  You can see this in part's code;

```python
import time

class AiLaunch():
    '''
    This part will apply a large thrust on initial activation. This is to help
    in racing to start fast and then the ai will take over quickly when it's
    up to speed.
    '''

    def __init__(self, launch_duration=1.0, launch_throttle=1.0, keep_enabled=False):
        self.active = False
        self.enabled = False
        self.timer_start = None
        self.timer_duration = launch_duration
        self.launch_throttle = launch_throttle
        self.prev_mode = None
        self.trigger_on_switch = keep_enabled
        
    def enable_ai_launch(self):
        self.enabled = True
        print('AiLauncher is enabled.')

    def run(self, mode, ai_throttle):
        new_throttle = ai_throttle

        if mode != self.prev_mode:
            self.prev_mode = mode
            if mode == "local" and self.trigger_on_switch:
                self.enabled = True

        if mode == "local" and self.enabled:
            if not self.active:
                self.active = True
                self.timer_start = time.time()
            else:
                duration = time.time() - self.timer_start
                if duration > self.timer_duration:
                    self.active = False
                    self.enabled = False
        else:
            self.active = False

        if self.active:
            print('AiLauncher is active!!!')
            new_throttle = self.launch_throttle

        return new_throttle
```

It is common for configuration values to be passed as arguments to a part's constructor, as they are in this example.  Also, if the part grabs some hardware resource, such as a camera or a serial port, it should also have a ```shutdown``` function that releases those resources properly when donkey is stopped.

So as we said, the part's run() method is called each time the vehicle loop is run; the input values are read from vehicle memory and passed as arguments to the run() method, which does it's work and then returns values that are then written to vehicle memory as outputs.  Since parts are run in the order they are added (for the most part) then you can see that you need to add a part that provides an output ahead of any part that needs that value as an input. 

## Threaded Parts

I say 'for the most part' because you can also specify that a part is to be run in it's own thread so it can at it's own rate.  A threaded part has a `run_threaded()` method rather than a `run()` method; the inputs are arguments and the return values are outputs just like the `run()` method.  Also similar to the run() method, the run_threaded() method is called once each time the vehicle loop runs.  Here is an example of adding a threaded part to the vehicle loop.  This part interfaces to a TF-Mini single bean lidar via a serial port; it reports a distance.  It takes no input arguments and outputs just the distance value.  Note that the argument `inputs=[]` is not really necessary; that is the default for inputs so it can be left off.

```python
    if cfg.HAVE_TFMINI:
        from donkeycar.parts.tfmini import TFMini
        lidar = TFMini(port=cfg.TFMINI_SERIAL_PORT)
        V.add(lidar, inputs=[], outputs=['lidar/dist'], threaded=True)
```

So if the run_threaded() part is called each time through the vehicle loop, just like the run() method, and it's inputs and outputs are organized just the like a non-threaded part, then what is the difference between a threaded part and a non-threaded part.  Once difference you can see above is that when you add a threaded part you pass `threaded=True`.  The other difference with a threaded part is that it must have a no-argument update() method.  When the threaded part is launched a thread is created and the part's update() method is registered as the method that is executed on the thread; the update() should run a loop doing it's work until the part is told to shutdown().  Here is a listing of the [TFMini part](https://github.com/autorope/donkeycar/blob/main/donkeycar/parts/tfmini.py) to show this; 

```python
class TFMini:
    """
    Class for TFMini and TFMini-Plus distance sensors.
    See wiring and installation instructions at https://github.com/TFmini/TFmini-RaspberryPi

    Returns distance in centimeters. 
    """

    def __init__(self, port="/dev/serial0", baudrate=115200, poll_delay=0.01, init_delay=0.1):
        self.ser = serial.Serial(port, baudrate)
        self.poll_delay = poll_delay

        self.dist = 0

        if not self.ser.is_open:
            self.ser.close() # in case it is still open, we do not want to open it twice
            self.ser.open()

        self.logger = logging.getLogger(__name__)

        self.logger.info("Init TFMini")
        time.sleep(init_delay)

    def update(self):
        while self.ser.is_open:
            self.poll()
            if self.poll_delay > 0:
                time.sleep(self.poll_delay)

    def poll(self):
        try:
            count = self.ser.in_waiting
            if count > 8:
                recv = self.ser.read(9)   
                self.ser.reset_input_buffer() 

                if recv[0] == 0x59 and recv[1] == 0x59:     
                    dist = recv[2] + recv[3] * 256
                    strength = recv[4] + recv[5] * 256

                    if strength > 0:
                        self.dist = dist

                    self.ser.reset_input_buffer()

        except Exception as e:
            self.logger.error(e)


    def run_threaded(self):
        return self.dist

    def run(self):
        self.poll()
        return self.dist

    def shutdown(self):
        self.ser.close()
```

> NOTE: The TFMini part manages a serial port itself; it is recommend to use the [SerialPort part](https://github.com/autorope/donkeycar/blob/main/donkeycar/parts/serial_port.py) to read line oriented data from a serial port instead of managing the port in your part. The SerialPort part can handle all the details of the serial port and outputing the resulting data; then your part only needs to take that data as input and use it.


In the TFMini part the update() method runs a loop as long as the serial port remains open.  The serial port is opened in the constructor and closed when the shutdown() method is called.  In a threaded part, the update() method is almost like an infinite loop, running over and over as much as python will give it time to run.  This is the section of code that can run much faster than the vehicle loop runs.  

The reason to use a threaded part is if your part needs to go faster than the vehicle loop or needs to respond to a device in closer to real time.  The loop in the `update()` method will run as fast as the python interpreter can let it, which will usually be much faster than the vehicle loop.  It's important to understand that the `update()` method is called by the part's thread BUT the `run_threaded()` method is called by the main vehicle loop thread.  This means that these two methods may interupt each other in the middle of what they are doing.  

You should use approprate thread-safe patterns, such as mutexes, to make sure that data updates and/or reads and other critical sections of code are safely isolated and atomic.  In some cases this requires a Lock to make sure resources are accessed safely from threads or that multiple lines of code are executated atomically.  It is worth remembering that assignment in Python is atomic (so there is one good thing about that Global Interpreter Lock, GIL).  So while this is NOT atomic;

```
x = 12.34
y = 34.56
angle = 1.34
```

because your code could be interrupted in between those assignments.  This IS atomic;

```
pose = (12.34, 34.56, 1.34)
```

So if you have aggregate internal state that may be mutated in a thread, then put it in a tuple and you can read and write it atomically without locks.
