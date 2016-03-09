# Building PX4 Software

PX4 can be built on the console or in a graphical development environment / IDE.

## Compiling on the Console

Before moving on to a graphical editor or IDE, it is important to validate the system setup. Do so by bringing up the terminal. On OS X, hit ⌘-space and search for 'terminal'. On Ubuntu, click the launch bar and search for 'terminal'. On Windows, find the PX4 folder in the start menu and click on 'PX4 Console'.

![](images/toolchain/terminal.png)

The terminal starts in the home directory. We default to '~/src/Firmware' and clone the upstream repository. Experienced developers might clone [their fork](https://help.github.com/articles/fork-a-repo/) instead.

<div class="host-code"></div>

```sh
mkdir -p ~/src
cd ~/src
git clone https://github.com/PX4/Firmware.git
cd Firmware
git submodule update --init --recursive
cd ..
```
Now its time to build the binaries by compiling the source code. But before going straight to the hardware, a [simulation run](simulation-sitl.md) is recommended as the next step. Users preferring to work in a graphical development environment should continue with the next section.

### NuttX / Pixhawk based boards

<div class="host-code"></div>

```sh
cd Firmware
make px4fmu-v2_default
```

Note the syntax: 'make' is the build tool, 'px4fmu-v2' is the hardware / autopilot version and 'default' is the default configuration. All PX4 build targets follow this logic. A successful run will end with this output:

<div class="host-code"></div>

```sh
[100%] Linking CXX executable firmware_nuttx
[100%] Built target firmware_nuttx
Scanning dependencies of target build_firmware_px4fmu-v2
[100%] Generating nuttx-px4fmu-v2-default.px4
[100%] Built target build_firmware_px4fmu-v2
```

By appending 'upload' to these commands the compiled binary will be uploaded via USB to the autopilot hardware:

<div class="host-code"></div>

```sh
make px4fmu-v2_default upload
```

A successful run will end with this output:

<div class="host-code"></div>

```sh
Erase  : [====================] 100.0%
Program: [====================] 100.0%
Verify : [====================] 100.0%
Rebooting.

[100%] Built target upload
```
### Raspberry Pi 2 boards
The command below builds the target for Raspbian (posix_pi2_release).

<div class="host-code"></div>

```sh
cd Firmware
make posix_rpi2_release # for cross-compiler build
```

The "mainapp" executable file is in the directory build_posix_rpi2_release/src/firmware/posix.
Copy it over to the RPi (replace YOUR_PI with the IP or hostname of your RPi, [instructions how to access your RPi](hardware-pi2.md#developer-quick-start))

```sh
scp build_posix_rpi2_release/src/firmware/posix/mainapp pi@YOUR_PI:/home/pi/
```

And run it with :
<div class="host-code"></div>

```sh
./mainapp
```


If you're building *directly* on the Pi, you will want the native build target (posix_pi2_default).

<div class="host-code"></div>

```sh
cd Firmware
make posix_rpi2_default # for native build
```

The "mainapp" executable file is in the directory build_posix_rpi2_default/src/firmware/posix.
Run it directly with :
<div class="host-code"></div>

```sh
./build_posix_rpi2_default/src/firmware/posix/mainapp
```

A successful build followed by executing mainapp will give you this :

<div class="host-code"></div>

```sh
[init] shell id: 1996021760
[init] task name: mainapp

______  __   __    ___
| ___ \ \ \ / /   /   |
| |_/ /  \ V /   / /| |
|  __/   /   \  / /_| |
| |     / /^\ \ \___  |
\_|     \/   \/     |_/

Ready to fly.


pxh>
```

### QuRT / Snapdragon based boards

The commands below build the targets for the Linux and the DSP side. Both executables communicate via [muORB](advanced-uorb.md).

<div class="host-code"></div>

```sh
cd Firmware
make posix_eagle_default
make qurt_eagle_default
```

To set up the development environment, see [Getting Started](https://github.com/ATLFlight/ATLFlightDocs/blob/master/GettingStarted.md). You must be able to do a graphical install of the Hexagon tools so a remote shell to the machine will not work.
Use the [HelloWorld](https://github.com/ATLFlight/ATLFlightDocs/blob/master/HelloWorld.md) and [dspal_tester](https://github.com/ATLFlight/dspal/blob/master/README.md) instructions to verify your setup is correct.

The quick instructions are:

Get the [SDK](https://developer.qualcomm.com/download/hexagon/hexagon-sdk-linux.bin) and Hexagon tools 7.2.10 installers.
```sh
git clone https://github.com/ATLFlight/cross_toolchain.git
mv qualcomm_hexagon_sdk_2_0_eval.bin cross_toolchain/downloads
mv Hexagon.LLVM_linux_installer_7.2.10.bin cross_toolchain/downloads
./cross_toolchain/install.sh
```

Follow the instructions to set up the development environment. If you accept all the installl defaults you can at any time re-run the following to get the env setup. It will only install missing components.
```sh
./cross_toolchain/install.sh

Make sure to set the following environment variables:
   export HEXAGON_SDK_ROOT=${HOME}/Qualcomm/Hexagon_SDK/2.0
   export HEXAGON_TOOLS_ROOT=${HOME}/Qualcomm/HEXAGON_Tools/7.2.10/Tools
   export HEXAGON_ARM_SYSROOT=${HOME}/Qualcomm/Hexagon_SDK/2.0/sysroot
   export PATH=${HEXAGON_SDK_ROOT}/gcc-linaro-arm-linux-gnueabihf-4.8-2013.08_linux/bin:$PATH

```

Clone current master:

```sh
git clone https://github.com/PX4/Firmware.git
cd Firmware
make posix_eagle_default
make qurt_eagle_default
```

To load the SW on the device, connect via USB cable and make sure the device is booted. In another terminal do:
```sh
adb shell
```
If that works, go back to previous terminal:
```sh
cd build_posix_eagle_default
make mainapp-load
cd build_qurt_eagle_default
make libmainapp-load
```
Or all in one line:
```sh
make posix_eagle_default && (cd build_posix_eagle_default && make mainapp-load) && make qurt_eagle_default && (cd build_qurt_eagle_default && make libmainapp-load)
```

Copy the startup files:
```sh
adb push posix-configs/eagle/flight/mainapp-flight.config /home/linaro/mainapp.config
adb push posix-configs/eagle/flight/px4-flight.config /usr/share/data/adsp/px4.config
```

Run the DSP debug monitor:
```sh
${HEXAGON_SDK_ROOT}/tools/mini-dm/Linux_Debug/mini-dm
```

Go back to ADB shell and run mainapp:
```sh
cd /home/linaro
./mainapp mainapp.config
```

## Compiling in a graphical IDE

The PX4 system supports Qt Creator, Eclipse and Sublime Text. Qt Creator is the most user-friendly variant and hence the only officially supported IDE. Unless an expert in Eclipse or Sublime, their use is discouraged. Hardcore users can find an [Eclipse project](https://github.com/PX4/Firmware/blob/master/.project) and a [Sublime project](https://github.com/PX4/Firmware/blob/master/Firmware.sublime-project) in the source tree.

{% youtube %}https://www.youtube.com/watch?v=Bkk8zttWxEI&rel=0&vq=hd720{% endyoutube %}

## Qt Creator Functionality

Qt creator offers clickable symbols, auto-completion of the complete codebase and building and flashing firmware.

![](images/toolchain/qtcreator.png)

### Qt Creator on Linux

<aside class="note">
Linux users can just load the CMakeLists.txt in the root firmware folder via File -> Open File or Project -> Select the CMakeLists.txt file.
</aside>

After loading, the 'play' button can be configured to run the project by selecting 'custom executable' in the run target configuration and entering 'make' as executable and 'upload' as argument.

### Qt Creator on Windows

<aside class="todo">
Windows has not been tested with Qt creator yet.
</aside>

### Qt Creator on Mac OS

Before starting Qt Creator, the [project file](https://cmake.org/Wiki/CMake_Generator_Specific_Information#Code::Blocks_Generator) needs to be created:

<div class="host-code"></div>

```sh
cd ~/src/Firmware
mkdir build_creator
cd build_creator
cmake .. -G "CodeBlocks - Unix Makefiles"
```

That's it! Start Qt Creator, then complete the steps in the video below to set up the project to build.

{% youtube %}https://www.youtube.com/watch?v=0pa0gS30zNw&rel=0&vq=hd720{% endyoutube %}
