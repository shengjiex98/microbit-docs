# Setting up the programming environment with Nordic SDK (on Linux)

- [Setting up the programming environment with Nordic SDK (on Linux)](#setting-up-the-programming-environment-with-nordic-sdk-on-linux)
  - [Before you start](#before-you-start)
  - [Overview](#overview)
  - [Things we need for compiling code](#things-we-need-for-compiling-code)
    - [Install the GCC cross compiler](#install-the-gcc-cross-compiler)
    - [Download the Nordic SDK](#download-the-nordic-sdk)
    - [Download the *blessed* project](#download-the-blessed-project)
    - [Check that `make` is installed](#check-that-make-is-installed)
  - [Things we need for debugging](#things-we-need-for-debugging)
    - [Install OpenOCD](#install-openocd)
    - [Install Eclipse IDE](#install-eclipse-ide)
  - [Run an example](#run-an-example)
  - [Debugging information](#debugging-information)
  - [Appendix](#appendix)

## Before you start

The content of this guide has been tested on Ubuntu 20.04. While the guide seems long, the instructions are very detailed and I promise there will be less work to do than it seems :)

## Overview

There will be quite a few pieces of software that you need to install in this guide. But don't worry, each of them has a purpose and you should have a good idea of what each of them do by the end of this overview section. Here's a list of things we need

- A cross compiler w/ debugger. We will continue to use the [arm-none-eabi-gcc](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm/downloads) that we used with CODAL.
- The Nordic SDK. This is a collection of libraries—written by the manufacturer of the SoC on the micro:bit board—that enables us to utilize the ARM processor on the micro:bit board. However, the SDK has no knowledge of other components on the board.
- A custom project called *blessed*. It it a collection of libraries specifically written for the micro:bit. For our purposes it fits a similar role as CODAL but is much less encapsulated and provides more freedom when we write our code.
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

Create an empty directory. This will be your project directory. Download [nRF5_SDK_17.0.2](https://www.nordicsemi.com/Software-and-tools/Software/nRF5-SDK/Download). Uncheck the checkmarks in the "SoftDevices" section so that the only item you have under "You have selected" is `nRF5_SDK_17.0.2_d674dde.zip`. Unzip and place it in a subfolder of your project directory called `nRF5_SDK_17.0.2_d674dde` (When you unzip the SDK, this folder will be created automatically)

We also need to put a `.h` header file specific to the micro:bit board in the SDK. In the `nRF5_SDK_17.0.2_d674dde` directory, in subfolder `components/boards`, create `custom_board.h`. Its contents are included in the end of this guide.

### Download the *blessed* project

Download and place the *blessed-devel* folder in your project directory

### Check that `make` is installed

Enter the following command in terminal and verify that `make` is installed.

```
$ make --version
```

## Things we need for debugging

### Install OpenOCD

We can install OpenOCD using `apt-get` just like the cross-compiler. Unfortunately, we need to install a newer version that is not updated in the offical `apt-get` repository yet. That requires us to download and compile the source code of OpenOCD.


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

Open a terminal window and navigate to `[your_project_dir]/blessed-devel/examples/microbit_leds`. Then build the project.

```
$ cd blessed-devel/examples/radio-broadcaster
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
   1. in the "Main" tab, set "C/C++ Application" to `[PROJECTDIR]/blessed-devel/examples/microbit_leds/build/timing-example.out` (replace `[PROJECTDIR]` by an absolute path to your project). Click "Apply".
   2. in the "Debugger" tab, set the executable path to `/usr/local/bin/openocd` and the actual executable to `/usr/local/bin/openocd`.
   3. in the "Config options" of the "Debugger" tab, insert `-f interface/cmsis-dap.cfg -f target/nrf52.cfg`
   4. under "GDB client setup", set "Executable name" and "Actual executable" both to `/usr/bin/gdb-multiarch`. Click "Apply"
4. Click "Debug"
   1. To view compiled assembly code, click in menu `Window -> Show -> Disassembly`.

## Appendix

```C
---  custom_board.h ---
/**
 * Copyright (c) 2012 - 2020, Nordic Semiconductor ASA
 *
 * All rights reserved.
 *
 * Redistribution and use in source and binary forms, with or without modification,
 * are permitted provided that the following conditions are met:
 *
 * 1. Redistributions of source code must retain the above copyright notice, this
 *    list of conditions and the following disclaimer.
 *
 * 2. Redistributions in binary form, except as embedded into a Nordic
 *    Semiconductor ASA integrated circuit in a product or a software update for
 *    such product, must reproduce the above copyright notice, this list of
 *    conditions and the following disclaimer in the documentation and/or other
 *    materials provided with the distribution.
 *
 * 3. Neither the name of Nordic Semiconductor ASA nor the names of its
 *    contributors may be used to endorse or promote products derived from this
 *    software without specific prior written permission.
 *
 * 4. This software, with or without modification, must only be used with a
 *    Nordic Semiconductor ASA integrated circuit.
 *
 * 5. Any software provided in binary form under this license must not be reverse
 *    engineered, decompiled, modified and/or disassembled.
 *
 * THIS SOFTWARE IS PROVIDED BY NORDIC SEMICONDUCTOR ASA "AS IS" AND ANY EXPRESS
 * OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES
 * OF MERCHANTABILITY, NONINFRINGEMENT, AND FITNESS FOR A PARTICULAR PURPOSE ARE
 * DISCLAIMED. IN NO EVENT SHALL NORDIC SEMICONDUCTOR ASA OR CONTRIBUTORS BE
 * LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR
 * CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE
 * GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION)
 * HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT
 * LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT
 * OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
 *
 */
#ifndef PCA10000_H
#define PCA10000_H

#ifdef __cplusplus
extern "C" 
#endif

#include "nrf_gpio.h"

// Definitions for PCA10000 v2.0.0 or higher

#define LEDS_NUMBER    0


#define LEDS_ACTIVE_STATE 0

#define BUTTONS_LIST 
#define LEDS_LIST 

#define LEDS_INV_MASK  LEDS_MASK

// there are no buttons on this board
#define BUTTONS_NUMBER 0

// UART pins connected to J-Link

#define TX_PIN_NUMBER   NRF_GPIO_PIN_MAP(0,6)
#define RX_PIN_NUMBER  NRF_GPIO_PIN_MAP(1,8)
#define CTS_PIN_NUMBER 0
#define RTS_PIN_NUMBER 0
#define HWFC           false

#ifdef __cplusplus

#endif

#endif
```