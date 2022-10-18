# Fastai(PyTorch) Parts

These parts encapsulate models defined using the [FastAi](https://docs.fast.ai/) high level api. They are intended to be used with the PyTorch backend. This allows you to build models using PyTorch or transfer learning.

 > _**Note**_ This part is interchangeable with the Keras part but does not have TensorRT or TfLite support.
 

The parts are designed to use the trained artificial neural network to reproduce the steering and throttle given the image the camera sees. They are created by using the [train command](/guide/deep_learning/train_autopilot/).

## FastAi Linear

This model type is created with the `--type=fastai_linear`. 

The `FastAILinear` pilot uses one neuron to output a continuous value via the 
PyTorch Dense layer with linear activation. One each for steering and throttle.
The output is not bounded.

#### Pros

* Steers smoothly. 
* It has been very robust.
* Performs well in a limited compute environment like the Pi3.
* No arbitrary limits to steering or throttle.

#### Cons

* May sometimes fail to learn throttle well.

#### Model Summary

Input: Image

Network: 5 Convolution layers followed by two dense layers before output

Output: Two dense layers with one scalar output each with linear activation for steering and throttle.

