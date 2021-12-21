# Simple-AXI-SPI-Peripheral
A Custom AXI4 SPI master-only peripheral that can be instantiated within a Xilinx Vivado design to interface to SPI slaves.  The SPI interface uses standard MOSI, MISO, SCLK, and either an active-low or active-high SS.  A single general-purpose output port with bit width up to 32 can be optionally instantiated to use, for example, as a slave select or additional control lines.  The modules has run-time configurable baud rate, lsb- or msb-first bit ordering, and SPI mode (CPOL:CPHA) and includes a loopback feature for testing.  Double-buffered RX and TX data registers allow back-to-back transfers without de-asserting SS.  

## Motivation
The SPI peripheral included in the Vivado IP catalog is overly complex when one simply needs to communicate with a single SPI peripheral.  This peripheral can be quickly instantiated and easily programmed from a hard- or soft-processor.  In addition, the optional GPIO port allows for custom output signals without instantiating a separate GPIO core.
![image](https://user-images.githubusercontent.com/64434702/146989767-5650805e-7dca-49f0-bb0a-f7cd8e72a37a.png)

## Build Status
The core has not been exhaustively tested and no benchmarking was performed.  The various features and modes were verified on an oscilloscope and SPI protocol anayzer and the GPIO feature tested on an FPGA development board.
## Instantiating the Core
The core can be instantiated and used like any other Xililx AXI4 IP.  Using the Xilinx "Customize IP" GUI, active-low SS can be checked or unchecked (default is checked) and "Use GPIO" controls whether the optional general-purpose output port is instantiated; if selected, the "GPIO Width" parameter selects the width of the port from 1 to 32. 

![image](https://user-images.githubusercontent.com/64434702/146989392-0e6dac22-6615-4c7d-a180-af2f8fb69ffc.png)

