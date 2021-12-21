# Simple-AXI-SPI-Peripheral
A Custom AXI4 SPI master-only peripheral that can be instantiated within a Xilinx Vivado design to interface to SPI slaves.  The SPI interface uses standard MOSI, MISO, SCLK, and either an active-low or active-high SS.  A single general-purpose output port with bit width up to 32 can be optionally instantiated to use, for example, as a slave select or additional control lines.  The modules has run-time configurable baud rate, lsb- or msb-first bit ordering, and SPI mode (CPOL:CPHA) and includes a loopback feature for testing.  Double-buffered RX and TX data registers allow back-to-back transfers without de-asserting SS.  

##Motivation-

The SPI peripheral included in the Vivado IP catalog is overly complex when one simply needs to communicate with a single SPI peripheral.
