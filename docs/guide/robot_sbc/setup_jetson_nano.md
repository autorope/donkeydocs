# Get Your Jetson Nano Working

![donkey](/assets/logos/nvidia_logo.png)

* [Step 1: Flash Operating System](#step-1-flash-operating-system)
* [Step 2: Install Dependencies](#step-2-install-dependencies)
* [Step 3: Setup Virtual Env](#step-3-setup-virtual-env)
* [Step 4: Install Donkeycar Python Code](#step-4-install-donkeycar-python-code)
* Then [Create your Donkeycar Application](/guide/create_application/)

## Step 1: Flash Operating System

Visit the official [Nvidia Jetson Nano Getting Started Guide](https://developer.nvidia.com/embedded/learn/get-started-jetson-nano-devkit#prepare). Work through the __Prepare for Setup__, __Writing Image to the microSD Card__, and __Setup and First Boot__ instructions, then return here.

Once you're done with the setup, ssh into your vehicle. Use the the terminal for Ubuntu or Mac. [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) for windows.

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
##  Step 4: Setup Virtual Environment

```bash
pip3 install virtualenv
python3 -m virtualenv -p python3 env --system-site-packages
echo "source env/bin/activate" >> ~/.bashrc
source ~/.bashrc
```

##  Step 5: Setup Python Dependencies

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

Finally, you can install PyTorch:
```bash
# Install PyTorch v1.7 - torchvision v0.8.1
wget https://nvidia.box.com/shared/static/wa34qwrwtk9njtyarwt5nvo6imenfy26.whl -O torch-1.7.0-cp36-cp36m-linux_aarch64.whl
pip3 install ./torch-1.7.0-cp36-cp36m-linux_aarch64.whl

# Install PyTorch Vision
sudo apt-get install libjpeg-dev zlib1g-dev libpython3-dev libavcodec-dev libavformat-dev libswscale-dev

# You can replace the following line with wherever you want to store your Git repositories
mkdir -p ~/projects; cd ~/projects
wget https://nvidia.box.com/shared/static/p57jwntv436lfrd78inwl7iml6p13fzh.whl
cp p57jwntv436lfrd78inwl7iml6p13fzh.whl torch-1.8.0-cp36-cp36m-linux_aarch64.whl
pip3 install torch-1.8.0-cp36-cp36m-linux_aarch64.whl
```

Note: If you get errors in the above step, there may be a version mismatch with the version of Nvidia's Jetpack that you're running. If so, please install PyTorch directly from Nvidia's site [here](https://forums.developer.nvidia.com/t/pytorch-for-jetson-version-1-8-0-now-available/72048).

Optionally, you can install RPi.GPIO clone for Jetson Nano from [here](https://github.com/NVIDIA/jetson-gpio). This is not required for default setup, but can be useful if using LED or other GPIO driven devices.



##  Step 5: Install Donkeycar Python Code

* Change to a dir you would like to use as the head of your projects. Assuming you've already made the `projects` directory above, you can use that:

```bash
cd ~/projects
```

* Get the latest donkeycar from Github.

```bash
git clone https://github.com/autorope/donkeycar
cd donkeycar
git checkout master
pip3 install -e .[nano]
```

##  Step 6: Install PyGame (Optional)

If you plan to use a USB camera, you will also want to setup pygame:

```bash
sudo apt-get install python-dev libsdl1.2-dev libsdl-image1.2-dev libsdl-mixer1.2-dev libsdl-ttf2.0-dev libsdl1.2-dev libsmpeg-dev python-numpy subversion libportmidi-dev ffmpeg libswscale-dev libavformat-dev libavcodec-dev libfreetype6-dev

pip install pygame

```

Later on you can add the `CAMERA_TYPE="WEBCAM"` in myconfig.py.

----

### Next, [create your Donkeycar application](/guide/create_application/).
