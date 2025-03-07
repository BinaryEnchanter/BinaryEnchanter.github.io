---
layout: post
title: "Boot Linux on Nexys A7 100T with Rocketchip"
author: "Changyi Li"
categories: sample
tags: [post,sample]
image: nexys.png
---

# Combination of RocketChip & Linux & Nexys-A7-100T(Failed)
This post refers to this [repository](https://github.com/eugene-tarassov/vivado-risc-v). Namely, a rehearsal of how to use the repository with regard to `Nexys A7 100T` in my env.
## Prerequisites
My env is in `VMWare`.Below is resource destributed.

Item   |Type
:----: |:----:
Host OS|Windows 11
VM OS |Ubuntu 22.04
Processor   |12*
Mem   |12G
HardDisk |200G**

*. one core per processor  
**. 100G for vivado2023.2, 20G for riscv-toolchain(seemingly unnecessary)
## Get the whole repository
``` sh
sudo apt install git make
git clone https://github.com/eugene-tarassov/vivado-risc-v.git
cd vivado-risc-v
make apt-install
make update-submodules
```
## Build FPGA bitstream
Owing to low resource on `Nexys A7 100T`, only one big RocketChip core is suitable. If you want to give multi-core a try, change `CONFIG=rocket64bn`, where `n` is core number. Or use your own config.
``` sh
source /path/to/Vivado/20xx.x/settings64.sh
make CONFIG=rocket64b1 BOARD=nexys-a7-100t bitstream
```  
`Note`:Don't why, it takes a long time to produce bitstream. Perhaps because in `./Makefile` it denotes only one thread to run bitstream. (You can find this in the makefile, it tells multi-thread leading error. Havn't check.)
## Boot Linux on qume
### Building Linux with qume
This would pre-initialize Linux, in convenience of saving time which initialization on board would take.
``` sh
./qemu/boot_qemu.sh
```
`Note`:On VMWare, hardware may not be utilized, causing stuck or crash on your virtual Ubuntu. To prevent this, configure the jobs in the `.sh` && `MakeFile`. (I tried revising the jobs to 4, but seemingly casuing issue which lead to run on single thread.)   
### Linux Login
After the commands executed, the Debian Linux would run on the shell. This procedure saves time for on-board initialization.

`Host name`: debian

`User login` and `password`: debian debian

`Root login` and `password`: root root

`Shut down Linux`: run `sudo poweroff`
## Work on SD
`Note`:Before all, you shall have SD card and a connector to link on your PC.  
This line would search for SD on your USB hosts, check if your desired SD.
``` sh
./mk-sd-card skip_mk_img
```
Then switch board boot mode to `SD` by connecting pins on board. And plug the SD card in the slot.

# Personal output (not necessary to look)
Originally, booting `Linux` on `zcu102` with `RocketChip` was my intention. Though never to know which comes first, tomorrow or fault? Just after bitstream generated for zcu102, the board quit working, even BIST not running after power on. Thus, I had to change the board to which I had other than zcu102. More disasterous, my SSD on the second slot failed to be detected by my laptop. F***!!!