---
layout: post
title: "Boot xv6-riscv on Linux qemu"
author: "Changyi Li"
categories: sample
tags: [post,sample]
image: clash.png
---

# Boot xv6-riscv on Linux qemu

For reason of using xv6 for OS experiments, xv6 need to be installed. Thus this blog comes out.

## Hard Env

My env is in `VMWare`.Below is resource destributed.

Item   |Type
:----: |:----:
Host OS|Windows 11
VM OS |Ubuntu 22.04
alloc Processor   |8
alloc Mem   |4G
alloc HardDisk |150G


## Dev Env Praperation

- **Clone code && cd repo**
```sh
git clone https://github.com/mit-pdos/xv6-riscv.git
cd
```
- **Install packages**

```sh
sudo apt-get install build-essential pkg-config libboost-all-dev autoconf libtool libssl-dev flex bison ninja-build libglib2.0-dev    libpixman-1-dev libslirp-dev libncurses5-dev libncursesw5-dev 

```

```sh
sudo apt install binutils-riscv64-linux-gnu
sudo apt install gcc-riscv64-linux-gnu
sudo apt install gdb-multiarch
sudo apt install qemu-system-misc opensbi u-boot-qemu qemu-utils
```
> These are dev packages, if any unable to locate, change your download resource by, that open `Software&Updates` and change `Download from` to `Main server` or `UK->archive.ubuntu.com`. If still unable, may search for ways out.

>Now you can check your qemu version by input cmd in shell, that `qemu-system-misc -version`. If your version is 6.2, you might maybe probably perhaps or so need to reinstall another version of qemu. We'll discuss it when needed.

##  make qemu

Simply cd xv6 pwd, and input `make qemu` in terminal.

:warning: You may encounter problem below, stucking in qemu boot xv6.
That is the terminal stucking at `qemu-system-riscv64 -machine virt -bios none -kernel kernel/kernel -m 128M -smp 3 -nographic -global virtio-mmio.force-legacy=false -drive file=fs.img,if=none,format=raw,id=x0 -device virtio-blk-device,drive=x0,bus=virtio-mmio-bus.0`
![stuck qemu](/assets/img/3.7_qemu_stuck.png)
>Correct one should show `xv6 kernel is booting` `hart 2 starting` `hart 1 starting` `hart 3 starting` `init: starting sh`

Now we need to un&re install qemu-system-misc

- uninstall  
    `sudo apt-get remove qemu-system-misc `
- reinstall manually /(suggest in another dir) 
    ```
    wget https://download.qemu.org/qemu-7.2.16.tar.xz
 
    tar xvJf qemu-7.2.16.tar.xz
    
    cd qemu-7.2.16/
    
    ./configure --disable-kvm --disable-werror --prefix=/usr/local --target-list="riscv64-softmmu "
    
    make -j$(nproc)
    
    sudo make install
    
    export PATH=$PATH:/opt/qemu/bin
    
    qemu-system-riscv64 –version
    ```
    >If meet any pkgs missing when make qemu, just install that pkgs && re-make 

    >If lucky enough, you can check your version is 7.2.16. Then back to xv6 pwd, `make qemu` and you'll find
    ![suc](/assets/img/3.7_qemu_suc.png)


>For now,we've successfully init xv6, next part is for debug.

## debug on Vs code
- Setting on vscode
    - in `.vscode/launch.json`
    ```java
    {
        // Use IntelliSense to learn about possible attributes.
        // Hover to view descriptions of existing attributes.
        // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
        "version": "0.2.0",
        "configurations": [
            {
                "name":"xv6debug",
                "type": "cppdbg",
                "request": "launch",
                "program": "${workspaceFolder}/kernel/kernel",
                "stopAtEntry": true,
                "cwd": "${workspaceFolder}",
                "miDebuggerServerAddress": "127.0.0.1:26000",
                "miDebuggerPath": "/usr/bin/gdb-multiarch",
                "MIMode": "gdb",
                "preLaunchTask": "xv6build"
            }
        ]
    }

    ```
    - in `.vscode/tasks.json`

    ```java
    {
        "version": "2.0.0",
        "tasks": [
            {
                "label": "xv6build",
                "type": "shell",
                "isBackground": true,
                "command": "make qemu-gdb",
                "problemMatcher":[
                    {
                        "pattern":[
                            {
                                "regexp": ".",
                                "file": 1,
                                "location": 2,
                                "message": 3
                            }
                        ],
                        "background": {
                            "beginsPattern":"Now run 'gdb' in another window.",
                            "endsPattern":"."
                        }
                    }
                ]
            }
        ] 
    }
    ```
- Revise `.gbdinit`
    Add `@REM` to the front of the 3rd line && Save
- Initialize gdb in pwd of xv6

    ```
    make qemu-gdb
    ```  

Now back to VS code, press `F5` and you can debug