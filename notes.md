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