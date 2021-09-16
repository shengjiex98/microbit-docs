# Setting up the programming environment with Nordic SDK (on Linux)

- [Setting up the programming environment with Nordic SDK (on Linux)](#setting-up-the-programming-environment-with-nordic-sdk-on-linux)
  - [Before you start](#before-you-start)
  - [Overview](#overview)
  - [Things we need for compiling code](#things-we-need-for-compiling-code)
    - [Install the GCC cross compiler](#install-the-gcc-cross-compiler)
    - [Download the Nordic SDK](#download-the-nordic-sdk)
    - [Download the *blessed-lab* project](#download-the-blessed-lab-project)
    - [Check that `make` is installed](#check-that-make-is-installed)
  - [Things we need for debugging](#things-we-need-for-debugging)
    - [Install OpenOCD](#install-openocd)
    - [Install Eclipse IDE](#install-eclipse-ide)
  - [Run an example](#run-an-example)
  - [Debugging information](#debugging-information)

## Before you start

The content of this guide has been tested on Ubuntu 20.04. While the guide seems long, the instructions are very detailed and I promise there will be less work to do than it seems :)

## Overview

There will be quite a few pieces of software that you need to install in this guide. But don't worry, each of them has a purpose and you should have a good idea of what each of them do by the end of this overview section. Here's a list of things we need

- A cross compiler w/ debugger. We will continue to use the [arm-none-eabi-gcc](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm/downloads) that we used with CODAL.
- The Nordic SDK. This is a collection of libraries—written by the manufacturer of the SoC on the micro:bit board—that enables us to utilize the ARM processor on the micro:bit board. However, the SDK has no knowledge of other components on the board.
- A custom project called *blessed-lab*. It it a collection of libraries specifically written for the micro:bit. For our purposes it fits a similar role as CODAL but is much less encapsulated and provides more freedom when we write our code.
- Build tools. We will use the Ubuntu default build tool `make`.

These are all we need to write code and run it on the micro:bit board. However, to help debugging, it is helpful to set up some additional tools that enables us to debug on the board itself, check the values of the registers, and view the ARM assembly code. To do this, we additionally need:

- An on-chip debugger. As the name suggests, it allows us to debug in real time on the micro:bit board. We will use [Open On-Chip Debugger](https://openocd.org/).
- An IDE with good support for embedded C programming and debugging. We will use [Eclipse](https://www.eclipse.org/ide/).

## Things we need for compiling code

### Install the GCC cross compiler

This can be easily done in the terminal. Run the following command in the terminal in Ubuntu. (The `$` sign before each line just indicates that the content following it is a command and it needs to be run in the terminal. You *do not* need to enter the `$` sign yourself)

```
$ sudo apt-get update
$ sudo apt-get install gcc-arm-none-eabi
$ sudo apt-get install gdb-multiarch
```

### Download the Nordic SDK

Create an empty directory. This will be your project directory. Download [nRF5_SDK_17.0.2](https://www.nordicsemi.com/Software-and-tools/Software/nRF5-SDK/Download). Uncheck the checkmarks in the "SoftDevices" section so that the only item you have under "You have selected" is `nRF5_SDK_17.0.2_d674dde.zip`. Unzip and place it in a subfolder of your project directory called `nRF5_SDK_17.0.2_d674dde` (When you unzip the SDK, this folder will be created automatically).

### Download the *blessed-lab* project

Download and place the *blessed-lab* folder in your project directory

### Check that `make` is installed

Enter the following command in terminal and verify that `make` is installed.
```
$ make --version
```

## Things we need for debugging

### Install OpenOCD

In an ideal world, we would install OpenOCD using `apt-get` just like the cross-compiler, and all the dependencies will be taken care of. Unfortunately, we need to install a newer version that is not updated in the offical `apt-get` repository yet. That requires us to download and compile the source code of OpenOCD.

1. Check if required packages are installed. The `>=` sign means the package version number should be equal to or greater than the version number indicated after the sign.
    - `make`
    - `libtool`
    - `pkg-config >= 0.23` (or compatible)
    - `libusb-1.0-0-dev`
    - `autoconf >= 2.69`
    - `automake >= 1.14`
    - `texinfo >= 5.0`

    To check whether a package is installed and/or what version it is, use the `--version` argument. For example, to check if `pkg-config` is installed and what version it is, run 
    ```
    $ pkg-config --version
    ```
    If any package is missing or needs to be updated, check [this link](https://askubuntu.com/questions/428772/how-to-install-specific-version-of-some-package) for tutorial for installing specific version of a package.

2. Clone the OpenOCD repo (the location doesn't matter)
    ```
    $ git clone git://git.code.sf.net/p/openocd/code openocd-code
    ```

3. Build and install OpenOCD
    ```
    $ cd openocd-code
    $ ./bootstrap
    $ ./configure
    $ make
    $ sudo make install
    ```
    For more information regarding installing OpenOCD, refer to the `README` file in the `openocd-code` directory.

### Install Eclipse IDE

1. Install Eclipse IDE with C/C++ Development Tools (CDT) Download the latest version of [Eclipse IDE](https://www.eclipse.org/downloads). When installing, select `Eclipse IDE for C/C++ Developers`.

2. Install Eclipse plugins

    Within Eclipse IDE, go to `Help -> Install New Software`. In the drop-down menu `Work with`, select the URL that starts with CDT, then search and select the following plugins.

    - C/C++ GCC Cross Compiler Support
    - C/C++ GDB Hardware Debugging
    - C/C++ Memory View Enhancements
    
    Check on the lower left corner of the search box that you have 3 items selected, then click `Next` to finish the installation.

    Repeat the above procedure, this time enter https://download.eclipse.org/embed-cdt/updates/v6/ in the `Work with` menu, click `Add` to the right of the drop-down menu to save it as a available site, and search for 

    - Embedded C/C++ OpenOCD Debugging

    Similarly, add http://embsysregview.sourceforge.net/update as an available site and download 

    - Embedded Systems Register View (SFR)
    - EmbSysRegView Data

## Run an example

Open a terminal window and navigate to `[your_project_dir]/blessed-lab/examples/microbit_leds`. Then build the project.

```
$ cd blessed-lab/examples/radio-broadcaster
$ make
```

There should now be a `timing-example.hex` file in the `build` subfolder. You can manually copy it to the micro:bit drive, or use the OpenOCD interface:

```
$ chmod +x flash_openocd.sh
$ ./flash_openocd.sh
```

If this step fails with the warning "Error: unable to find a matching CMSIS-DAP device", try running 
```
$ sudo ./flash_openocd.sh
```
and see if it works. If this succeeds, there's a permission issue with the OpenOCD interface. Follow the steps described in the "Other Linux distros" section in [this link](https://forgge.github.io/theCore/guides/running-openocd-without-sudo.html) to make it work without using `sudo`.

You should now be able to see the LEDs on board blinking.

## Debugging information
In Eclipse IDE:
1. Import the `microbit_leds` example as a makefile project 
2. Click in menu: `Run -> Debug Configurations`
3. Create a new `GDB Openocd Debugging` configuration
   1. in the "Main" tab, set "C/C++ Application" to `[PROJECTDIR]/blessed-lab/examples/microbit_leds/build/timing-example.out` (replace `[PROJECTDIR]` by an absolute path to your project). Click "Apply".
   2. in the "Debugger" tab, set the executable path to `/usr/local/bin/openocd` and the actual executable to `/usr/local/bin/openocd`.
   3. in the "Config options" of the "Debugger" tab, insert `-f interface/cmsis-dap.cfg -f target/nrf52.cfg`
   4. under "GDB client setup", set "Executable name" and "Actual executable" both to `/usr/bin/gdb-multiarch`. Click "Apply"
4. Click "Debug"
   1. To view compiled assembly code, click in menu `Window -> Show View -> Disassembly`.