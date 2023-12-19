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
sudo apt-get install libmtdev1 libgl1 xclip
```
* Add export `LD_PRELOAD=/usr/lib/x86_64-linux-gnu/libstdc++.so.6` to your `.bashrc` and re-source it.
  ```bash
  source ~/.bashrc
  ```

At this point switch to the [Ubuntu instructions](http://docs.donkeycar.com/guide/host_pc/setup_ubuntu/) and continue the setup there.

* Possible problems when running the UI

If you use the Donkey UI with an NVIDIA graphics card and you see a blurred window it might be due to some settings on 
your PC. In the settings, switch the NVIDIA graphics card 3D rendering mode from 
`let the running program decide the 3D rendering mode`
to `let me decide on the 3D rendering mode: Quality`.

---
## Next let's [install software on Donkeycar](/guide/install_software/#step-2-install-software-on-donkeycar)
