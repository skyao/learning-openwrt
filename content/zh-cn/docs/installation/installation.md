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

https://www.right.com.cn/forum/thread-8267087-1-1.html

理由：

- 功能多
- 持续更新
- 自带 mlx4 驱动，支持我的 hp544+ 网卡

### 转换镜像文件

下载之后得到的 img 格式的镜像文件，不同安装方式需要转换为其他不同的镜像格式。


