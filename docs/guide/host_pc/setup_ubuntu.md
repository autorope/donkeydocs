# Install Donkeycar on Linux


> Note : tested on Ubuntu 20.04 LTS, 22.04 LTS

* Open the Terminal application.

* Install miniconda Python 3.9 64 bit. 

```bash
wget https://repo.anaconda.com/miniconda/Miniconda3-py39_23.3.1-0-Linux-x86_64.sh
bash ./Miniconda3-py39_23.3.1-0-Linux-x86_64.sh
```

Setup your `donkey` conda env with:

```bash
conda create -n donkey python=3.9
conda activate donkey
```

Now there are two different installations possible. Very likely you will 
want to do the user install. Then you will perform Step 
[_User install_](#user-install). In case 
you want to debug or edit the source code, you will need to do the more advanced 
[_Developer install_](#developer-install). But you can do only one.

> _**Note**_: Only do User install or Developer install but not both!

### User install

As you have activated the new `donkey` env already you simply type:

```bash
pip install donkeycar[pc]
```
This will install the latest release.

### Developer install

Here you can choose which branch or tag you want to install, and you can 
edit and/or debug the code, by downloading the source code from GitHub.

Create a project directory you would like to use as the 
head of your projects, change into it and download and install `donkeycar` 
from GitHub.

```bash
mkdir projects
cd projects
git clone https://github.com/autorope/donkeycar
cd donkeycar
git checkout main
pip install -e .[pc]
```

Note: if you are using ZSH (you'll know if you are), you won't be able to 
run `pip install -e .[pc]`. You'll need to escape the brackets and run 
`pip install -e .\[pc\]`.


* If this is not your first install, update Conda and remove old donkey

```bash
conda update -n base -c defaults conda
conda env remove -n donkey
```

The newer version of Tensorflow is already built with GPU support. If you 
have an Nvidia GPU, install Cuda 11 following instructions on Nivida's page 
[here](https://developer.nvidia.com/cuda-11.0-download-archive)

* Optional Install Coral edge tpu compiler

If you have a Google Coral edge tpu, you may wish to compile models. You 
will need to install the edgetpu_compiler exectutable. Follow [their 
instructions](https://coral.withgoogle.com/docs/edgetpu/compiler/).

* Optionally configure PyTorch to use GPU - only for NVidia Graphics cards

If you have an NVidia card, you should update to the latest drivers and 
[install Cuda SDK](https://www.tensorflow.org/install/gpu#windows_setup).

```bash
conda install cudatoolkit=11 -c pytorch
```

You should replace `<CUDA Version>` with your CUDA version. Any version 
above 10.0 should work. You can find out your CUDA version by running 
`nvcc --version` or `nvidia-smi`. (if those commands don't work, it means you 
don't already have them installed. Follow the directions given by that error 
to install them.) If the version given by these two commands don't match, go 
with the version given by `nvidia-smi`.

* Create your local working dir:

```bash
donkey createcar --path ~/mycar
```

> Note: After closing the Anaconda Prompt, when you open it again, you will need to 
> type ```conda activate donkey``` to re-enable the mappings to donkey specific 
> Python libraries

----

### Next let's [install software on Donkeycar](/guide/install_software/#step-2-install-software-on-donkeycar)
