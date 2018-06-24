---
layout: post
title: Linux无法启动，修复grub 
---

由于一些误操作，将grub配置错误，导致系统无法启动。可能会进入`grub rescue`模式，也可能显示 `Gave up waiting for root device.`等一些错误。出现这些情况，可以尝试重装grub来修复。

### 1. 从 Live CD 启动

随意挑选一个 Linux 发行版，将镜像使用 [unetbootin](https://unetbootin.github.io/) 烧到U盘，并从U盘启动。

### 2. 挂载磁盘

通过 `fdisk -l` 查看设备，回想起每个分区的挂载点。本次，在原来的系统中，`\` 对应的分区是 `\dev\sda5`, `\boot` 对应 `\dev\sda3` , `\home` 对应 `\dev\sda4`，分别将它们挂载。
```sh
mount /dev/sda5 /mnt
mount /dev/sda3 /boot
mount /dev/sda4 /home
```
挂载`\dev`、`\proc`、`\sys`  
```sh
mount --bind /dev /mnt/dev
mount --bind /proc /mnt/proc
mount --bind /sys /mnt/sys
```
切换根文件系统
```sh
chroot /mnt
```

### 3.修复 grub
```sh
grub-install /dev/sda
update-grub
```




