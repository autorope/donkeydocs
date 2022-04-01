## Install Donkeycar on Mac

![donkey](/assets/logos/apple_logo.jpg)

* Install [miniconda Python 3.7 64 bit](https://conda.io/miniconda.html)

* Install [git 64 bit](https://www.atlassian.com/git/tutorials/install-git)

* Start Terminal

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

* Get a stable release from Github.

```bash
git clone https://github.com/autorope/donkeycar
cd donkeycar
git fetch --all --tags
git checkout tags/4.3.6.1
```

* If this is not your first install, update Conda and remove old donkey

```bash
conda update -n base -c defaults conda
conda env remove -n donkey
```

* Create the Python anaconda environment

```bash
conda env create -f install/envs/mac.yml
conda activate donkey
pip install -e .[pc]
```
We have observed that the `conda` installation can be slow (not as slow in OSX as in Linux, but 
still slow). If the install looks like it's hanging then you can install with `mamba` instead. 
This should take < 5 min. In that case please run:
```bash
conda install mamba -n base -c conda-forge
mamba env create -f install/envs/ubuntu.yml
conda activate donkey
pip install -e .[pc]
```
Note: if you are using ZSH (you'll know if you are), you won't be able to run `pip install -e .[pc]`. You'll need to escape the brackets and run `pip install -e .\[pc\]`.

* Tensorflow GPU

Currently there is no NVidia gpu support for [tensorflow on mac](https://www.tensorflow.org/install#install-tensorflow).

* Create your local working dir:

```bash
donkey createcar --path ~/mycar
```

> Note: After closing the Terminal, when you open it again, you will need to 
> type ```conda activate donkey``` to re-enable the mappings to donkey specific 
> Python libraries

----

### Next let's [install software on Donkeycar](/guide/install_software/#step-2-install-software-on-donkeycar)
