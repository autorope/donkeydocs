# Windows

Windows provides a few different methods for setting up and installing Donkey Car.  

1. Miniconda
2. Native
3. Windows Subsystem for Linux (WSL) - Recommended

## Install Donkeycar on Windows (miniconda)

![donkey](/assets/logos/windows_logo.png)

* Install [miniconda Python 3.9 64 bit](https://repo.anaconda.com/miniconda/Miniconda3-py39_23.3.1-0-Windows-x86_64.exe).

* Open the Anaconda prompt window via Start Menu | Anaconda 64bit | Anaconda Prompt

* type `git`. If the command is not found, then install [git 64 bit](https://git-scm.com/download/win)

* Change to a dir you would like to use as the head of your projects.

```bash
mkdir projects
cd projects
```

* Get the latest donkey from Github.

> Note: There are currently version changes happening on the `main` branch so you might rather want to check out a stable release as explained below.

```bash
git clone https://github.com/autorope/donkeycar
cd donkeycar
git checkout main
```

* Get a stable release from Github.

```bash
git clone https://github.com/autorope/donkeycar
cd donkeycar
git fetch --all --tags -f
git checkout tags/4.4.0
```

* If this is not your first install, update Conda and remove old donkey

```bash
conda update -n base -c defaults conda
conda env remove -n donkey
```

* Create the Python anaconda environment

```bash
conda env create -f install\envs\windows.yml
conda activate donkey
pip install --user tensorflow==2.9
pip install -e .[pc]
```
Note: if you are using ZSH (you'll know if you are), you won't be able to run `pip install -e .[pc]`. You'll need to escape the brackets and run `pip install -e .\[pc\]`.

* Optionally Install Tensorflow GPU - only for NVidia Graphics cards

If you have an NVidia card, you should update to the latest drivers and [install Cuda 11](https://developer.nvidia.com/cuda-11.0-download-archive). 

```bash
pip install tensorflow-gpu==2.9
```

* Optionally configure PyTorch to use GPU - only for NVidia Graphics cards

If you have an NVidia card, you should update to the lastest drivers and [install Cuda SDK](https://www.tensorflow.org/install/gpu#windows_setup). 

```bash
conda install cudatoolkit=<CUDA Version> -c pytorch
```

You should replace `<CUDA Version>` with your CUDA version. Any version above 10.0 should work. You can find out your CUDA version by running `nvcc --version` or `nvidia-smi`.

* Create your local working dir:

```bash
donkey createcar --path ~/mycar
```

> Note: After closing the Anaconda Prompt, when you open it again, you will need to 
> type ```conda activate donkey``` to re-enable the mappings to donkey specific 
> Python libraries

----
### Next let's [install software on Donkeycar](/guide/install_software/#step-2-install-software-on-donkeycar)

---

## Install Donkeycar on Windows (native)

![donkey](/assets/logos/windows_logo.png)

* Install [Python 3.9](https://www.python.org/downloads/)

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
git checkout main
```

> NOTE:  The `main` branch has the latest (unstable) version of donkeycar with experimental features.

* Get a stable release from Github:

```bash
git clone https://github.com/autorope/donkeycar
cd donkeycar
git fetch --all --tags
git checkout tags/4.3.6.1
```

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

* Refresh list of packages and install pip and xclip:

```bash
sudo apt-get update
sudo apt install python3-pip
sudo apt-get install libmtdev1 xclip
```

At this point switch to the [Ubuntu instructions](http://docs.donkeycar.com/guide/host_pc/setup_ubuntu/) and continue the setup there.

* Possible problems when running the UI

If you use the Donkey UI and see the following error:
```bash
[ERROR  ] [Input       ] MTDev is not supported by your version of linux
Traceback (most recent call last):
  File "/home/you/miniconda3/envs/donkey/lib/python3.7/site-packages/kivy/input/providers/init.py", line 41, in <module>
    import kivy.input.providers.mtdev
  File "/home/you/miniconda3/envs/donkey/lib/python3.7/site-packages/kivy/input/providers/mtdev.py", line 84, in <module>
    from kivy.lib.mtdev import Device, \
  File "/home/you/miniconda3/envs/donkey/lib/python3.7/site-packages/kivy/lib/mtdev.py", line 29, in <module>
    libmtdev = cdll.LoadLibrary('libmtdev.so.1')
  File "/home/you/miniconda3/envs/donkey/lib/python3.7/ctypes/init.py", line 442, in LoadLibrary
    return self._dlltype(name)
  File "/home/you/miniconda3/envs/donkey/lib/python3.7/ctypes/init.py", line 364, in init
    self._handle = _dlopen(self._name, mode)
OSError: libmtdev.so.1: cannot open shared object file: No such file or directory

```
Then please install `libmtdev`:
```bash
sudo apt-get update
sudo apt-get install libmtdev-dev
```

---
### Next let's [install software on Donkeycar](/guide/install_software/#step-2-install-software-on-donkeycar)
