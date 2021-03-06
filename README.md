fpga-drive-aximm-pcie
=====================

Example design for FPGA Drive using the AXI Memory Mapped to PCI Express Bridge IP.

## Requirements

This project is designed for Vivado 2017.2. If you are using an older version of Vivado, then you *MUST* use an older version
of this repository. Refer to the [list of commits](https://github.com/fpgadeveloper/fpga-drive-aximm-pcie/commits/master "list of commits")
to find links to the older versions of this repository.

In order to test this design on hardware, you will need the following:

* Vivado 2017.2
* [FPGA Drive](http://fpgadrive.com "FPGA Drive") - for connecting a PCIe SSD
* M.2 PCIe Solid State Drive
* One of the supported carriers listed above

## Supported carrier boards

* Zynq-7000 [PicoZed FMC Carrier Card V2](http://zedboard.org/product/picozed-fmc-carrier-card-v2 "PicoZed FMC Carrier Card V2") with [PicoZed 7015/30](http://picozed.org "PicoZed")
  * PCIe edge (use pzfmc-7z015-7z030-1v8-lpc.xdc)
  * LPC connector (use pzfmc-7z015-7z030-1v8.xdc)
* Artix-7 [AC701 Evaluation board](http://www.xilinx.com/products/boards-and-kits/ek-a7-ac701-g.html "AC701 Evaluation board")
  * PCIe edge (use ac701.xdc)
  * HPC connector (use ac701-hpc.xdc)
* Kintex-7 [KC705 Evaluation board](http://www.xilinx.com/products/boards-and-kits/ek-k7-kc705-g.html "KC705 Evaluation board")
  * PCIe edge (use kc705.xdc)
  * LPC connnector (use kc705-lpc.xdc)
  * HPC connnector (use kc705-hpc.xdc)
* Kintex Ultrascale [KCU105 Evaluation board](http://www.xilinx.com/products/boards-and-kits/kcu105.html "KCU105 Evaluation board")
  * LPC connnector (use kcu105-lpc.xdc)
  * HPC connnector (use kcu105-hpc.xdc)
* Virtex-7 [VC707 Evaluation board](http://www.xilinx.com/products/boards-and-kits/ek-v7-vc707-g.html "VC707 Evaluation board")
  * PCIe edge (use vc707.xdc)
  * HPC connector 1 (use vc707-hpc1.xdc)
  * HPC connector 2 (use vc707-hpc2.xdc)
* Virtex-7 [VC709 Evaluation board](http://www.xilinx.com/products/boards-and-kits/dk-v7-vc709-g.html "VC709 Evaluation board")
  * PCIe edge (use vc709.xdc)
  * HPC connector (use vc709-hpc.xdc)
* Zynq-7000 [ZC706 Evaluation board](http://www.xilinx.com/products/boards-and-kits/ek-z7-zc706-g.html "ZC706 Evaluation board")
  * PCIe edge (use zc706.xdc)
  * LPC connector (use zc706-lpc.xdc)
  * HPC connector (use zc706-hpc.xdc)

## Description

This project demonstrates using the AXI Memory Mapped to PCIe Bridge IP
to interface an FPGA with a PCIe end-point device. The bridge IP is configured
as a PCIe Root Port, using 1 to 4 lanes, Gen2 depending on target hardware.

The bare metal software application reports on the status of the PCIe link and 
performs enumeration of the detected PCIe end-points.

### Build instructions

To use the sources in this repository, please follow these steps:

1. Download the repo as a zip file and extract the files to a directory
   on your hard drive --OR-- Git users: clone the repo to your hard drive
2. Open Windows Explorer, browse to the repo files on your hard drive.
3. In the Vivado directory, you will find multiple batch files (*.bat).
   Double click on the batch file that is appropriate to your hardware,
   for example, double-click `build-zedboard.bat` if you are using the ZedBoard.
   This will generate a Vivado project for your hardware platform.
4. Run Vivado and open the project that was just created.
5. Click Generate bitstream.
6. When the bitstream is successfully generated, select `File->Export->Export Hardware`.
   In the window that opens, tick "Include bitstream" and "Local to project".
7. Return to Windows Explorer and browse to the SDK directory in the repo.
8. Double click the `build-sdk.bat` batch file. The batch file will run the
   `build-sdk.tcl` script and build the SDK workspace containing the hardware
   design and the software application.
9. Run Xilinx SDK (DO NOT use the Launch SDK option from Vivado) and select the workspace to be the SDK directory of the repo.
10. Select `Project->Build automatically`.
11. Connect and power up the hardware.
12. Open a Putty terminal to view the UART output.
13. In the SDK, select `Xilinx Tools->Program FPGA`.
14. Right-click on the application and select `Run As->Launch on Hardware (System Debugger)`

## Board Specific Notes

### VC709 and KCU105

These designs are based on the [AXI Bridge for PCI Express Gen3 Subsystem](http://www.xilinx.com/support/documentation/ip_documentation/axi_pcie3/v3_0/pg194-axi-bridge-pcie-gen3.pdf "AXI Bridge for PCI Express Gen3 Subsystem v3.0")
, for which Xilinx does not currently provide a driver. To generate an example stand-alone application for these boards,
the SDK build script makes a local copy of the driver for the [AXI Memory Mapped to PCIe Gen2 IP](https://www.xilinx.com/products/intellectual-property/axi_pcie.html "AXI Memory Mapped to PCIe Gen2 IP")
with a few small modifications to make it work with the Gen3 core. If you use or modify these applications, be aware
that they refer to the locally copied and modified driver located in `EmbeddedSw/XilinxProcessorIPLib/drivers`, and that
that driver is actually designed for the Gen2 core. In other words, you can expect the driver to work for the example
application that checks link-up, link speed/width and enumerates the end points, but anything else may fail due to
differences between the driver code and the Gen3 IP specs.

### PicoZed

#### Installation of PicoZed board definition files

To use this project on the PicoZed, you must first install the board definition files
for the PicoZed into your Vivado installation.

The following folders contain the board definition files and can be found in this project repository at this location:

https://github.com/fpgadeveloper/fpga-drive-aximm-pcie/tree/master/Vivado/boards/board_files

* `picozed_7015_fmc2`
* `picozed_7030_fmc2`

Copy those folders and their contents into the `C:\Xilinx\Vivado\2017.2\data\boards\board_files` folder (this may
be different on your machine, depending on your Vivado installation directory).

### PicoZed FMC Carrier Card V2

On this carrier, the GBTCLK0 of the LPC FMC connector is routed to a clock synthesizer/MUX, rather than being directly
connected to the Zynq. In order to use the FPGA Drive FMC on the [PicoZed FMC Carrier Card V2](http://zedboard.org/product/picozed-fmc-carrier-card-v2 "PicoZed FMC Carrier Card V2"), you will need to reprogram 
the configuration EEPROM for the clock synthesizer. See the [Hardware User Guide](http://zedboard.org/sites/default/files/documentations/PZCC-FMC-V2-UG_1.0.pdf "Hardware User Guide")
for the [PicoZed FMC Carrier Card V2](http://zedboard.org/product/picozed-fmc-carrier-card-v2 "PicoZed FMC Carrier Card V2") more information about this.

## Troubleshooting

Check the following if the project fails to build or generate a bitstream:

### 1. Are you using the correct version of Vivado for this version of the repository?
Check the version specified in the Requirements section of this readme file. Note that this project is regularly maintained to the latest
version of Vivado and you may have to refer to an earlier commit of this repo if you are using an older version of Vivado.

### 2. Did you follow the Build instructions in this readme file?
All the projects in the repo are built, synthesised and implemented to a bitstream before being committed, so if you follow the
instructions, there should not be any build issues.

### 3. Did you copy/clone the repo into a short directory structure?
Vivado doesn't cope well with long directory structures, so copy/clone the repo into a short directory structure such as
`C:\projects\`. When working in long directory structures, you can get errors relating to missing files, particularly files 
that are normally generated by Vivado (FIFOs, etc).

## License

Feel free to modify the code for your specific application.

## Fork and share

If you port this project to another hardware platform, please send me the
code or push it onto GitHub and send me the link so I can post it on my
website. The more people that benefit, the better.

### About us

This project was developed by [Opsero Inc.](http://opsero.com "Opsero Inc."),
a tight-knit team of FPGA experts delivering FPGA products and design services to start-ups and tech companies. 
Follow our blog, [FPGA Developer](http://www.fpgadeveloper.com "FPGA Developer"), for news, tutorials and
updates on the awesome projects we work on.