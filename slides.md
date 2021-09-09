# Introduction and Setup

## What is micro:bit

- A simple programmable device, originally developed by BBC maily for educational purposes. But it has properties that makes them good for many projects not limited to the educational scope.
- It is somewhat similar to other devices like Raspberry Pi and Arduino, but micro:bit is more self-contained in the sense that it has a simple LED display, input buttons, various sensors (microphone, touch senseor, accelerometer and a compass) as well as a speaker built-in.
- It is running on a Nordic SoC with an ARM Cortex-M4 core, RAM and Flash ROM. The SoC also enables radio connectivity, including Bluetooth support.
- There are two versions of micro:bit with different hardware and development environment. We are only concerned with the newer V2 version in this course.

## How to setup the programming environment

Below, we introduce setup procedures for two ways of programming the micro:bit board: using CODAL, or using the Nordic SDK directly. CODAL is a library developed by Lancaster University that sits on top of the Nordic SDK and abstracts various hardware components. It makes programming micro:bit much simpler, but also hides details of the hardware that we want to explore later in the course.

### Setting up w/ CODAL programming environment

This part is fairly straightforward, and can be done on Windows, Mac or Linux thanks to the fact that there is a chip on micro:bit that specifically handles the interface between your computer and the board. With this chip, micro:bit appears as a standard flash drive on you computer and uploading a compiled program is as simple as copying the `.hex` file onto that drive. Later we will use other tools to directly debug on the board, which is best done on a Linux environment.

#### Windows

1. We need to install a cross compiler kit that is capable of compiling the C code to ARM architecture, instead of the x86 architecture that our computer uses (They are called cross compilers because they run on x86 but generate compiled code in ARM). Install [arm-none-eabi-gcc](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm/downloads).
   1. Make sure to check `Add path to environment variable` to ensure that other programs can find the compiler.
   2. Open Windows Powershell and type `arm-none-eabi-gcc -v` to verify that it is correctly installed.
2. We then need to install make tools. Essentially these tools read a "makefile" that contains instructions about how exactly how our project should be built, then use the compilers to build the project, following these instructions. In theory you can also build projects manually using the compiler, but this quickly becomes unfeasible as the project size grows. On Windows, we need to install [CMake](https://cmake.org/download) and [Ninja](https://ninja-build.org).
   1. Make sure to add CMake to PATH at the end of its installation.
   2. To install Ninja, a simple solution is to copy it to the `CMake\bin` directory. CMake should have been installed to `C:\Program Files\CMake\` by default.
3. Now we clone the [example repository from Lancaster University](https://github.com/lancaster-university/microbit-v2-samples). 
   1. After cloning it, use [VS Code](https://code.visualstudio.com/) to open the directory we just cloned.
   2. Click on the `CMakeLists.txt` file. A pop-up window will appear asking if you want to install an addon for CMake. Select "Yes".
   3. After installing the addon there should be a new extension for it on your side bar. Click on it and find "Configure All Projects" then select the "GCC arm-none-eabi" compiler. CMake will then automatically clone the CODAL libraries to the `library` sub directory in the project folder. This may take some time.
   4. After configuration is finished we should have everything we need to build a simple example program and upload it onto the board. Go to `source`->`main.cpp` to take a look at the code, then click the `Build` button on the bottom status bar of VS Code.
   5. If the build process succeeds there should be a `MICROBIT.hex` file in the project directory. Copy it onto the microbit drive and see it work! 

### Setting up programming environment using the Nordic SDK
