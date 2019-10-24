---
title: reverse notes
date: 2019-10-17
---

## NOR flash vs NAND flash

### 共性

* 闪存的最小寻址单位是字节(byte)，而不是磁盘上的扇区(sector)。这意味着我们可以从一块闪存的任意偏移(offset)读数据，但并不表明对闪存写操作也是以字节为单位进行的。我们会在下面的阐述中找到答案。

* 当一块闪存处在干净的状态时（被擦写过，但是还没有写操作发生），在这块flash上的每一位(bit)都是逻辑1。

* 闪存上的每一位(bit)可以被写操作置成逻辑0。 可是把逻辑 0 置成逻辑 1 却不能按位(bit)来操作，而只能按擦写块（erase block）为单位进行擦写操作。擦写块的大小从 4K 到128K 不等。从上层来看，擦写所完成的功能就是把擦写块内的每一位都重设置（reset）成逻辑 1。

* 闪存的使用寿命是有限的。具体来说，闪存的使用寿命是由擦写块的最大可擦写次数来决定的。超过了最大可擦写次数，这个擦写块就成为坏块(bad block)了。因此为了避免某个擦写块被过度擦写，以至于它先于其他的擦写块达到最大可擦写次数，我们应该在尽量小的影响性能的前提下，使擦写操作均匀的分布在每个擦写块上。这个过程叫做磨损平衡（wear leveling）。

### 不同之处

* NOR flash 读/写操作的基本单位是字节；而 NAND flash 又把擦写块分成页(page), 页是写操作的基本单位，一般一个页的大小是 512 或 2K 个字节。对于一个页的重复写操作次数是有限制的，不同厂商生产的 NAND flash 有不同的限制，有些是一次，有些是四次，六次或十次。

* 按照现在的技术水平，一般来说NOR flash擦写块的最大可擦写次数在十万次左右，NAND flash擦写块的最大可擦写次数在百万次左右。

* NOR flash is faster to read than NAND flash, but it's also more expensive, and it takes longer to erase and write new data. NAND has a higher storage capacity than NOR.

## 文件系统

### jffs2

https://www.ibm.com/developerworks/cn/linux/l-jffs2/

* JournallingFlashFileSystemVersion2
* （闪存日志型文件系统第2版）
* 功能:管理在MTD设备上实现的日志型文件系统。

### SquashFS
https://en.wikipedia.org/wiki/SquashFS
Squashfs is a compressed read-only file system for Linux.

## LZMA 压缩
https://en.wikipedia.org/wiki/Lempel%E2%80%93Ziv%E2%80%93Markov_chain_algorithm

## MTD

* Memory Technology Device
* 内存技术设备

## 联想智能摄像机 hacking

### 目标

敏感数据，如密码等存储
通信协议，远端APP连接等

在解压出的 `squashfs-root-0/fox/res` 目录下
通过提示音发现设备为“联想智能摄像机”，
具有baby ai功能

通过 `strings -n 20 GD*.BIN > strings.out` 命令
输出不小于20个可见字符的字符串
找到其中可能有价值的内容如下：

```
bootargs=console=ttyS1,115200n8 mem=96M@0x0 ispmem=8M@0x6000000 rmem=24M@0x6800000 init=/linuxrc rootfstype=squashfs root=/dev/mtdblock2 rw mtdparts=jz_sfc:256k(boot),2560k(kernel),2048k(root),-(appfs)
bootcmd=sdupdate; flashcrccheck;
ethaddr=00:11:22:33:44:55
serverip=193.169.4.2
gatewayip=193.169.4.1
netmask=255.255.255.0
```

`console=ttyS1,115200n8` 表示使用ttyS1（第二个串口），
设定波特率115200，no stop bits and 8 data bits.

启动的根文件系统是 `squashfs` ，根设备在mtdparts的第二个分区(mtdblock2)

```
binwalk -Me GD*.BIN > binwalk-output.txt
```

## firmadyne

* software-based full system emulation
* dynamic analysis
* large-scale
* automated

### 方针路线

* get firmware image
* unpack the contents
* identify the kernel
* extract the filesystem

### 挑战

* presence of various hardware-specific peripherals
* storage of persistent con-figuration in non-volatile memory (NVRAM)
* dynamically-generated configuration files

### 四个主要部件

1. 爬取固件
2. 提取固件文件系统
3. 开始模拟运行
4. 动态分析

#### 爬取固件

为每个网站手动写模板从一堆二进制内容中提取出固件

提供宏数据（如build date，release version，links to Management Information Base files for the SNMP）

对于动态页面，爬取制造商的FTP网站，代价是没有宏数据。

#### 提取固件文件系统

利用了 binwalk 提供的API提取内核和根文件系统

#### 开始模拟运行

当文件系统提取出来以后，识别硬件架构

之后启动与固件的硬件架构一样(指令集、字长、大小端)的QEMU模拟器。

通过拦截到文件系统、网络等相关内核子系统的系统调用来推断网络配置

#### 动态分析

分析的结果存在数据库中。

开发了3个脆弱点分析途径，帮助找到了之前没有发现过的14个脆弱点，这些脆弱点影响了69个固件。

### 在不同层面上的动态分析---为何模拟整个系统是最好的

#### 应用级别

例：提取出web页面，拿到外面的服务器(如Apache)上运行

问题：

许多web页面依赖于对server端脚步语言(PHP)的非标准扩展，以完成针专门对于硬件的功能，如NVRAM值。
比如，上百个受分析的固件中用到了自己的函数，如获取配置数据时用到了 `get_conf()` (PHP) 和 `nvram_get()` (ASP.NET)

除此之外，一些固件厂商自己定制了web服务器软件，直接将HTML内容嵌入了进去。

对应用数据的分析局限性很大，只能分析到类似PHP命令注入漏洞等问题，对于应用本身及系统组件束手无策。

#### 进程级别

在原始文件系统的环境下模拟单一的进程。在用户模式执行QEMU作为单一进程模拟器，chroot到原始文件系统，启动webserver等。

问题：

只是**部分**避免了前面提到的问题。

得不到一些特定硬件外设会导致因错误而终止。
比如：没有NVRAM时，应用想要访问 `/dev/nvram` 设备时会导致错误。

一些执行环境上的微小不同也可能对程序行为造成严重影响。
比如 `alphafs` web服务器在访问 NVRAM 前会验证依赖于硬件的产品和制造商ID，如果没有则终止并返回错误消息。为此，web服务器使用了 `mmap()` 系统调用来访问内存 `/dev/mem`，检查EEPROM芯片特定偏移处的 `ProductID` 和 `VendorID`

另外，由于主存储器写次数的限制，许多设备将易失性数据写在了内存文件系统中。每次开机动态生成这些文件系统。因此像 `/dev`和`/etc` 这类目录可能只是软链接到了内存文件系统中。
比如D-Link DIR-865L无线路由器通过启动脚本生成程序配置文件，这些程序中包括了 `lighttpd` web服务器，配置文件通过 `-c` 选项传给web服务器，因此即使原始的文件系统都在，`lighttpd` 运行也会失败。

#### 系统级别

系统级别的模拟能够克服前面提到的问题。
各种硬件外设接口都会有，优雅地模拟出固件的功能，动态生成的文件也会同在真实设备上一样生成。可以分析所有系统启动的进程。

这种方式能够成功模拟出测试集中96.6%的基于Linux的固件。

问题：

The lack of emulation support for out-of-tree kernel modules located on the filesystem.
Differences in kernel version may result in system instability.


