# Introduction and Setup

## What is micro:bit

- A simple programmable device, originally developed by BBC maily for educational purposes. But it has properties that makes them good for purposes outside of the educational scope.
- It is somewhat similar to other devices like Raspberry Pi and Arduino, but micro:bit is more self-contained in the sense that it has a simple LED display, input buttons, various sensors (microphone, touch senseor, accelerometer and a compass) as well as a speaker built-in.
- It is running on a Nordic SoC that encompasses an ARM Cortex-M4 core, a RAM, a Flash ROM and a radio component that enables communications such as Bluetooth.
- There are two versions of micro:bit with different hardware and development environment. We are only concerned with the newer V2 version in this course.

## How to setup the programming environment

Below, we introduce setup procedures for two ways of programming the micro:bit board: using CODAL, or using the Nordic SDK directly. CODAL is a library developed by Lancaster University that sits on top of the Nordic SDK and abstracts various hardware components. It makes programming micro:bit much simpler, but also hides details of the hardware that we want to explore later in the course.

### Setting up w/ CODAL programming environment (on Windows)

This part is fairly straightforward thanks to the chip on micro:bit that specifically handles the interface between your computer and the board. With this chip, micro:bit appears as a standard flash drive on you computer and uploading a compiled program is as simple as copying the `.hex` file onto that drive.

1. Install the [arm-none-eabi-gcc](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm/downloads) cross compiler. It is called a cross compiler because it runs on the x86 platform (out computer) but generate compiled code that works on ARM (micro:bit).
   1. Make sure to check `Add path to environment variable` to ensure that other programs can find the compiler.
   2. Open Windows PowerShell and type `arm-none-eabi-gcc -v` to verify that it is correctly installed.
2. Install [CMake](https://cmake.org/download) and [Ninja](https://ninja-build.org). These are called "make tools". Essentially, they follow instructions about how exactly how our project should be built described in a "makefile", then execute these instructions using the compiler. In theory you can also build projects manually using the compiler, without the help of any build tools, but this quickly becomes unfeasible as the project size grows.
   1. Make sure to add CMake to PATH at the end of its installation.
   2. To install Ninja, a simple solution is to copy the `ninja.exe` file to the `C:\Program Files\CMake\bin` directory (or whatever path you chose for CMake if not this default path).
   3. Verify they are installed by running the following commands in PowerShell: `cmake --version` and `ninja --version`.
3. Now we need to download the examples files.
   1. If you are familiar with [Git](https://git-scm.com/), go ahead and clone [this repository](https://github.com/lancaster-university/microbit-v2-samples). You can also simply download the `.zip` file using the link and decompress it to a directory of your choice if you don't want to use Git.
   2. Use [VS Code](https://code.visualstudio.com/) to open the directory you just cloned/downloaded.
   3. Click on the `CMakeLists.txt` file. A pop-up window will appear asking if you want to install an addon for CMake. Select "Yes".
   4. After installing the addon there should be a new extension for it on your side bar. Click on it and find "Configure All Projects" then select the "GCC arm-none-eabi" compiler. CMake will then automatically clone the CODAL libraries to the `library` sub directory in the project folder. This may take some time.
   5. After configuration is finished you should have everything that you need to build a simple example program. In the project directory, take a look at `source`->`main.cpp` and it should be pretty clear what the code is doing. Click the `Build` button on the bottom status bar of VS Code.
   6. If the build process succeeds there should be a `MICROBIT.hex` file in the project directory. Copy it onto the microbit drive and see it work!

### Setting up programming environment using the Nordic SDK
