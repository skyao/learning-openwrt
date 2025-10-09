---
title: "设置OpenWRT的远程ssh登录"
linkTitle: "远程ssh"
weight: 20
date: 2021-08-26
description: >
  通过外网来远程ssh登录OpenWRT
---


### 容许远程SSH

打开 openwrt 的 系统 -》管理权 页面：

http://192.168.0.1/cgi-bin/luci/admin/system/admin

Dropbear 实例设置中，默认是没有勾选"网关端口"的。

选择勾选，保存&应用。

### 开启端口映射

由于电信屏蔽了 22 端口的访问，因此我们需要映射一个端口到 22 端口。网络 -》防火墙 -》端口转发：

http://192.168.0.1/cgi-bin/luci/admin/network/firewall/forwards

添加端口转发：

- 名称：openwrt-ssh
- 传输协议： tcp+udp
- 外部区域：wan
- 外部端口： 18822
- 内部区域：lan
- 内部 ip 地址： 192.168.0.1
- 内部端口：22

这样就可以在外网通过 18822 端口来远程ssh了。

### 配合 ddns

由于公网ip是每次拨号都不一样的，因此需要配置 ddns 一起使用，如：

```bash
ssh root@dev.skyao.net -p 18822
```



