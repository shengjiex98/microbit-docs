# Notes for micro:bit development

## Write C and compile it to assembly
1. [Nordic nRF52833 SoC](https://infocenter.nordicsemi.com/index.jsp?topic=%2Fps_nrf52833%2Fkeyfeatures_html5.html) with ARM Cortex M4
2. Develop use [Nordic SDK](https://www.nordicsemi.com/Products/Development-software/nrf5-sdk/download) (which is essentially a collection of C libraries)

## Step through debugging for C and assembly
Use Eclipse IDE with plugins. Refer to the setup guide.

## Access peripherals
Refer to micro:bit documentation for pin connections.

[For built-in peripherals](https://tech.microbit.org/hardware/schematic/)

[For the edge connector](https://tech.microbit.org/hardware/edgeconnector/)


## What's JTAG?
A debugger interface. OpenOCD uses SWD for debugging. (The CMSIS-DAP acts as a medium for debugging)

## Measure power consumption
Using the included battery pack, cut the negative wire (why negative?) and connect a resistor. Use a [picoscope](https://tech.microbit.org/hardware/edgeconnector/) to measure the voltage and current.

## Slides

1. What is the micro:bit board, what it has/does
2. Set up programming environment using the Nordic SDK, using the manual
3. How to write one simple program, compile to HEX, run it on the board
4. Semantics

## Book
1. Chapter 2 (CODAL vs SDK)


## Appendix: debugging information
*Note: this should have been done for you. In case something goes wrong, refer the the steps below to configure Build and Debug procedures.*

In Eclipse IDE:
1. Import the `lab4_and_lab5` example as a makefile project
2. Right-click on the project folder on the left sidebar and click "Properties".
   1. in the "C/C++ Build" tab, uncheck "Use default build command". Then set "Build command" to `make`.
3. Click in menu: "Run" -> "Debug Configurations"
4. Create a new "GDB Openocd Debugging" configuration
   1. in the "Main" tab, set "C/C++ Application" to `[PROJECTDIR]/blessed-lab/examples/microbit_leds/build/timing-example.out` (replace `[PROJECTDIR]` by an absolute path to your project). Click "Apply".
   2. in the "Debugger" tab, set the executable path to `/usr/local/bin/openocd` and the actual executable to `/usr/local/bin/openocd`.
   3. in the "Config options" of the "Debugger" tab, insert `-f interface/cmsis-dap.cfg -f target/nrf52.cfg`
   4. under "GDB client setup", set "Executable name" and "Actual executable" both to `/usr/bin/gdb-multiarch`. Click "Apply"
5. Click "Debug"
   1. To view compiled assembly code, click in menu "Window" -> "Show View" -> "Disassembly".