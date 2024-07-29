+++
title = "如何使用 mtk_uartboot 救砖 bpi-r3 mini"
date = "2024-07-29T15:54:48+08:00"

description = "如果你的 bpi-r3 mini nand 和 emmc 都不能启动， 教你如何使用 mtk_uartboot 救砖 bpi-r3 mini"

tags = []
+++

如果你的 bpi-r3 mini nand 和 emmc 都不能启动， 教你如何使用 mtk_uartboot 救砖 bpi-r3 mini

## 准备工作
1. r3mini 以及 type-c 线
2. chrome 或者 其他串口工具
3. U盘，格式为FAT，`rootfs.cpio.zst` 和 `bpi-r3-6.10.0-main.itb` 文件放入u盘根目录，插入r3mini
4. windows 电脑，linux 自己下载对应的  mtk_uartboot
5. `mtk_uartboot.exe`  `bpi-r3mini_ram_bl2.bin` `bpi-r3mini_emmc_fip.bin` 3个文件放到电脑
打开cmd命令行，并cd到3个文件的目录

## 文件来源
* `mtk_uartboot.exe` https://github.com/981213/mtk_uartboot/releases
* `bpi-r3mini_ram_bl2.bin` https://www.fw-web.de/dokuwiki/doku.php?id=en:bpi-r3mini:start
* `bpi-r3mini_emmc_fip.bin` https://github.com/frank-w/u-boot/releases
* `bpi-r3-6.10.0-main.itb` https://github.com/frank-w/BPI-Router-Linux/releases
* `rootfs.cpio.zst` https://forum.banana-pi.org/t/bpi-r3-mini-how-to-unbricked-nand-and-emmc/17843/25

`bpi-r3mini_ram_bl2.bin` 和 `rootfs.cpio.zst` 也可以在这里下载 https://github.com/kisy/blog/releases/tag/r3mini-uartboot

## 开始救砖

### 引导 ram uboot

1. type-c 线一头接 r3mini 一头接电脑，电脑为 r3mini 供电
2. 在打开的 cmd 里执行 mtk_uartboot，此时不能有其他地方占用串口
3. 等待出现 `NOTICE:  Received FIP` 后 chrome 打开 https://googlechromelabs.github.io/serial-terminal/
4. 点击页面中的 Connect，选择 r3mini 的串口
5. CONNECTED 后点击下面的命令交互区域，按回车键 出现 `BPI-R3M>` 表示进入 uboot 的 console 交互界面

mtk_uartboot 运行示例
```powershell
.\mtk_uartboot.exe -s COM4 -p .\bpi-r3mini_ram_bl2.bin --aarch64 -f .\bpi-r3mini_emmc_fip.bin
```

### 进入 ram linux

一行一行按照顺序执行下面命令

注意：`usb 0:4` 这个后面的 partition 编号不一定是 0:4 也可能是 0:1 等其他，自己试 ls usb 0:4 能显示文件列表的就是对的

```sh
usb start
ls usb 0:4
setenv initrd rootfs.cpio.zst
setenv fit bpi-r3-6.10.0-main.itb
setenv partition 0:4
setenv device usb
setenv setbootconf 'setenv bootconf "#conf-emmc-mini"'
run newboot
```

等出现下面的字符时表示已经启动 Linux 系统
```
Welcome to Buildroot
buildroot login:
```
输入 root 回车

出现 `#` 表示可以输入基本的 Linux 命令
使用 dd 等命令刷系统了

