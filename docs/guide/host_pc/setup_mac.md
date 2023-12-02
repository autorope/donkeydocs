## Install Donkeycar on Mac

![donkey](/assets/logos/apple_logo.jpg)

* Install [miniconda Python 3.9 64 bit](https://conda.io/miniconda.html)

* Start Terminal

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
if you are using an Intel Mac or you type:

```bash
pip install donkeycar[macos]
```
if you are on Apple Silicon. This will install the latest release.

### Developer install

Here you can choose which branch or tag you want to install, and you can 
edit and/or debug the code, by downloading the source code from GitHub.

Install [git 64 bit](https://www.atlassian.com/git/tutorials/install-git) 
and change to a dir you would like to use as the head of your projects. 

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

### Further steps

* If this is not your first install, update Conda and remove old donkey

```bash
conda update -n base -c defaults conda
conda env remove -n donkey
```

* Tensorflow GPU

Currently, there is no NVidia gpu support for 
[tensorflow on mac](https://www.tensorflow.org/install#install-tensorflow).

* Create your local working dir:

```bash
donkey createcar --path ~/mycar
```

> Note: After closing the Terminal, when you open it again, you will need to 
> type
>`conda activate donkey`
> to re-enable the mappings to donkey specific 
> Python libraries

----

### Next let's [install software on Donkeycar](/guide/install_software/#step-2-install-software-on-donkeycar)
