---
title: "备份和还原OpenWRT设置"
linkTitle: "备份还原"
weight: 40
date: 2021-08-26
description: >
  备份OpenWRT设置，在出现问题时还原
---

http://192.168.0.1/cgi-bin/luci/admin/system/flashops

## 备份配置

备份配置文件，安全起见保存配置文件到百度盘之类的网盘。

## 恢复配置

在出现问题时，恢复配置

## 恢复出厂

由于使用的 openwrt 的固件安装包是基于 squashfs 格式的，因此支持恢复到出厂设置。

当出现严重问题时，可以通过 恢复到出厂设置 + 恢复配置来快速的恢复正常使用。
