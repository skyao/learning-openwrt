---
title: "zerotier"
linkTitle: "zerotier"
weight: 20
date: 2021-08-26
description: >
  使用 zerotier
---

## 准备工作

### 注册帐号

我直接用 github 帐号登录。

### 创建网络

登录后提示创建一个新的网络

记录下这个网络的 ZeroTier Network ID。a0cbf4b62a273a30

## 客户端搭建

### openwrt

打开 openwrt 的 "vpn" -< "zerotier"：

http://192.168.0.1/cgi-bin/luci/admin/vpn/zerotier 

填写 ZeroTier Network ID，勾选 启动 和 自动允许客户端 NAT， 点 "保存&应用"

连接成功后会显示 “***Zerotier 运行中***”， "接口信息" 中也可以看到相关的信息。

在 zerotier 的管理页面上，这时可以看到有一个提示 "One device has joined this network." 在设置列表中找到这个设备，勾选前面的框让认证通过。

### mac

下载安装 zerotier 的 app，

### windows

