# Windows

Windows provides a few different methods for setting up and installing Donkey Car. If you are unfamiliar with Python or concerned about the way that you install Donkey Car, please use option 1.

1. Miniconda
2. Native
3. Windows Subsystem for Linux (WSL) - Experimental

## Install Donkeycar on Windows (miniconda)

![donkey](/assets/logos/windows_logo.png)

### Prerequisites

* Install [miniconda Python 3.7 64 bit](https://conda.io/miniconda.html).

* Open the Anaconda prompt window via Start Menu > Anaconda3 (64bit) > Anaconda Powershell Prompt (miniconda3)

* type `git`. If the command is not found, then install [git 64 bit](https://git-scm.com/download/win)

* Change to a dir you would like to use as the head of your projects.

```bash
mkdir projects
cd projects
```

* Get the latest donkey from Github.

```bash
git clone https://github.com/autorope/donkeycar
cd donkeycar
git checkout master
```

### Create conda environment

* If this is not your first install, update Conda and remove old donkey

```bash
conda update -n base -c defaults conda
conda env remove -n donkey
```

* Create the Python anaconda environment

```bash
conda env create -f install\envs\windows.yml
conda activate donkey
```

> Note: After closing the Anaconda Prompt, when you open it again, you will need to 
> type ```conda activate donkey``` to re-enable the mappings to donkey specific 
> Python libraries

### Install TensorFlow

#### Option 1: Standard TensorFlow installation is compatible with most PCs.

```bash
# with the donkey conda environment active
# in the donkeycar directory
pip install --user tensorflow==2.2.0
```
#### Option 2: GPU accelerated TensorFlow is much faster, but it only works with Nvidia graphics cards.

```bash
# with the donkey conda environment active
# in the donkeycar directory
pip install tensorflow-gpu==2.2.0
conda install cudatoolkit=10.1 -c pytorch
```

> Note: `tensorflow-gpu` 2.2.0 requires `cudatoolkit` version 10.1. Future versions of tensorflow-gpu will require CUDA 11, which is available from the Pytorch channel (`conda install cudatoolkit=11.3 -c pytorch`) or from the Nvidia channel (`conda install cuda -c nvidia`). You can find out your CUDA version by running `nvcc --version` or `nvidia-smi`. See [TensorFlow GPU Support](https://www.tensorflow.org/install/gpu#windows_setup) for more advanced documentation about Nvidia drivers.

### Install donkey on your PC

Once TensorFlow is installed, install `donkey`.

```bash
# with the donkey conda environment active
# with tensorflow installed
# in the donkeycar directory
pip install -e .[pc]
```

> Note: if you are using ZSH (you'll know if you are), you won't be able to run `pip install -e .[pc]`. You'll need to escape the brackets and run `pip install -e .\[pc\]`.

### Create a car

Congratulations! You've installed the `donkey` command line utility and are now ready to create your first car.

* First time training an autopilot? Complete the [Donkey Simulator tutorial](/docs/guide/simulator.md) while you wait for your car parts to arrive in the mail.
* If you're more advanced and already completed the [build a Donkey tutorial](/docs/guide/build_hardware.md)? It's time to [install Software on your Donkey Car](/docs/guide/install_software/#step-2-install-software-on-donkeycar).
* For detailed guidance on the `createcar` command, see the [Create a Car tutorial](/docs/guide/create_application.md) and [`donkey`](/docs/guide/utility/donkey.md) utility manual.

---

## Install Donkeycar on Windows (native)

![donkey](/assets/logos/windows_logo.png)

* Install [Python 3.6 (or later)](https://www.python.org/downloads/)

* Install [Git Bash](https://gitforwindows.org/).  During install make sure you check Git to run 'from command line and also from 3rd-party software'.

* Open Command Prompt as Administrator via the Start Menu (cmd.exe | right-click | run as administrator)

* Change to a folder that that you would like to use for all your projects

```shell
mkdir projects
cd projects
```

* Get the latest donkey from Github.

```bash
git clone https://github.com/autorope/donkeycar
cd donkeycar
git checkout master
```

> NOTE:  The `dev` branch has the latest (unstable) version of donkeycar with experimental features.

* Install Donkeycar into Python

```
pip3 install -e .[pc]
```

* Recommended for GPU Users: Install Tensorflow GPU - only for NVIDIA Graphics cards

If you have an NVIDIA card, you should update to the lastest drivers and [install Cuda SDK](https://www.tensorflow.org/install/gpu#windows_setup). 

```bash
pip3 install tensorflow
```

* Create your local working dir:

```bash
donkey createcar --path \Users\<username>\projects\mycar --template complete
```

> **Templates**
>  There are a number of different templates to choose from in Donkey Car.
>  basic | complete
>  You can find all the templates in the [donkeycar/donkeycar/templates](https://github.com/autorope/donkeycar/tree/dev/donkeycar/templates) folder

---
### Next let's [install software on Donkeycar](/guide/install_software/#step-2-install-software-on-donkeycar)
---


## Install Donkeycar on Windows (WSL)

The Windows Subsystem for Linux (WSL) lets developers run a GNU/Linux environment -- including most command-line tools, utilities, and applications -- directly on Windows, unmodified, without the overhead of a traditional virtual machine or dualboot setup.

* Install [Windows Subsystem for Linux](https://docs.microsoft.com/en-us/windows/wsl/install-win10).
  1.  If using Windows 10 (this is not necessary for Windows 11), turn on Windows 10 "Windows Subsystem for Linux" Feature (Settings > Apps > Programs and Features > Turn Windows features on or off)
  2.  Download a Linux Distribution from the Microsoft Store (recommend [Ubuntu](https://www.microsoft.com/en-us/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab) Latest)
  3.  Open the Ubuntu App and configure.

* Open the Ubuntu App to get a prompt window via Start Menu | Ubuntu

* Install `pip` using `sudo apt install python3-pip`

* Install the following two libraries for the UI to work: `sudo apt-get install libmtdev1 xclip`

* Change to a directory that you would like to use as the head of all your projects.

```bash
mkdir projects
cd projects
```

* Get the latest donkey from Github.

```bash
git clone https://github.com/autorope/donkeycar
cd donkeycar
git checkout master
```

> NOTE:  The `dev` branch has the latest (unstable) version of donkeycar with experimental features.

* Install Donkeycar into Python

```
pip3 install -e .[pc]
```

Once you're done with this, close the Ubuntu terminal and then reopen it so the path changes take effect.

* Experimental Support - GPU Users: Install Tensorflow GPU - only for NVIDIA Graphics cards

If you have an NVIDIA card, you should update to the lastest drivers and [install Cuda SDK](https://www.tensorflow.org/install/gpu#windows_setup). 

```bash
pip3 install tensorflow
```

* Create your local working dir:

```bash
donkey createcar --path /path/to/projects/mycar --template complete
```

> **Templates**
>  There are a number of different templates to choose from in Donkey Car.
>  basic | complete
>  You can find all the templates in the [donkeycar/donkeycar/templates](https://github.com/autorope/donkeycar/tree/dev/donkeycar/templates) folder

---
### Next let's [install software on Donkeycar](/guide/install_software/#step-2-install-software-on-donkeycar)
