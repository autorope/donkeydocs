# Get Your Jetson Nano/Xavier NX Working

![donkey](/assets/logos/nvidia_logo.png)

* [Step 1: Flash Operating System](#step-1-flash-operating-system)
* [Step 2: Free up the serial port](#step-2-free-up-the-serial-port-optional-only-needed-if-youre-using-the-robohat-mm1)
* [Step 3: Install Dependencies](#step-3-install-system-wide-dependencies)
* [Step 4: Setup Python Environment](#step-4-setup-python-environment)
* [Step 4: Install Donkeycar Python Code](#step-4-install-donkeycar-python-code)
* [Step 5: (Optional) Install RPi.GPIO](#step-5-optional-install-rpigpio)
* [Step 6: (Optional) Fix for pink tint on CSIC cameras](#step-6-optional-fix-for-pink-tint-on-csic-cameras)
* [Step 7: (Optional) Install PyGame for USB camera](#step-7-optional-install-pygame-for-usb-camera)
* Then [Create your Donkeycar Application](/guide/create_application/)




## Step 1: Flash Operating System

These instructions work for Jetpack 4.5.1. They are known to *NOT* work on
Jetpack 4.6 or 4.6.1.

* If you have a 4gb Jetson Nano then download Jetpack 4.5.1 from Nvidia
  here; [jetson-nano-jp451-sd-card-image.zip](https://developer.nvidia.com/embedded/l4t/r32_release_v5.1/r32_release_v5.1/jeston_nano/jetson-nano-jp451-sd-card-image.zip)
* If you have a 2gb Jetson Nano the download Jetpack 4.5.1from Nvidia
  here; [jetson-nano-2gb-jp451-sd-card-image.zip](https://developer.nvidia.com/embedded/l4t/r32_release_v5.1/r32_release_v5.1/jeston_nano_2gb/jetson-nano-2gb-jp451-sd-card-image.zip)

This installs the official Nvidia build of Tensorflow 2.3.1; make sure you are
using the same version of Tensorflow on your host PC if you are using one. Using
a different version of Tensorflow to train your network may result in errors
when you attempt to use it as an autopilot.

Visit the
official [Nvidia Jetson Nano Getting Started Guide](https://developer.nvidia.com/embedded/learn/get-started-jetson-nano-devkit#prepare)
or [Nvidia Xavier NX Getting Started Guide](https://developer.nvidia.com/embedded/learn/get-started-jetson-xavier-nx-devkit).
Work through the __Prepare for Setup__, __Writing Image to the microSD Card__,
and __Setup and First Boot__ instructions, then return here.

Once you're done with the setup, ssh into your vehicle. Use the the terminal for
Ubuntu or
Mac. [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) for
windows.

## Step 2: Free up the serial port (optional. Only needed if you're using the Robohat MM1)

```
sudo usermod -aG dialout <your username>
sudo systemctl disable nvgetty
```

## Step 3: Install System-Wide Dependencies

First install some packages with `apt-get`.

```bash
sudo apt-get update -y
sudo apt-get upgrade -y
sudo apt-get install -y libhdf5-serial-dev hdf5-tools libhdf5-dev zlib1g-dev zip libjpeg8-dev liblapack-dev libblas-dev gfortran
sudo apt-get install -y python3-dev python3-pip
sudo apt-get install -y libxslt1-dev libxml2-dev libffi-dev libcurl4-openssl-dev libssl-dev libpng-dev libopenblas-dev
sudo apt-get install -y git nano
sudo apt-get install -y openmpi-doc openmpi-bin libopenmpi-dev libopenblas-dev
```

## Step 4: Setup Python Environment.

We have now a different approaches to the python environment setup. For 
Donkey Car <= 4.4.X we are using a virtual environment. For the latest 
Donkey Car from the `main` branch, we are using mini-forge, an equivalent of 
miniconda for aarm architectures. 


### Setup the Python Environment for Donkey Car <= 4.4:

#### Setup Virtual Environment

```bash
pip3 install virtualenv
python3 -m virtualenv -p python3 env --system-site-packages
echo "source ~/env/bin/activate" >> ~/.bashrc
source ~/.bashrc
```

#### Setup Python Dependencies

Next, you will need to install packages with `pip`:

```bash
pip3 install -U pip testresources setuptools
pip3 install -U futures==3.1.1 protobuf==3.12.2 pybind11==2.5.0
pip3 install -U cython==0.29.21 pyserial
pip3 install -U future==0.18.2 mock==4.0.2 h5py==2.10.0 keras_preprocessing==1.1.2 keras_applications==1.0.8 gast==0.3.3
pip3 install -U absl-py==0.9.0 py-cpuinfo==7.0.0 psutil==5.7.2 portpicker==1.3.1 six requests==2.24.0 astor==0.8.1 termcolor==1.1.0 wrapt==1.12.1 google-pasta==0.2.0
pip3 install -U gdown

# This will install tensorflow as a system package
pip3 install --pre --extra-index-url https://developer.download.nvidia.com/compute/redist/jp/v45 tensorflow==2.3.1
```

#### Pytorch
 
Finally, you can install PyTorch:

```bash
wget https://nvidia.box.com/shared/static/p57jwntv436lfrd78inwl7iml6p13fzh.whl
cp p57jwntv436lfrd78inwl7iml6p13fzh.whl torch-1.8.0-cp36-cp36m-linux_aarch64.whl
pip3 install torch-1.8.0-cp36-cp36m-linux_aarch64.whl
sudo apt-get install libjpeg-dev zlib1g-dev libpython3-dev libavcodec-dev libavformat-dev libswscale-dev
mkdir -p ~/projects; cd ~/projects
git clone -b v0.9.0 https://github.com/pytorch/vision torchvision
cd torchvision 
python setup.py install
cd ../
```

#### Install Donkeycar Python Code

Change to a dir you would like to use as the head of your projects. Assuming
you've already made the `projects` directory above, you can use that. Get 
the lates 4.4.X release and install that into the venv.

```bash
cd ~/projects
git clone https://github.com/autorope/donkeycar
cd donkeycar
git fetch --all --tags -f
latestTag=$(git describe --tags `git rev-list --tags --max-count=1`)
git checkout $latestTag
pip install -e .[nano]

```


### Setup the Python Environment for Donkey Car main

The installation on JP 4.6.X is more tricky, because we need to use a newer 
version of tensorflow than what is packaged by NVidia for this OS version. 
Also, we need a compatible OpenCV version which needs to be built on the nano.


#### Installation on  JP 4.6.X

* Step 1: Install mamba-forge

Download and install mambaforge

```bash
wget https://github.com/conda-forge/miniforge/releases/latest/download/Mambaforge-Linux-aarch64.sh
chmod u+x Mambaforge-Linux-aarch64.sh
sudo Mambaforge-Linux-aarch64.sh
```

* Step 2: Install git lfs

```bash
sudo apt-get install git-lfs
```

* Step 3: Download Donkey Car

Downloading Donkey Car and the Tensorflow wheel for jp46 and tf29

```bash
mkdir projects
cd projects
git clone https://github.com/autorope/donkeycar
git clone https://github.com/autorope/jetson
cd donkeycar
git checkout main
```

* Step 4: Create the python env and install donkey

Also install tensorflow in the last step

```bash
cd donkeycar
mamba env create -f install/envs/jetson46.yml
conda activate donkey
pip install -e .[nano]
pip install ../jetson/tensorflow-2.9.3-cp39-cp39-linux_aarch64.whl
```

* Step 5: Check the TF installation

Run python and verify that tensorflow is version 2.9 and trt is version 8.2.1:

```bash
python
>>> import tensorflow as tf
>>> tf.version
>>> from tensorflow.python.compiler.tensorrt import trt_convert as trtf
>>> trt._check_trt_version_compatibility()
```

* Step 6: Install OpenCV

OpenCV with GStreamer support is required for the Nano camera. However, the 
OpenCV binaries on PYPI don't have that enabled. Hence, we need to build these 
ourselves.

Install opencv 4.6 + Gstreamer following the Q-Engineering Opencv manual install
[tutorial](https://qengineering.eu/install-opencv-4.5-on-jetson-nano.html). 

Note, the `CMake` command should be executed with the following flags:

```bash
cmake -D CMAKE_BUILD_TYPE=RELEASE \
-D CMAKE_INSTALL_PREFIX=$(python3 -c \“import sys\”) \
-D CMAKE_PREFIX_PATH=$/mambaforge/envs/donkey/bin/python3.9 \
-D python=ON \
-D BUILD_opencv_python2=OFF \
-D BUILD_opencv_python3=ON \
-D CMAKE_INSTALL_PREFIX=/home/thomas/mambaforge \
-D OPENCV_EXTRA_MODULES_PATH=~/opencv_contrib/modules \
-D EIGEN_INCLUDE_PATH=/usr/include/eigen3 \
-D WITH_OPENCL=OFF \
-D WITH_CUDA=ON \
-D CUDA_ARCH_BIN=5.3 \
-D CUDA_ARCH_PTX="" \
-D WITH_CUDNN=ON \
-D WITH_CUBLAS=ON \
-D ENABLE_FAST_MATH=ON \
-D CUDA_FAST_MATH=ON \
-D OPENCV_DNN_CUDA=ON \
-D ENABLE_NEON=ON \
-D WITH_QT=OFF \
-D WITH_OPENMP=ON \
-D BUILD_TIFF=ON \
-D WITH_FFMPEG=ON \
-D WITH_GSTREAMER=ON \
-D WITH_TBB=ON \
-D BUILD_TBB=ON \
-D BUILD_TESTS=OFF \
-D WITH_EIGEN=ON \
-D WITH_V4L=ON \
-D WITH_LIBV4L=ON \
-D OPENCV_ENABLE_NONFREE=ON \
-D INSTALL_C_EXAMPLES=OFF \
-D INSTALL_PYTHON_EXAMPLES=OFF \
-D PYTHON3_PACKAGES_PATH=/home/thomas/mambaforge/envs/donkey/lib/python3.9/site-packages \
-D PYTHON3_LIBRARIES_PATH=/home/thomas/mambaforge/envs/donkey/lib \
-D OPENCV_GENERATE_PKGCONFIG=ON \
-D BUILD_EXAMPLES=OFF
```

* Step 7: Check that OpenCV has been installed properly

Run python and verify that opencv is version 4.6 and Gstreamer is version 1.19 by:

```bash
python
>>> import cv2
>>> print(cv2.getBuildInformation()) 
```

#### Installation on JP 5.0.X

* Step 1: Install mamba-forge

Download and install mambaforge

```bash
wget https://github.com/conda-forge/miniforge/releases/latest/download/Mambaforge-Linux-aarch64.sh
chmod u+x Mambaforge-Linux-aarch64.sh
sudo Mambaforge-Linux-aarch64.sh
```

* Step 2: Download and install Donkey Car

Downloading Donkey Car from GitHub

```bash
mkdir projects
cd projects
git clone https://github.com/autorope/donkeycar
cd donkeycar
git checkout main
mamba env create -f install/envs/jetson.yml
conda activate donkey
pip install -e .[nano]

```

* Step 3: Check the TF installation

Run python and verify that tensorflow is version 2.9 and trt is version 8.2.1. 
To get the tensorrt shared libraries to load correctly we must set the 
environment variable `LD_PRELOAD` as: 

```bash
export LD_PRELOAD=/usr/lib/aarch64-linux-gnu/libnvinfer.so.8
```

Note, this has to be either every time you run donkeycar or tensorflow, or 
you put the above line into your `.bashrc`. 


```bash
python
>>> import tensorflow as tf
>>> tf.version
>>> from tensorflow.python.compiler.tensorrt import trt_convert as trtf
>>> trt._check_trt_version_compatibility()
```


## Step 5: (Optional) Install RPi.GPIO

Optionally, you can install the RPi.GPIO clone for Jetson Nano
from [here](https://github.com/NVIDIA/jetson-gpio). This is not required for
default setup, but can be useful if using LED or other GPIO driven devices.


## Step 6: (Optional) Fix for pink tint on CSIC cameras

If you're using a CSIC camera you may have a pink tint on the images. As
described [here](https://jonathantse.medium.com/fix-pink-tint-on-jetson-nano-wide-angle-camera-a8ce5fbd797f),
this fix will remove it.

```bash
wget https://www.dropbox.com/s/u80hr1o8n9hqeaj/camera_overrides.isp
sudo cp camera_overrides.isp /var/nvidia/nvcam/settings/
sudo chmod 664 /var/nvidia/nvcam/settings/camera_overrides.isp
sudo chown root:root /var/nvidia/nvcam/settings/camera_overrides.isp
```

## Step 7: (Optional) Install PyGame for USB camera

If you plan to use a USB camera, you will also want to setup pygame:

```bash
sudo apt-get install python-dev libsdl1.2-dev libsdl-image1.2-dev libsdl-mixer1.2-dev libsdl-ttf2.0-dev libsdl1.2-dev libsmpeg-dev python-numpy subversion libportmidi-dev ffmpeg libswscale-dev libavformat-dev libavcodec-dev libfreetype6-dev
pip install pygame
```

Later on you can add the `CAMERA_TYPE="WEBCAM"` in myconfig.py.

----

### Next, [create your Donkeycar application](/guide/create_application).
