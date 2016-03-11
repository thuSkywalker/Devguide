# Installing Files and Code

Usage of the [Homebrew package manager](http://mxcl.github.com/homebrew/) for Mac OS X is recommended. The installation of Homebrew is quick and easy: [installation instructions](http://mxcl.github.com/homebrew/).

## Pixhawk

After installing Homebrew, copy these commands to your shell:

<div class="host-code"></div>

```sh
brew tap PX4/homebrew-px4
brew tap osrf/simulation
brew update
brew install git bash-completion genromfs kconfig-frontends gcc-arm-none-eabi
brew install astyle cmake ninja
# simulation tools
brew install ant graphviz sdformat3 protobuf eigen
```

Then install the required python packages:

<div class="host-code"></div>

```sh
sudo easy_install pip
sudo pip install pyserial empy
```

## Snapdragon Flight

Developers working on Snapdragon Flight should download the Hexagon Linux toolchain and execute the commands below:

<div class="host-code"></div>

```sh
cp ~/Downloads/Hexagon.LNX.7.2\ Installer-07210.1.tar .
vagrant up
vagrant ssh
```

Switch to the Firmware directory:

<div class="host-code"></div>

```sh
cd /Firmware
```

Then follow the instructions for the Linux installation: 

## Simulation

OS X comes with CLANG pre-installed. No further installation steps are required.

## Editor / IDE

And finally download and install the Qt Creator app: [Download](http://www.qt.io/download-open-source/#section-6)

Now continue to run the [first build](starting-building.md)!
