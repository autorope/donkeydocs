
# Stop Sign Detection

This part utilize a Google Coral accelerator and a pre-trained object detection model by Coral project to perform stop sign detection. If the donkey car see a stop sign, it will override the ```pilot/throttle``` to 0. In addition, a bounding box will be annotated to the ```cam/image_array```.

<video style="width:50%" controls>
  <source src="../../assets/parts/stop_sign_detection/demo.mp4" type="video/mp4">
Your browser does not support the video tag.
</video>

---------------

## Requirement
To use this part, you must have:

- [Google Coral USB Accelerator](https://coral.ai/products/accelerator/)

## How to use
Put the following lines in ```myconfig.py```
```
STOP_SIGN_DETECTOR = True
STOP_SIGN_MIN_SCORE = 0.2
STOP_SIGN_SHOW_BOUNDING_BOX = True
```

### Install Edge TPU dependencies

Follow the Coral Edge TPU [get started](https://coral.ai/docs/accelerator/get-started) instructions to install the necessary software.  For the RaspberryPi follow the Linux instructions.

The stop sign detector uses a pre-compiled model, so we only need the inference runtime to make this work.  However, if you are creating your own model then you will need the [Edge TPU Compiler](https://coral.ai/docs/edgetpu/compiler/) on your RaspberryPi (or Linux laptop if you are training on that).  Note that the compiler only runs on Linux.

## Detecting other objects

Since the pre-trained model are trained on coco, there are 80 objects that the model is able to detect. You can simply change the ```STOP_SIGN_CLASS_ID``` in ```stop_sign_detector.py``` to try.

## Accuracy

Since SSD [is not good at detecting small objects](https://medium.com/@jonathan_hui/what-do-we-learn-from-single-shot-object-detectors-ssd-yolo-fpn-focal-loss-3888677c5f4d), the accuracy of detecting the stop sign from far away may not be good. There are some ways that we can make enhancement but this is out of the scope of this part.

### Getting this to work without the Coral Edge TPU
There is an [issue in the Github](https://github.com/autorope/donkeycar/issues/953) for making this work without the Coral Edge TPU.  If you get this working please submit a pull request.  


