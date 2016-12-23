#Hackathon session with Viktorio and Oguz

## 2016-12-17
###Attendees:
Oguz Meteer, Viktorio El Hakim

###Goals
Decided on a project: **multicore RISCV system on PYNQ-Z1**.
First goal is to get the rocket-core (RISCV from UC Berkeley) on the PYNQ working. After that we want to get a simple "Hello World" like application running. Once this works, we want to then use the PicoRV32 core by Clifford Wolf (which is much smaller) to build a multicore system. Communication between the cores will most likely be the Nebula ring used in Starburst, but other options are still up for discussion.

###Results
* We started on the initial porting from Zybo to PYNQ. We did manage to generate a bitstream without problems, however we still need to check if the PS system configuration from the Zybo is also correctly applicable to the PYNQ. The file that holds the configuration is `fpga-zynq/pynq/src/tcl/pync_bd.tcl`.

## 2016-12-19
###Attendees:
Oguz Meteer, Viktorio El Hakim

###Goals
Continue the porting process, where the first goal is to get U-Boot compiling.

###Results
* We learned that `BOOT.bin` consists of the FSBL, the bitstream, and U-Boot in that order.
* We created the FSBL project, which compiled without errors.
* We added the pynq board to boards.cfg, which is used to find the right file to load the configuration of that specific board.
* We discussed methods for loading a native RISCV application from the ARM using the front-end server by UC Berkeley, and possible create our own. Also we could maybe use U-Boot for the RISCV to load it directly during the booting process.

## 2016-12-20
###Attendees:
Oguz Meteer

###Goals
Configure the PS using the PYNQ base project PS settings. When this works, we will build `BOOT.bin`, and create the DTS file.

###Results
* Got the PYNQ base image from Digilent, and configured the settings according to the base project PS settings. Among the modified settings were DDR timings and used peripherals.
* Replaced the old `pynd_bc.tcl` file with the newly exported block design.
* The PYNQ-Z1 board has UART connected to UART0, so in addition to changing this in the PS settings in Vivado, also changed it in `zynq_pynq.h`.
* In order to build `BOOT.bin` I had to use the `Create Boot Image` dialog box. However I couldn't get the dialog box to appear and there were no errors. In the end I found out that GTK3 is the cause of this (and of the ugly rendering of Eclipse), so I modified the `xsdk` script in `Xilinx/SDK/2016.2/bin` and added `-eclipseargs --launcher.GTK_version 2` after the call to `rdi_xsdk` on line 67. This fixed the ugly rendering and now the dialog box does appear. Note: this is because we are both using Arch Linux. I also tried it on Ubuntu 14.04, which is supported, and it worked out of the box.
* Built `BOOT.bin`.
* Built the DTS file using a renamed ZYBO DTS file. The only change is that the name `ps7_uart_1` is now `ps7_uart_0`, since the PYNQ board uses UART0. This probably will not be correct though, so we need to check this.

## 2016-12-21
###Attendees:
Oguz Meteer

###Goals
Create a ramdisk and check if the PYNQ board can boot with the newly created files.

###Results
* Created a ramdisk by fetching is premade one.
* Copied `BOOT.bin`, `devicetree.dtb`, `uImage`, and `uramdisk.image.gz` to an SD card that already had the PYNQ Linux image stored on it. The original files were renamed and then the four mentioned files were copied.
* Managed to run U-Boot but does not start the Linux kernel. The output is:

```
U-Boot 2014.07-dirty (Dec 20 2016 - 17:20:30)

Board:  Xilinx Zynq
I2C:   ready
DRAM:  ECC disabled 256 MiB
MMC:   zynq_sdhci: 0
SF: Detected S25FL128S_64K with page size 256 Bytes, erase size 64 KiB, total 16 MiB
*** Warning - bad CRC, using default environment

In:    serial
Out:   serial
Err:   serial
Net:   Gem.e000b000
Warning: failed to set MAC address

Hit any key to stop autoboot:  0 
Device: zynq_sdhci
Manufacturer ID: 3
OEM: 5344
Name: SU08G 
Tran Speed: 50000000
Rd Block Len: 512
SD version 3.0
High Capacity: Yes
Capacity: 7.4 GiB
Bus Width: 4-bit
reading uEnv.txt
** Unable to read file uEnv.txt **
Copying Linux from SD to RAM...
reading uImage
3386736 bytes read in 390 ms (8.3 MiB/s)
reading devicetree.dtb
7490 bytes read in 17 ms (429.7 KiB/s)
reading uramdisk.image.gz
6136848 bytes read in 674 ms (8.7 MiB/s)
## Booting kernel from Legacy Image at 03000000 ...
   Image Name:   Linux-3.15.0-xilinx-06044-g6fd59
   Image Type:   ARM Linux Kernel Image (uncompressed)
   Data Size:    3386672 Bytes = 3.2 MiB
   Load Address: 00008000
   Entry Point:  00008000
   Verifying Checksum ... OK
## Loading init Ramdisk from Legacy Image at 02000000 ...
   Image Name:   
   Image Type:   ARM Linux RAMDisk Image (gzip compressed)
   Data Size:    6136784 Bytes = 5.9 MiB
   Load Address: 00000000
   Entry Point:  00000000
   Verifying Checksum ... OK
## Flattened Device Tree blob at 02a00000
   Booting using the fdt blob at 0x2a00000
   Loading Kernel Image ... OK
   Loading Ramdisk to 0ed6b000, end 0f3453d0 ... OK
   Loading Device Tree to 0ed66000, end 0ed6ad41 ... OK

Starting kernel ...
```

## 2016-12-21
###Attendees:
Oguz Meteer

###Goals
Solve the [issue](https://github.com/GuzTech/fpga-zynq/issues/1) of the Linux kernel not booting. The issue states several potential issues that could be the cause. After Linux boots, we want to execute a simple Hello World application using the front-end server on Linux on the ARM.

###Actions
Tried the obvious thing, which is to try the `devicetree.dts` file that was already on the SD card with the PYNQ Linux image. Since this worked, I tried to generate a DTS file myself, which also worked. Tried to execute a Hello World application on the RISCV on the FPGA.

###Results
* Got the kernel booting with the `devicetree.dts` file from the PYNQ Linux image, so our renamed Zybo device tree file was not usable with the PYNQ board.
* Generated a DTS file by following [this](http://www.wiki.xilinx.com/Build+Device+Tree+Blob). In the SDK, I generated another BSP project, selecting device tree as the project type, and the results were `skeleton.dtsi`, `system.dts`, and `zynq-7000.dtsi`. Renamed the dts file accordingly, and the DTB file was created without an issue, which solves the [issue](https://github.com/GuzTech/fpga-zynq/issues/1) that we wanted to solve.
* The front-end server hangs when trying to execute a Hello World application on the RISCV. This might be because:
 * The DTS file still has some incorrect settings.
 * The Vivado project is not configured correctly.
 * The bitstream is not loaded on the FPGA.

## 2016-12-23
###Attendees:
Oguz Meteer, Viktorio El Hakim

###Goals
* Figure out why during boot we get an htif error that corresponds with the HTIF hardware that loads our RISCV application into the instruction memory of our rocket core.
* Get Linux kernel 4.8.6 running on the PYNQ, and later run Arch Linux on it as well.

###Results
* We tested the Zybo files on the Zybo and it also gave us the same htif error during boot. The front-end server did work correctly however, so that error is not the issue it seems.
* We managed to build the kernel without issues, but we could not get the kernel to start.
* For both issues, it would be very useful if we could use JTAG and try to figure out where and why the front-end server hangs and why the kernel doesn't boot. For the next time, we want to look into how we might accomplish this. We could probably also use Chipscope for low-level stuff.