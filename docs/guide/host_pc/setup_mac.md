## Install Donkeycar on Mac

![donkey](/assets/logos/apple_logo.jpg)

# Create a persistent named venv

bash```
uv venv ~/.venvs/donkeycar --python 3.12
```
# Activate on login

bash```
echo 'source ~/.venvs/donkeycar/bin/activate' >> ~/.zshrc
source ~/.venvs/donkeycar/bin/activate
```
# User install (PyPI release):

bash```
uv pip install donkeycar[macos]
```
# Developer install (git clone):

bash```
uv pip install -e ".[macos,dev]"
```
### Next let's [install software on Donkeycar](/guide/install_software/#step-2-install-software-on-donkeycar)
