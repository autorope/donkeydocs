# Object Detection: Stop Sign and Traffic Cone

This part will detect objects in the camera image and perform a sequence of events. The detection algorithm is based upon Google's Tensorflow Lite object detection.
Google's 'out of the box' model detects objects from the Coco set which includes traffic stop signs.  A custom model was created to detect 
traffic cones. For each object type, a protocol is defined that adjusts the throttle and steering angle to implement an event sequence:
1. Stop and Go protocol
2. Traffic Cone protocol in the module.  

*These parts are intended to run only in Pilot Mode.*

## Protocol: Stop at a Stop Sign, then proceed (class Stop\_And\_Go)
This part will override the pilot_throttle to stop the Donkeycar when it detects a stop sign.  After a pause, the
car will pass the stop sign and proceed around the track. The pause time is configurable.

<iframe class="video" width="560" height="315" src="https://youtu.be/0TLaEs126zs" allowfullscreen></iframe>
Stop Sign Detection with Autopilot in Action

## Protocol: Swerve around a Traffic Cone (class Pass_Object)
This part will override the pilot angle and pilot throttle to control the Donkeycar.
On detection of a traffic cone, the car will steer around the cone. The output steering angle is based upon
the difference between the pilot angle and the object detection angle. The output angle will be in the direction of the pilot angle,
if the difference is greater than the tolerance and less than the max angle. Otherwise, the output angle will be set to the
object angle - tolerance.

<iframe class="video" width="560" height="315" src="https://youtu.be/sjvswIy4TGE" allowfullscreen></iframe>
Traffic Cone avoidance with Autopilot in Action

## Inputs/Outputs
```
Inputs  = [pilot/angle, pilot/throttle, cam/image_array]  
Outputs = [pilot/angle, pilot/throttle, cam/image_array]
```

If no detection the inputs will be passed to the outputs. If an object is detected, the protocol will modify the angle
and/or throttle as determined by the protocol.  The image will be marked with a bounding box showing the object detected in the image.

## Architecture and Files
The class *Object_Detector* from the module *detector.py*  manages the detection of the objects. 
The detection algorithm uses one of four models.

- *efficientdet\_lite0.tfile*:  COCO objects that includes the Stop Sign
- *efficientdet\_lite0_edgetpu.tfile*: COCO objects that includes the Stop Sign; compiled to support the Coral Edge TPU coprocessor
- *conemodel.tflite*: model to recognize Traffic Cone objects 
- *conemodel\_edgetpu.tflite*: model to recognize Traffic Cone objects; compiled to support the Coral Edge TPU coprocessor

Reference:  [TensorFlow Lite Python object detection example with Raspberry Pi](https://github.com/tensorflow/examples/tree/master/lite/examples/object_detection/raspberry_pi)

The module *od\_protocol.py* contains Donkeycar part classes for the protocols. If run counter is true, it passes the image to the detector. If the detector finds an object, the bounding box is returned.  The bounding box is used to calculate the position angle from 0.0. Then the specific protocol manage function decides how to respond.

## Performance
When running in pilot mode, the Donkeycar is typically running the Tensorflow deep learning model to drive the car. The object detection is additional model.   Running both of these AI models at full speed
on a Raspberry Pi is too slow to maintain a reasonable vehicle hertz.  The protocol parts should be run in nonthreaded mode but only a few times a second. 
By default the object detection parts are configured to run 1 time for every n vehicle hz with the parameter OD\_RUN\_HZ. This will 
minimally impact the performance of the Keras part. However, once an object is detected (stop sign or traffic cone), 
the protocol part will run at full speed to complete the protocol sequence.
This compromise allows the detection protocol to maximize its accuracy without impacting overall performance. 

If you have the Coral Edge TPU, run the protocol parts in threaded mode. The parts will run a full speed.

## Limitations and Enhancements
Note that the author tested the part on a Raspberry Pi 4 2GB only.  *No testing was done with the Coral Edge TPU or Jetson Nano.*

The *Detection\_Protocol* class in *OD_Protocol.py* module is a base class that provides the
base functionality that can be extended to develop other protocols
to either perform different actions based upon the Stop Sign or Traffic Cone or to detect other COCO objects.

## Dependencies
The detection algorithm requires python module tflite\_support. 
```
pip install tflite_support
```
Download a Tensorflow Lite model as needed into the directory donkeycar/donkeycar/parts/object\_detector

>#### Download Google Coco model to recognize Stop Signs
>[efficientdet\_lite0.tflite](https://drive.google.com/file/d/1exLAjDTkZOcQsrItflHRaf49eHSm5YWf/view?usp=sharing)

>[efficientdet\_lite0\_edgetpu.tflite](https://drive.google.com/file/d/1G2NgKx-MlEyArtS6RsXQGkV9dWm3dKHM/view?usp=sharing)

>#### Download a custom model to recognize traffic cones
>[conemodel.tflite](https://drive.google.com/file/d/1ETdqEfbe3f90wI5FPYh7jh5_67DrDJgF/view?usp=sharing)

>[conemodel\_edgetpu.tflite](https://drive.google.com/file/d/1yiW63i2mVKnWs1suh5uPjEp0sONW8_05/view?usp=sharing)


## Configuration
The following are the default settings from config.py.  Your myconfig.py must include the setting to enable one of the protocols.
OD\_STOPANDGO = True or OD\_PASS\_CONE=True

```
OD_USE_EDGETPU = False          # If True, Coral Edge TPU connected. Use the edgeTPU model 
OD_SCORE = 0.5                  # Set the score threshold for detection.
OD_RUN_HZ = 1                   # Run detection algorithm n times per drive_loop_hz ex. 1 time every 20 drive loop 

Object Detector - Stop and Go protocol
OD_STOPANDGO = False            # Set true to enable the protocol;  stop and pause, then proceed past the sign before looking again
OD_PAUSE_TIME = 2.0             # after stop sequence completes, pause for n seconds

Object Detector - Pass a Cone protocol
OD_PASS_CONE = False            # Set true to enable the protocol; detection to swerve around a cone in roadway
OD_SPEEDUP_MULTIPLIER = 1.0     # scale throttle of the car as it passes the object (cone)
OD_MAX_ANGLE = 0.75             # maximum and minimum drive angle range
OD_TOLERANCE = 0.45             # drive angle is set to be larger than TOLERANCE + CONE ANGLE or smaller than TOL - CONE angle
```



