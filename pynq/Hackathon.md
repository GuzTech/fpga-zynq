#Hackathon session with Viktorio and Oguz

## 2016-12-17
###Attendees:
Oguz Meteer, Viktorio El Hakim

###Goals
Decided on a project: **multicore RISCV system on PYNQ-Z1**.
First goal was to get the rocket-core (RISCV from UC Berkeley) on the PYNQ working. After that we want to get a simple "Hello World" like application running. Once this works, we want to then use the PicoRV32 core by Clifford Wolf (which is much smaller) to build a multicore system. Communication between the cores will most likely be the Nebula ring used in Starburst, but other options are still up for discussion.

###Results
* We started on the initial porting from Zybo to PYNQ. We did manage to generate a bitstream without problems, however we still need to check if the PS system configuration from the Zybo is also correctly applicable to the PYNQ. The file that holds the configuration is `fpga-zynq/pynq/src/tcl/pync_bd.tcl`.

## 2016-12-19
###Attendees:
Oguz Meteer, Viktorio El Hakim

###Goals
Continue the porting process, where the first goal is to get uboot compiling.

###Results
* We learned that `boot.bin` consists of the FSBL, the bitstream, and uboot in that order.
* We created the FSBL project, which compiled without errors.
* We added the pynq board to boards.cfg, which is used to find the right file to load the configuration of that specific board.
* We discussed methods for loading a native RISCV application from the ARM using the front-end server by UC Berkeley, and possible create our own. Also we could maybe use uboot for the RISCV to load it directly during the booting process.

## 2016-12-21
###Attendees:
Oguz Meteer

###Goals
Configure the PS using the PYNQ base project PS settings. When this works, we will build `boot.bin`, and create the DTS file.

###Results
* Got the PYNQ base image from Digilent, and configured the settings according to the base project PS settings. Among the modified settings were DDR timings and used peripherals.
* Replaced the old `pynd_bc.tcl` file with the newly exported block design.
* The PYNQ-Z1 board has UART connected to UART0, so in addition to changing this in the PS settings in Vivado, also changed it in `zynq_pynq.h`.
* In order to build `boot.bin` I had to use the `Create Boot Image` dialog box. However I couldn't get the dialog box to appear and there were no errors. In the end I found out that GTK3 is the cause of this (and of the ugly rendering of Eclipse), so I modified the `xsdk` script in `Xilinx/SDK/2016.2/bin` and added `-eclipseargs --launcher.GTK_version 2` after the call to `rdi_xsdk` on line 67. This fixed the ugly rendering and now the dialog box does appear. Note: this is because we are both using Arch Linux. I also tried it on Ubuntu 14.04, which is supported, and it worked out of the box.
* Built `boot.bin`.
* Built the DTS file using a renamed ZYBO DTS file. The only change is that the name `ps7_uart_1` is now `ps7_uart_0`, since the PYNQ board uses UART0. This probably will not be correct though, so we need to check this.
