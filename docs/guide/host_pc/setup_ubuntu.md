# Install Donkeycar on Linux


> Note : tested on Ubuntu 20.04 LTS, 22.04 LTS

* Open the Terminal application.

* Install UV. 

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

# Create a persistent named venv
uv venv ~/.venvs/donkeycar --python 3.12

# Activate on login (bash)
echo 'source ~/.venvs/donkeycar/bin/activate' >> ~/.bashrc
source ~/.venvs/donkeycar/bin/activate

# User install (PyPI release):
uv pip install donkeycar[pc]


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
uv pip install -e ".[pc,dev]"
```

* Optional Install Coral edge tpu compiler

If you have a Google Coral edge tpu, you may wish to compile models. You 
will need to install the edgetpu_compiler exectutable. Follow [their 
instructions](https://coral.withgoogle.com/docs/edgetpu/compiler/).


----

### Next let's [install software on Donkeycar](/guide/install_software/#step-2-install-software-on-donkeycar)
