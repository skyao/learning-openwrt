---
title: "在ESXi上安装OpenWRT"
linkTitle: "ESXi"
weight: 20
date: 2021-08-26
description: >
  介绍 OpenWRT 在ESXi上的安装
---

## 安装

### 准备镜像

下载下来的 img 格式的镜像文件需要先转为 exsi 的 vmdk 格式才能使用。

在 windows 上下载安装 starwindconverter.exe 软件，可以将类似 bleach-plus-20230113-openwrt-x86-64-generic-squashfs-combined-efi.img 的文件转为两个文件：

1. bleach-plus-20230113-openwrt-x86-64-generic-squashfs-combined-efi.vmdk
2. bleach-plus-20230113-openwrt-x86-64-generic-squashfs-combined-efi-flat.vmdk

注意这两个文件都需要使用，上传到 exsi 上时需要两个文件放在一起。如果文件需要改名，在先修改 img 文件的名字，再进行格式转换。

### 新建虚拟机

在 esxi 下新建虚拟机：

- 取名 openwrt，客户机操作系统选 linux，客户机操作系统版本选 其他 4.x linux （64位）
- cpu给两核
- 内存至少给1g：必须选择预先分配内存
- 硬盘先给16g(等下这个硬盘要删除的)
- 网卡只给两个：一个是用来做lan（通常也是 esxi 的管理网口）的，通常是虚拟网卡，默认是 vm network，然后添加 pci 设备，加入准备作为 wan 口使用的网卡，如果有其他网卡先不要加
- 用作wan口的网卡如果有多个网口，只在作为 wan 使用的网口中插入网线
- 虚拟机选项中，时间设置为 将主机时间同步到客户机中。引导选项中固件选择efi，取消启动uefi安全引导的勾选

确认创建虚拟机，这时打开仓储，通过数据存储浏览器可以看到在 datastore1 下增加了一个新目录（和虚拟机同名如openwrt），里面有和虚拟机同名的 vmdk 文件。通过数据存储浏览器将 准备好的 openwrt 的 vmdk 文件（注意是两个文件）上传到这个目录。

回到虚拟机设置，删除默认的这个硬盘，勾选删除数据。然后添加硬盘，选现有硬盘，再指向刚才上传的 openwrt 的 vmdk 文件。硬盘大小修改为 16g，硬盘模式修改为 “独立 - 持久”。这样就实现了用下载好的 openwrt 镜像文件作为 esxi 虚拟机的硬盘的目的。

在虚拟机上右键，自动启动，选择 启用。

再次确认：openwrt 虚拟机开机时，只插了两根网线，lan和wan。

## 配置 openwrt

### 修改 openwrt 的默认网段

在 esxi 的虚拟机控制台操作，`vi /etc/config/network`

将 lan 口的 ip地址从默认的 192.168.1.1 修改为 192.168.0.1

输入 `reboot` 重新启动 openwrt

### 设置 openwrt  的网络

重启 openwrt  之后，通过 http://192.168.0.1 打开 openwrt 的配置页面，默认账号为 root / password 。

打开 状态 -》 概述 应该可以看到有两个接口 eth0 和 eth1，显示为已经连接（注意前面的网卡和网线的要求）。

打开 网络 -》 接口 -》lan -》物理设置，应该可以看到 lan 默认是适用 eth0；打开 网络 -》 接口 -》wan-》物理设置，应该可以看到 wan默认是适用 eth1；

修改 wan 口的设置，比如开启 pppoe 拨号。

pppoe 设置：

- 高级设置中，取消“使用内置的 ipv6 管理”，获取 ipv6 地址修改为禁止 

- 账号：

应用之后验证wan连接OK可以上网之后，关闭openwrt。

### 增加其他网卡

关机状态下，修改配置，增加其他网卡。具体为增加pci设备，将需要增加的网卡一一加上。

启动 openwrt 就可以看到这些网卡了，修改 接口 -》 lan 将这些网卡作为 lan 加入进来。