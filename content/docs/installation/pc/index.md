---
title: "在物理机上安装 OpenWRT"
linkTitle: "物理机"
weight: 40
date: 2021-08-26
description: >
  介绍 OpenWRT 在物理机上的安装
---

## 安装

### ~~方式一： u盘启动命令写入磁盘~~

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

实操时遇到一些问题：

- 我用最新的微pe，做成u盘启动，然后运行 physdiskwrite 报错，只能放弃
- 直接将需要写入的硬盘接到普通 win10 电脑上，然后运行 physdiskwrite 可以正常写入，也就是说 pe 不是必备
- 在磁盘写入之前，最好用 win10 的磁盘工具或者 diskgenius 之类的将目标磁盘的所有分区清空，否则会报错
- physdiskwrite ui如果遇到报错，先检查是不是磁盘没有清空，如果还报错，可以直接用命令行 cmd 执行命令 `physdiskwrite -u xxxx.img`，注意启动 cmd 时要用管理员权限
- 实测 diskgenius 的从镜像恢复硬盘的功能应该也可以实现类似的功能，但是我操作时写入中途失败，估计是我下载的镜像的格式不被 diskgenius 支持。（如下面扩容时报错所示）

具体操作参考：

- [如何安装软路由？ 篇三：软路由系统的安装-物理机OpenWRT](https://post.smzdm.com/p/ad2ok5ok/)

扩容问题

下载的 openwrt 镜像一般只给1g的空间，平时是够用的，但如果有用 docker 等软件时，容易空间不够。因此最好是在安装时就扩容。

比较推荐的做法是在写入镜像之后，第一次启动之前，通过硬盘分区工具无损的扩展分区，比如 diskgenius。

实操时遇到问题：网上找到的类似资料，openwrt 的分区格式一般时 ext4，这种格式 diskgenius 是支持无损扩容的。但我操作时，openwrt 的分区显示不是 ext4 格式，导致无法扩容。

> 暂时先搁置，后面有需要再看怎么解决。



