#Hackathon session with Viktorio and Oguz

## 2016-12-17
Decided on a project: **multicore RISCV system on PYNQ-Z1**.
First goal was to get the rocket-core (RISCV from UC Berkeley) on the PYNQ working. After that we want to get a simple "Hello World" like application running. Once this works, we want to then use the PicoRV32 core by Clifford Wolf (which is much smaller) to build a multicore system. Communication between the cores will most likely be the Nebula ring used in Starburst, but other options are still up for discussion.

###Results
* We started on the initial porting from Zybo to PYNQ. We did manage to generate a bitstream without problems, however we still need to check if the PS system configuration from the Zybo is also correctly applicable to the PYNQ. The file that holds the configuration is `fpga-zynq/pynq/src/tcl/pync_bd.tcl`.

## 2016-12-19
We continued the porting process, where the first goal was to get uboot compiling. We learned that `boot.bin` consists of the FSBL, the bitstream, and uboot in that order. We discussed methods for loading a native RISCV application from the ARM using the front-end server by UC Berkeley, and possible create our own. Also we could maybe use uboot for the RISCV to load it directly during the booting process.

###Results
* We created the FSBL project, which compiled without errors.
* We added the pynq board to boards.cfg, which is used to find the right file to load the configuration of that specific board.

