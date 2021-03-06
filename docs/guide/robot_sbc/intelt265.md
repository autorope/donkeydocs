# A Guide to using the Intel Realsense T265 sensor with Donkeycar

----

* **Note** Although the Realsense T265 can be used with a Nvidia Jetson Nano, it's a bit easier to set up with a Raspberry Pi (we recommend the RPi 4, with at least 4GB memory). Also, the Intel Realsense D4XX series can also be used with Donkeycar as a regular camera (with the use of its depth sensing data coming soon), and we'll add instructions for that when it's ready.

## Original T265 path follower code by [Tawn Kramer](https://github.com/tawnkramer/donkey)

* Step 1: Setup Donkeycar

* Step 2: Setup Librealsense on Ubuntu Machine.
Using the latest version of Raspian (tested with Raspian Buster) on the RPi, follow [these instructions](https://github.com/IntelRealSense/librealsense/blob/master/doc/installation_raspbian.md) to set up Intel's Realsense libraries (Librealsense) and dependencies. 

* Step 3: Setup TensorRT on your Jetson Nano

After you’ve done that, set up the directory with this:

```bash
donkey createcar --path ~/follow --template path_follower
```

Running
```bash 
cd ~/follow 
python3 manage.py drive
```

Once it’s running, open a browser on your laptop and enter this in the URL bar: `http://<your nano’s IP address>:8887`

The rest of the instructions from Tawn’s repo:

When you drive, this will draw a red line for the path, a green circle for the robot location.
Mark a nice starting spot for your robot. Be sure to put it right back there each time you start.
Drive the car in some kind of loop. You see the red line show the path.
Hit X on the PS3/4 controller to save the path.
Put the bot back at the start spot.
Then hit the “select” button (on a PS3 controller) or “share” (on a PS4 controller) twice to go to pilot mode. This will start driving on the path. If you want it go faster or slower, change this line in the myconfig.py file: `THROTTLE_FORWARD_PWM = 530`
Check the bottom of myconfig.py for some settings to tweak. PID values, map offsets and scale. things like that. You might want to start by downloading and using the myconfig.py file from my repo, which has some known-good settings and is otherwise a good place to start.
Some tips:

When you start, the green dot will be in the top left corner of the box. You may prefer to have it in the center. If so, change `PATH_OFFSET = (0, 0)` in the myconfig.py file to `PATH_OFFSET = (250, 250)`

For a small course, you may find that the path is too small to see well. In that case, change `PATH_SCALE = 5.0` to `PATH_SCALE = 10.0` (or more, if necessary)

If you’re not seeing the red line, that means that a path file has already been written. Delete “donkey_path.pkl” (rm donkey_path.pkl) and the red line should show up

It defaults to recording a path point every 0.3 meters. If you want it to be smoother, you can change to a smaller number in myconfig.py with this line: `PATH_MIN_DIST = 0.3`
