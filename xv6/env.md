# 环境搭建

在这个教程中，我们将使用 [VirtualBox](https://www.virtualbox.org/) 在 [Ubuntu](https://cn.ubuntu.com/) 中安装Xv6的开发工具链，并使用 [QEMU](https://www.qemu.org/) 运行Xv6。

## 安装VirtualBox

进入 [VirtualBox下载](https://www.virtualbox.org/wiki/Downloads) 页面你所在的平台下载相应的包并安装。

## 安装Ubuntu

进入 [Ubuntu下载](https://cn.ubuntu.com/download/desktop) 下载系统光盘映像。

在VirtualBox中新建一个虚拟机并使用光盘映像安装Ubuntu系统。

## 安装扩展程序

安装扩展程序`VBoxGuestAdditions`以使用修改分辨率、共享文件夹和共享剪切板等扩展功能。

进入 [下载索引](http://download.virtualbox.org/virtualbox) ，根据VirtualBox的版本进入相应页面下载扩展程序的光盘映像`VBoxGuestAdditions_x.x.x.iso`，然后在VirtualBox中把它添加到虚拟机。

为了能够编译并安装扩展程序，需要在虚拟机中安装 [gcc](https://gcc.gnu.org/) 和 [make](http://www.gnu.org/software/make/) ：

`sudo apt-get install gcc make`

打开虚拟机任务栏中光盘映像，点击右上角的`Run Software`安装扩展程序。安装完成后重启虚拟机。

使用`视图->虚拟显示屏`可以修改虚拟机分辨率。

使用`设备->共享剪切板`可以启用共享剪切板。

启用共享文件夹要复杂一点。

首先在VirtualBox中指定宿主机的共享文件夹路径。

然后在虚拟机中创建共享文件夹

`sudo mkdir /mnt/share`

最后挂载共享文件夹

`sudo mount -t vboxsf VirtualBox_Share /mnt/share`

这里的VirtualBox_Share是VirtualBox中宿主机的共享文件夹名。

## 安装工具链

安装编译Xv6工具链需要的依赖：

`sudo apt-get install autoconf automake autotools-dev curl libmpc-dev libmpfr-dev libgmp-dev gawk build-essential bison flex texinfo gperf libtool patchutils bc zlib1g-dev libexpat-dev`

下载Xv6工具链源码：

`git clone --recursive https://github.com/riscv/riscv-gnu-toolchain`

生成Makefile：

`cd riscv-gnu-toolchain`

`./configure --prefix=/usr/local`

编译工具链：

`sudo make`

检查是否成功安装：

```
$ riscv64-unknown-elf-gcc --version
riscv64-unknown-elf-gcc (GCC) 10.1.0
Copyright (C) 2020 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```

## 安装QEMU

安装编译QEMU需要的依赖

`sudo apt-get install libglib2.0-dev libpixman-1-dev`

下载QEMU：

`wget https://download.qemu.org/qemu-4.1.0.tar.xz`

解压文件：

`tar xvJf qemu-4.1.0.tar.xz`

生成Makefile：

`cd qemu-4.1.0`

`./configure --disable-kvm --disable-werror --prefix=/usr/local --target-list="riscv64-softmmu"`

编译QEMU：

`make`

安装QEMU：

`sudo make install`

检查是否成功安装：


```
$ qemu-system-riscv64 --version
QEMU emulator version 4.1.0
Copyright (c) 2003-2019 Fabrice Bellard and the QEMU Project developers
```

## 运行xv6

下载Xv6源码：

`git clone https://github.com/mit-pdos/Xv6-fall19.git`

编译运行Xv6：

`cd xv6-fall19`

`make qemu`

```
xv6 kernel is booting

virtio disk init 0
init: starting sh
$ 
```

要退出Xv6，先按下`ctrl+a`，然后按下`x`。

## 安装开发工具

获取Microsoft GPG key：

`wget -q https://packages.microsoft.com/keys/microsoft.asc -O- | sudo apt-key add -`

启用edge仓库：

`sudo add-apt-repository "deb [arch=amd64] https://packages.microsoft.com/repos/edge stable main"`

安装edge

`sudo apt install microsoft-edge-dev`

启用vscode仓库：

`sudo add-apt-repository "deb [arch=amd64] https://packages.microsoft.com/repos/vscode stable main"`

安装vscode：

`sudo apt install code`

- [6.S081 / Fall 2019](https://pdos.csail.mit.edu/6.828/2019/tools.html)