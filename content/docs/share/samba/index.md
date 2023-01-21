---
title: "设置OpenWRT的samba共享"
linkTitle: "samba共享"
weight: 20
date: 2021-08-26
description: >
  通过 samba 进行网络共享
---

## 背景

openwrt 默认安装了以下 samba 的包：

- samba4-libs
- samba4-server
- autosamba
- luci-app-samba4
- luci-i18n-samba4-zh-cn

功能是预设提供的，因此只要配置好就行。

## 页面设置

在 openwrt 的 网络存储 -》网络共享页面进行设置：

http://192.168.0.1/cgi-bin/luci/admin/nas/samba4

共享设置如下：

| name     | 目录          |
| -------- | ------------- |
| movie3   | /mnt/movie3   |
| download | /mnt/download |
| shared   | /mnt/shared   |
| movie2   | /mnt/movie2   |
| movie    | /mnt/movie    |



## 参考资料

- [Openwrt下折腾 Samba4 配置](https://blog.csdn.net/kk380446/article/details/114213014)