# Notes for micro:bit development

## Arch Linux
### arm-none-eabi
`arm-none-eabi-gcc` might throw:
```
/usr/lib/gcc/arm-none-eabi/11.2.0/include/stdint.h:9:26: fatal error: stdint.h: No such file or directory
 # include_next <stdint.h>
```

This is because there's no other `stdint.h` file on the computer. In this case, install the `arm-none-eabi-newlib` package:

```
$ sudo pacman -S arm-none-eabi-newlib
```

### gcc-multiarch
...is not available as a official repo package. Use the AUR package [gdb-multiarch](https://aur.archlinux.org/packages/gdb-multiarch/)

### OpenOCD
... is much easier to install on Arch. Simply run
```
$ sudo pacman -S openocd
```


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

## Changes to microbit-lab

1. Added scrolling code
2. Removed `/build` folders in exercises from git.
3. Removed `/.settings` folders from projects.
4. Changed project names in `.project` setting file from `microbit_led` to `lab4_and_lab5`, `lab6`, `lab4_solution` and so on.
5. Added build configurations for each project (using `make`).
6. Added launch (debug) configuration files. Turns out these configurations are not stored in the `.project` or `.cproject` files and must be imported separately.
7. Changed "Linked resources" to use relative paths. (Was using `/home/pk/workspace/microbit/blessed-devel/include` etc.)

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