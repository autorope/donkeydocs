# Windows

Donkey Car used to support a native Windows installation but this has been 
deprecated in favor of the WSL install. 
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
## Next let's [install software on Donkeycar](/guide/install_software/#step-2-install-software-on-donkeycar)
