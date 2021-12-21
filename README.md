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

## Interfacing the Peripheral from Software

The peripheral contains four 32b registers using the first four offsets from the base address.
|  Address  |Register| Purpose               |
|-----------|--------|-----------------------|
|base + 0x0 |DATA    | SPI RX/TX Data Reg    |
|base + 0x4 |S       | SPI Status Reg        |
|base + 0x8 |C       | SPI Configuration Reg |
|base + 0xC |GPIO    | GPIO Output Data      |

### DATA Register 
The data register is the buffer for the double-buffered receive and transmit. Reads from the data register will return the most recent received data from the slave in DATA[7:0]; DATA[31:8] will always read 0.  Writes to DATA[7:0] will buffer the next octet to be transmitted when the SPI link becomes available.  The SPI status register has flags to indicate when the RX buffer is not empty and the TX buffer is empty.

| |`31.............................8`|`7......0`|
|-|----------------------------------|----------|
|R|`00000000000000000000000000000000`|`RxData  `|
|W|`00000000000000000000000000000000`|`TXData  `|

