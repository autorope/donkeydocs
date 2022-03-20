# Install Donkeycar on Linux

![donkey](/assets/logos/linux_logo.png)

> Note : tested on Ubuntu 20.04 LTS

* Open the Terminal application.

* Install [miniconda Python 3.7 64 bit](https://conda.io/miniconda.html). 

```bash
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash ./Miniconda3-latest-Linux-x86_64.sh
```

* Change to a dir you would like to use as the head of your projects.

```bash
mkdir projects
cd projects
```

* Get the latest donkeycar from Github.

```bash
git clone https://github.com/autorope/donkeycar
cd donkeycar
git checkout main
```

* To get a stable release

```bash
git clone https://github.com/autorope/donkeycar
cd donkeycar
git fetch --all --tags
git checkout tags/4.3.6
```

* If this is not your first install, update Conda and remove old donkey

```bash
conda update -n base -c defaults conda
conda env remove -n donkey
```

* Create the Python anaconda environment

```bash
conda env create -f install/envs/ubuntu.yml
conda activate donkey
pip install -e .[pc]
```
We have observed that the `conda` installation can be very slow. If the install looks like it's hanging
then you can install with `mamba` instead. This should take < 5 min. In that case please run:
```bash
conda install mamba -n base -c conda-forge
mamba env create -f install/envs/ubuntu.yml
conda activate donkey
pip install -e .[pc]
```
Note: if you are using ZSH (you'll know if you are), you won't be able to run `pip install -e .[pc]`. 
You'll need to escape the brackets and run `pip install -e .\[pc\]`.



* Optional Install Tensorflow GPU - only for NVidia Graphics cards

You should have an NVidia GPU with the latest drivers. Conda will handle installing the correct cuda and cuddn libraries for the version of tensorflow you are using.

```bash
conda install tensorflow-gpu==2.2.0
```

* Optional Install Coral edge tpu compiler

If you have a Google Coral edge tpu, you may wish to compile models. You will need to install the edgetpu_compiler exectutable. Follow [their instructions](https://coral.withgoogle.com/docs/edgetpu/compiler/).

* Optionally configure PyTorch to use GPU - only for NVidia Graphics cards

If you have an NVidia card, you should update to the latest drivers and [install Cuda SDK](https://www.tensorflow.org/install/gpu#windows_setup). 

```bash
conda install cudatoolkit=<CUDA Version> -c pytorch
```

You should replace `<CUDA Version>` with your CUDA version. Any version above 10.0 should work. You can find out your CUDA version by running `nvcc --version` or `nvidia-smi`. (if those commands don't work, it means you don't already have them installed. Follow the directions given by that error to install them.) If the version given by these two commands don't match, go with the version given by `nvidia-smi`.

* Create your local working dir:

```bash
donkey createcar --path ~/mycar
```

> Note: After closing the Anaconda Prompt, when you open it again, you will need to 
> type ```conda activate donkey``` to re-enable the mappings to donkey specific 
> Python libraries

----

### Next let's [install software on Donkeycar](/guide/install_software/#step-2-install-software-on-donkeycar)
