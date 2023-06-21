# Get Your Jetson Nano/Xavier NX Working

![donkey](/assets/logos/nvidia_logo.png)

We have a different approaches to installing the software depending on the
version of Donkey Car. For Donkey Car <= 4.5.X we are using Jetpack 4.5.X
which comes with Tensorflow 2.3.1. The python installation is using virtual
env, i.e. it is based on the system python with version 3.6. This is the 
only version that is working on the old Jetson Nano.

For the `main` branch we have updated Tensorflow to 2.9 and python to 3.8 or
3.9. This is running on a newer version of Jetpack. You will need a Jetson 
Xavier or any of the newer Jetsons like the Orin, to use Jetpack 5.0.2. To 
decouple the python installation from the system python we are using Miniforge 
which is a mamba based version of Miniconda that works on the aarm architecture.

For Donkey Car <= 4.5.X please go to the next section. For the latest
version on the `main` branch please jump
to [this section](#installation-for-donkey-car-main).

We recommend to use 4GB version of the Jetson Nano or the Jetson Xavier to
run the software without issues. It's also recommended using a 128GB
microSD card with U3 speed, like for example
[this SanDisk SD Card.](https://www.amazon.com/SanDisk-128GB-Extreme-microSD-Adapter/dp/B07FCMKK5X/ref=sr_1_4?crid=1J19V1ZZ4EVQ5&keywords=SanDisk+128GB+Extreme+microSDXC+UHS-I&qid=1676908353&sprefix=sandisk+128gb+extreme+microsdxc+uhs-i%2Caps%2C121&sr=8-4)


These are the supported versions:

| Jetson      | Jetpack | Python | Donkey   | Tensorflow |
|-------------|---------|--------|----------|------------|
| Nano        | 4.5.2   | 3.6    | <= 4.5.X | 2.3.1      |    
| Xavier/Orin | 4.6.2   | 3.8    | >= 5.X   | 2.9        | 


Then [Create your Donkeycar Application](/guide/create_application/)


## Installation for Donkey Car <= 4.5.X

> Note: These instructions are working for DC 4.3.6 and DC 4.4.0 only at the 
> moment. We are working on a patch to have support for 4.5.X too. 


* [Step 1a: Flash Operating System](#step-1a-flash-operating-system)
* [Step 2a: Free up the serial port](#step-2a-free-up-the-serial-port-optional-only-needed-if-youre-using-the-robohat-mm1)
* [Step 3a: Install Dependencies](#step-3a-install-system-wide-dependencies)
* [Step 4a: Setup Python Environment](#step-4a-setup-python-environment)
* [Step 5a: (Optional) Install PyGame for USB camera](#step-5a-optional-install-pygame-for-usb-camera)


### Step 1a: Flash Operating System


These instructions work for Jetpack 4.5.1. 

* If you have a 4gb Jetson Nano then download Jetpack 4.5.1 from Nvidia
  here; [jetson-nano-jp451-sd-card-image.zip](https://developer.nvidia.com/embedded/l4t/r32_release_v5.1/r32_release_v5.1/jeston_nano/jetson-nano-jp451-sd-card-image.zip)
* If you have a 2gb Jetson Nano the download Jetpack 4.5.1from Nvidia
  here; [jetson-nano-2gb-jp451-sd-card-image.zip](https://developer.nvidia.com/embedded/l4t/r32_release_v5.1/r32_release_v5.1/jeston_nano_2gb/jetson-nano-2gb-jp451-sd-card-image.zip)

This installs the official Nvidia build of Tensorflow 2.3.1; make sure you are
using the same version of Tensorflow on your host PC if you are using one. Using
a different version of Tensorflow to train your network may result in errors
when you attempt to use it as an autopilot.

Visit the official [Nvidia Jetson Nano Getting Started Guide](https://developer.nvidia.com/embedded/learn/get-started-jetson-nano-devkit#prepare)
or [Nvidia Xavier NX Getting Started Guide](https://developer.nvidia.com/embedded/learn/get-started-jetson-xavier-nx-devkit).
Work through the __Prepare for Setup__, __Writing Image to the microSD Card__,
and __Setup and First Boot__ instructions, then return here.

Once you're done with the setup, ssh into your vehicle. Use the terminal for
Ubuntu or Mac. [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)
for windows.

Remove Libre Office:

```bash
sudo apt-get remove --purge libreoffice*
sudo apt-get clean
sudo apt-get autoremove
```

And add a 8GB swap file:

```bash
git clone https://github.com/JetsonHacksNano/installSwapfile
cd installSwapfile
./installSwapfile.sh
sudo reboot now 
```

### Step 2a: Free up the serial port (optional. Only needed if you're using the Robohat MM1)

```bash
sudo usermod -aG dialout <your username>
sudo systemctl disable nvgetty
```

### Step 3a: Install System-Wide Dependencies

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

### Step 4a: Setup Python Environment.

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

#### Install Donkeycar Python Code

Change to a dir you would like to use as the head of your projects. Assuming
you've already made the `projects` directory above, you can use that. Get
the latest 4.5.X release and install that into the venv.

```bash
mkdir projects
cd ~/projects
git clone https://github.com/autorope/donkeycar
cd donkeycar
git fetch --all --tags -f
latestTag=$(git describe --tags `git rev-list --tags --max-count=1`)
git checkout $latestTag
pip install -e .[nano45]

```

### Step 5a: (Optional) Install PyGame for USB camera

If you plan to use a USB camera, you will also want to setup pygame:

```bash
sudo apt-get install python-dev libsdl1.2-dev libsdl-image1.2-dev libsdl-mixer1.2-dev libsdl-ttf2.0-dev libsdl1.2-dev libsmpeg-dev python-numpy subversion libportmidi-dev ffmpeg libswscale-dev libavformat-dev libavcodec-dev libfreetype6-dev
pip install pygame
```

Later on you can add the `CAMERA_TYPE="WEBCAM"` in myconfig.py.



## Installation for Donkey Car main

Instructions for the latest code from the `main` branch. Note the 
installation differs between the two available OSs. On Jetson you need to 
install Jetpack 5.0.2.


### Installation on Jetson Xavier (or newer Jetson boards)

* [Step 1c: Flash Operating System](#step-1c-flash-operating-system)
* [Step 2c: Free up the serial port](#step-2c-free-up-the-serial-port-optional-only-needed-if-youre-using-the-robohat-mm1)
* [Step 3c: Setup Python Environment](#step-3c-setup-python-environment)
* [Step 4c: (Optional) Install PyGame for USB camera](#step-4c-optional-install-pygame-for-usb-camera)


#### Step 1c: Flash Operating System

These instructions work for Jetpack 5.0.2.

Please install the Jetpack image from [jetson-nx-developer-kit-sd-card-image.zip](https://developer.nvidia.com/jetson-nx-developer-kit-sd-card-image).


Visit the official [Nvidia Xavier NX Getting Started Guide](https://developer.nvidia.com/embedded/learn/get-started-jetson-xavier-nx-devkit).
Work through the __Prepare for Setup__, __Writing Image to the microSD Card__,
and __Setup and First Boot__ instructions, then return here.

Once you're done with the setup, ssh into your vehicle. Use the terminal for
Ubuntu or Mac. [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)
for windows.

Remove Libre Office:

```bash
sudo apt-get remove --purge libreoffice*
sudo apt-get clean
sudo apt-get autoremove
```

And add a 8GB swap file. Note, if you intend to run from an SSD, perform the 
swap file setup only after booting from the SSD:

```bash
git clone https://github.com/JetsonHacksNano/installSwapfile
cd installSwapfile
./installSwapfile.sh -s 8
reboot 
```

#### Step 2c: Free up the serial port (optional. Only needed if you're using the Robohat MM1)

```bash
sudo usermod -aG dialout <your username>
sudo systemctl disable nvgetty
```

#### Step 3c: Setup python environment

* Step 1: Install mamba-forge

Download and install Miniconda and install `mamba`.

```bash
wget https://repo.anaconda.com/miniconda/Miniconda3-py38_23.1.0-1-Linux-aarch64.sh
chmod u+x ./Miniconda3-py38_23.1.0-1-Linux-aarch64.sh
bash ./Miniconda3-py38_23.1.0-1-Linux-aarch64.sh
conda install mamba -n base -c conda-forge
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
pip install -U albumentations --no-binary qudida,albumentations
sudo chmod 666 /dev/gpiochip*

```

* Step 3: Check the TF and OpenCV installation

Run python and verify that tensorflow is version 2.9 and trt is version 8.2.1.
To get the tensorrt shared libraries to load correctly we must set the
environment variable `LD_PRELOAD` as:

```bash
export LD_PRELOAD=/usr/lib/aarch64-linux-gnu/libnvinfer.so.8:/usr/lib/aarch64-linux-gnu/libgomp.so.1
```

Note, this has to be done either every time you run donkeycar or tensorflow, or
you put the above line into your `.bashrc`.

```bash
python
>>> import tensorflow as tf
>>> tf.__version__
>>> from tensorflow.python.compiler.tensorrt import trt_convert as trt
>>> trt._check_trt_version_compatibility()
>>> import cv2
>>> print(cv2.getBuildInformation())
```

#### Step 4c: (Optional) Install PyGame for USB camera

If you plan to use a USB camera, you will also want to setup pygame:

```bash
pip install pygame
```
Later on you can add the `CAMERA_TYPE="WEBCAM"` in myconfig.py.


## (Optional) Fix for pink tint on CSIC cameras

This applies to any installation you did above, either JP 4.6.X or 5.0.X.
If you're using a CSIC camera you may have a pink tint on the images. As
described [here](https://jonathantse.medium.com/fix-pink-tint-on-jetson-nano-wide-angle-camera-a8ce5fbd797f),
this fix will remove it.

```bash
wget https://www.dropbox.com/s/u80hr1o8n9hqeaj/camera_overrides.isp
sudo cp camera_overrides.isp /var/nvidia/nvcam/settings/
sudo chmod 664 /var/nvidia/nvcam/settings/camera_overrides.isp
sudo chown root:root /var/nvidia/nvcam/settings/camera_overrides.isp
```


----

## Next, [create your Donkeycar application](/guide/create_application).
