---
title: "设置OpenWRT的远程管理"
linkTitle: "远程管理"
weight: 10
date: 2021-08-26
description: >
  通过外网来访问OpenWRT的web管理页面。
---



### 容许外部访问

打开 openwrt 的 系统 -》web 管理页面：

http://192.168.0.1/cgi-bin/luci/admin/system/webadmin

默认是勾选了"只允许内网访问"的。

取消勾选，保存&应用。

### 开启端口映射

由于电信屏蔽了 80 端口的访问，因此我们需要映射一个端口到 80 端口。网络 -》防火墙 -》端口转发：

http://192.168.0.1/cgi-bin/luci/admin/network/firewall/forwards

添加端口转发：

- 名称：openwrt-web
- 传输协议： tcp+udp
- 外部区域：wan
- 外部端口： 18888
- 内部区域：lan
- 内部 ip 地址： 192.168.0.1
- 内部端口：80

这样就可以在外网通过 18888 端口来访问了。

> 备注：不要使用 10080 这样的端口，会被主流的浏览器如 edge ，chrome,firefox 报错 ERR_UNSAFE_PORT ，换成 18888 这种就可以了。

### 配合 ddns

由于公网ip是每次拨号都不一样的，因此需要配置 ddns 一起使用，如：

http://dev.skyao.net:18888/

