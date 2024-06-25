# Cameras

Donkeycar supports a large number of cameras via the `CAMERA_TYPE` configuration.  For most applications a wide field of vision is important, so your camera should use a 120 degree wide angle lens or better.  A 160 degree wide angle lense is recommended.

## Camera Setup

If you are using the default deep learning template or the computer vision template then you will need a camera.  By default __myconfig.py__ assumes a RaspberryPi camera.  You can change this by editing the `CAMERA_TYPE` value in the __myconfig.py__ file in your `~/mycar` folder.  

If you are using the gps path follow template then you do not need, and may not want, a camera.  In this case you can change the camera type to mock; `CAMERA_TYPE = "MOCK"`.

### Raspberry Pi:

If you are on a raspberry pi and using the recommended pi camera ("PICAM"), then no changes are needed to your __myconfg.py__.

This works with all Raspberry Pi cameras, including the original Raspberry Pi Camera Module based on the 5 megapixel OV5647 chipset and the Raspberry Pi Camera Module v2 based on the Sony IMX219 chip.  These cameras are easily obtainable and are offered in generic (clone) versions by many vendors.

> **Make sure your camera works**.  [Camera connection](https://www.raspberrypi.com/documentation/accessories/camera.html#connect-the-camera) issues are common, especially after [assembly of a new Donkeycar](https://docs.donkeycar.com/guide/build_hardware/#step-6-attach-camera), installation of a new camera or after a crash.  In any of those cases or if you otherwise encounter an camera error when using the Donkeycar software, you should make sure your camera is working properly before asking for help on the [Discord](https://discord.gg/PN6kFeA).  Raspberry Pi OS includes [Camera Software](https://www.raspberrypi.com/documentation/computers/camera_software.html) that will take a picture or stream video.  If you have a keyboard, mouse and monitor connected to the Raspberry Pi, then you can run the [`rpicam-hello`](https://www.raspberrypi.com/documentation/computers/camera_software.html#rpicam-hello) utility to show the camera's video stream.  If you are ssh'ing into your Raspberry Pi, then you can take an image and save it as a jpeg using the [`rpicam-jpeg`](https://www.raspberrypi.com/documentation/computers/camera_software.html#rpicam-jpeg) utility, then copy the resulting jpeg file to you host computer to view it (generally if it successfully takes the photo without reporting an error then the camera should be ok).

### Jetson Nano:

The Jetson does not have a driver for the original 5 megapixels OV5647 based Raspberry Pi Camera, but it does have a driver for the v2 camera based on the IMX219 chip.  Indeed the recommended camera is based on the IMX219 chip.

The default setting `CAMERA_TYPE = "PICAM"` does **not** work on the Jetson, even if you are using an 8mp RaspberryPi camera or a camera based on a Sony IMX219 based camera  In either of these cases you will want edit your __myconfg.py__ to have: `CAMERA_TYPE = "CSIC"`.

For flipping the image vertically set `CSIC_CAM_GSTREAMER_FLIP_PARM = 6` - this is helpful if you have to mount the camera in a rotated position.

### USB Cameras

`CAMERA_TYPE = CVCAM` is a camera type that has worked for USB cameras when OpenCV is setup. This requires additional setup for [OpenCV for Nano](/guide/robot_sbc/setup_jetson_nano/#step-4-install-opencv) or [OpenCV for Raspberry Pi](https://www.learnopencv.com/install-opencv-4-on-raspberry-pi/).  

If you have installed the optional pygame library then you can connect to the camera by editing the camera type to `CAMERA_TYPE = "WEBCAM"`.  See the required additional setup for [pygame](https://www.pygame.org/wiki/GettingStarted).

If you have more than one camera then you made need to the `CAMERA_INDEX` configuration value.  By default it is zero.

>> NOTE: `CAMERA_TYPE = CVCAM` depends upon a version of OpenCV that has GStreamer support compiled in.  This is the default on the Jetson computers and is supported in the recommended version of OpenCV for that Raspberry Pi.

### Intel Realsense D435

The Intel Realsense cameras are RGBD cameras; they provide RGB images and Depth.  You can use them as an RGB camera to provide images for the Deep Learning template or the Computer Vision template by setting `CAMERA_TYPE = "D435"` in your __myconfig.py__ settings.  You will also want to review the settings that are specific to the Intel Realsense cameras;

```
# Intel Realsense D435 and D435i depth sensing camera
REALSENSE_D435_RGB = True       # True to capture RGB image
REALSENSE_D435_DEPTH = True     # True to capture depth as image array
REALSENSE_D435_IMU = False      # True to capture IMU data (D435i only)
REALSENSE_D435_ID = None        # serial number of camera or None if you only have one camera (it will autodetect)
```

If you are not using depth then you will want to set `REALSENSE_D435_DEPTH = False` so it does not save the depth data.

## Troubleshooting

If the colors look wrong it may be that the camera is outputting BGR colors rather than RGB.  You can set `BGR2RGB = True` to convert from BGR to RGB.

We are adding other cameras over time, so read the camera section in __myconfig.py__ to see what options are available.

If you are having troubles with your camera, check out our [Discord hardware channel](https://discord.gg/zcyzK69S) for more help.
