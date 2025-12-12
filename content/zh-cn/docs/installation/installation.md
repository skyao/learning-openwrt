---
title: "OpenWRT的安装概述"
linkTitle: "概述"
weight: 1
date: 2021-08-26
description: >
  OpenWRT 支持多种方式的安装
---

OpenWRT 支持多种方式的安装：

- 虚拟机： 支持 esxi
- 物理机

## 准备工作

### 下载 openwrt

经过几次对比，目前我选择使用的是这个版本：

https://www.right.com.cn/forum/forum.php?mod=viewthread&tid=8433584&fromuid=559447

下载地址：

https://pan.baidu.com/s/1Y5i7fgaR0b_ieLCZgVVn2A?pwd=nnb7

选择的高大上版本（plus版），如类似 bleachwrt-plus-20251024-x86-64-generic-squashfs-combined-efi.img.gz。

理由：

- 功能多
- 持续更新

### 转换镜像文件

下载之后解压缩之后得到 img 格式的镜像文件，不同安装方式需要转换为其他不同的镜像格式。


