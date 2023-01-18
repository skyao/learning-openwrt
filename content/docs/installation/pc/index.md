---
title: "在物理机上安装OpenWRT"
linkTitle: "物理机"
weight: 20
date: 2021-08-26
description: >
  介绍 OpenWRT 在PC物理机上的安装
---

## 安装

### 方式一： u盘启动命令写入磁盘

首先准备一个u盘，然后使用镜像写入工具（我习惯性使用 rufus ）将下载得到的 img 镜像写入到 u 盘中。

再使用这个制作好的 u 盘，修改主板bios为从 u 盘启动，之后启动电脑就可以运行 openwrt，但此时 openwrt 运行在 u 盘中。还需要将镜像文件写入到目标磁盘。

此时可以通过 openwrt 的页面上传或者 scp 命令将 img 镜像文件传到 openwrt 下。然后 ssh 登录 openwrt，执行 DD 命令，将 img 镜像写入到物理机磁盘。

dd 命令的示范如下：

```bash
dd if=/tmp/op.img of=/dev/sda
```

if 为输入文件，指向我们的 img 文件位置; of 为输出文件，选择物理硬盘。注意是整个磁盘，不是单个分区。具体磁盘分区情况可以通过 `fdisk -f` 命令查看。

> 备注：这个方式有点绕，其实最后还是要用工具将 img 镜像文件写入到物理磁盘，openwrt 在这个过程中只是作为一个 linux 系统使用。应该可以直接用 预先存放好 img 文件的 linux 启动盘 （比如最常见的 ubuntu server 安装盘）启动电脑，然后同样执行 DD 命令。

参考资料：

- https://www.geektech.top/dd-openwrt.html

### 方式二：直接将镜像写入到磁盘

需要准备的工具有：

- windows PE 启动盘
- OpenWRT的img映像文件
- physdiskwrite ：写镜像文件的工具

简单说就是在 windows 下 （可以是PE，也可以是普通windows）通过 physdiskwrite 软件将 img 文件直接写入到指定的目标磁盘。这个方案更简单直白。

具体操作参考：

- [如何安装软路由？ 篇三：软路由系统的安装-物理机OpenWRT](https://post.smzdm.com/p/ad2ok5ok/)

## 参考资料

