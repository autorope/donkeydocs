# Train an Autopilot

Donkey supports three kinds of autopilots; a deep-learning autopilot, a path follow autopilot and a computer vision autopilot.  

If you followed along with the [Create Donkeycar App](/guide/create_application) section, then you know that you choose which template to use when you create your **mycar** application folder using the **createcar** command.

This section will talk about what the templates are for, then we can get onto training an autopilot.

## Deep Learning Autopilot
The deep learning autopilot uses a single forward facing camera and a convolutional neural network to implement an autopilot using a technique known as Behavioral Cloning (also known as Imitation Learning).  The technique is called Behavioral Cloning because the goal is to create an autopilot that imitates that actions of a human.  This is the first kind of autopilot that Donkeycar supported and what it is best known for.  For driving a car the overall process looks like this;

- A **human drives** the car to **gather data**. As the you manually drive around the track Donkeycar records data 20 times per second.  Each piece of data has 3 components; a camera image, the throttle value and the steering values that were in place at the time the image was taken. We want about 10,000 of these.
- **Clean the data**.  We don't want driving mistakes in the data; things like driving off the track or crashing into an obstacle.  Alternatively, we can delete such data while we are driving so it never get's into the data set.
- Use the collected data to calculate (**train**) **a Convolutional Neural Network**.
- Use the trained CNN to **infer the throttle and steering values** given an image.  So when we are in autopilot mode, we take an image, give it to the CNN, get the throttle and steering and put those into the cars hardware.  We **do that 20 times per second** and now we are driving!

Because the deep learning autopilot depends on a camera image, lighting conditions are important.  The deep learning template is great for an indoor track where lighting conditions and the details of the room can be controlled, but it can be more difficult to get working outside where lighting conditions are variable and things change in the environment.  

**Aside:** The Donkeycar approach to deep learning driving was inspired by an Nvidia research paper entitled [End to End Learning for Self-Driving Cars](https://arxiv.org/pdf/1604.07316v1.pdf).


[Train a deep learning autopilot](./deep_learning/train_autopilot.md)


## Path Follow Autopilot
The path follow template is an alternative to the deep learning template.  Outside we have access to GPS; the path follow template allows you to record a path using a GPS receiver and then configure an autopilot that can follow that path.  The overall process looks like this;

- A **human drives** the car to **gather data**. The data is aquired from a GPS receiver and represents and (x,y) position in meters.  Each (x,y) position is called a waypoint.  The user will drive the course once to collect waypoints.  The complete set of waypoints is called a path.
- The autopilot gets the car's current (x,y) position from the GPS receiver, then finds the two closest points in the path and adjusts the car's steering to drive towards the path.  It does this 20 times per second and now we are driving!  

There is a lot more detail on this in the next section.

[Train a path follow autopilot](./path_follow/path_follow.md)

## Computer Vision Autopilot
The computer vision autopilot uses traditional computer vision techniques, such as color space conversion and edge detection algorithms, to identify features in the camera image and turn those into steering and throttle values.  This autopilot has an advantage over the other autopilots in that it does not require manual driving to gather data.  Instead you will choose or write a computer vision algorithm and modify the algorithm parameters to suit the track.  This autopilot is specifically designed to make it easy to write your own algorithm using the OpenCV library and the many Donkeycar-provided primitives.

- A **human** chooses the cv algorithm and modifies parameters until it delivers accurate and reliable steering and throttle values. 
- A **human** places the car on the track and changes from user to autopilot mode.
- When in autopilot mode, an image from the camera is passed to the cv algorithm which interprets it and outputs steering and throttle values.  It does this 20 times per second and now we are driving!

There is a lot more detail in the next section about the build-in algorithm and how to write your own algorithm.

[Computer Vision Autopilot](./computer_vision/computer_vision.md)
