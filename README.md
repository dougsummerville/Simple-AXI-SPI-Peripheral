# Simple-AXI-SPI-Peripheral
An AXI4 SPI master-only peripheral that can be instantiated within a Xilinx Vivado design to interface SPI slave(s).  The SPI interface uses standard MOSI, MISO, SCLK, and either an active-low or active-high SS.  A single general-purpose output port with a width of up to 32 bits can be optionally enabled to use, for example, as slave select or additional control lines.  The SPI module has run-time configurable baud rate, bit ordering, and SPI mode (CPOL:CPHA) selection and includes a loopback feature for testing.  Double-buffered RX and TX data registers allow back-to-back transfers without de-asserting SS.  

## Motivation
The SPI peripheral included in the Vivado IP catalog is overly complex for simple designs.  This peripheral can be quickly instantiated and easily programmed from a hard- or soft-processor.  In addition, the optional GPIO port allows for custom output signals without instantiating a separate GPIO core.
![image](https://user-images.githubusercontent.com/64434702/146989767-5650805e-7dca-49f0-bb0a-f7cd8e72a37a.png)

## Build Status
The core has not been exhaustively tested and no benchmarking was performed.  The various features and modes were verified using both an oscilloscope and SPI protocol anayzer. The module was tested on a Digilent Basys3 FPGA development board.
## Instantiating the Core
The core can be instantiated and used like any other Xililx AXI4 IP.  Using the Xilinx "Customize IP" GUI, active-low SS (default) can be selected, or unchecked for an active high signal.  "Use GPIO" controls whether the optional general-purpose output port is instantiated; if selected, the "GPIO Width" parameter selects the width of the port from 1 to 32. 

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

### Status (S) Register 
The read-only status register contains two flags: SPTEF and SPRE.  Writes have no effect.

SPTE
: SPI Transmit buffer Empty (STBE) flag is 1 whenever the TX buffer is available to be written.  When the SPI is idle, two consecutive writes to the DATA register can be made before STBE goes low (due to double-buffering).  Before transmitting, SPTEF should be polled to indicate the byte can be sent.

SPRF
: SPI Receive Buffer Full (SRBF) flag becomes 1 whenever a new value from the slave is ready to be read from the DATA register.  A read of the S register followed by a read of the DATA register clears SRBF.  There is no protection against overrun.

BUSY
: SPI busy flag.  When 0, the SPI module has completed all previous transmissions and is idle.  When 1, a serial transmission is in progress.

| |`31...................................3`|`2` |`1` |`0` |
|-|----------------------------------------|----|----|----|
|R|`00000000000000000000000000000000000000`|BUSY|SPRF|SPTE|

### SPI Configuration Register (C)
The C register configures the SPI communication settings.  

DVSR
: Baud Clock Divisor.  The frequency of serial transmission is half the AXI clock frequency, Fa/2, divided by DVSR. For example, if the AXI bus is running at 100 MHz and DVSR is set to 200, the serial transmission rate will be ( 100MHz /2 /200 ) = 256 KHz

CPOL,CPHA
: Standard Motorola mode setting.  For example, to configure SPI mode 2 set CPOL=1 and CPHA=0;

LSBF
: Least significant bit first enable.  When 1, bytes are transmitted lsb-first; otherwise, msb-first but ordering is used.

LOOPE
: Enable loopback mode.  For testing.  MISO input is bypassed and the MOSI output is fed back into the RX input.

|   |`31..........20`|`19` |`18` |`17` |`16` |`15..............0`|
|---|----------------|-----|-----|-----|-----|-------------------|
|R/W|`00000000000000`|LOOPE|LSBF |CPOL |CPHA | DVSR              |

### GPIO Output Register 

This register contains the values driven on the (optional) GP output port.  Only the least significant 'w' bits are used, where 'w' is the configured width of the port.

## Installation
Create a local copy of the directory tree axi_spi_simple_1.0/ to your local hard drive.  Add the IP path to your Vivado project and instantiate the IP in the standard way.

## Using the IP Core

The following C code example demonstrates how one cn interface the IP core from software.  It does not access the I/O registers through the Xilinx API but accesses them directly using the known memory-mapped I/O address.  The code may need to be tailored for the specific development environment.  

...
...


