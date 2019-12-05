---
title: eclipse及gnu-mcu-eclipse的使用
date: 2019-12-04
---

# 安装过程

以下内容翻译自 https://gnu-mcu-eclipse.github.io/install/

## 安装 `xpm` 

假设已经安装有`npm`
(如果过慢需要翻墙)

```
$ sudo npm install --global xpm@latest
```

确定 `xpm` 已安装
```
$ which xpm
/usr/bin/xpm
```

## 安装工具链

由于多数平台默认不自带嵌入式系统(ARM或RISC-V)的GCC工具链，
故这一步是必须的

### ARM

[ARM平台详细安装说明](https://xpack.github.io/arm-none-eabi-gcc/install/)

```sh
xpm install --global @xpack-dev-tools/arm-none-eabi-gcc@latest
```

### RISC-V

[RISC-V平台详细安装说明](https://xpack.github.io/riscv-none-embed-gcc/install/)

```sh
xpm install --global @xpack-dev-tools/riscv-none-embed-gcc@latest
```

## 安装QEMU

```sh
xpm install --global @xpack-dev-tools/qemu-arm@latest
```

## 安装eclipse插件

[How to install the GNU MCU Eclipse plug-ins](https://gnu-mcu-eclipse.github.io/plugins/install/)

通过Eclipse应用市场安装

* Eclipse菜单-> Help -> Eclipse Marketplace
* 查找 GNU MCU Eclipse
* 安装

## 编译测试项目

```sh
git clone https://github.com/xpack-dev-tools/qemu-eclipse-test-projects
```

将 `arm-none-eabi-g++` 和 `arm-none-eabi-gcc` 的路径加入到 `PATH` 中

以 `NUCLEO-F103RB` 开发板为例，在 `Debug` 目录下运行

```sh
qemu-system-gnuarmeclipse -board NUCLEO-F103RB --image ncl-f103-blink.elf
```
