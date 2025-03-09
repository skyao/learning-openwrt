---
title: "连接 OpenWRT"
linkTitle: "连接"
weight: 50
date: 2021-08-26
description: >
  连接刚安装好的 OpenWRT
---


## 连接 OpenWRT

在 openwrt 的界面上，进入命令行，查看当前网络信息：

```bash
ip addr
```

找到 br-lan：

```bash
inet 192.168.1.1/24 brd 192.168.1.255 scope global dynamic br-lan
```

可以看到 br-lan 的 ip 地址为 192.168.1.1。

```bash
vi /etc/config/network
```

将这里的 br-lan 的 ip 地址修改为我们想要设置的 ip 地址，比如 192.168.3.1。

```bash
option ipaddr '192.168.3.1'
```

保存退出，重启网络：

```bash
/etc/init.d/network restart
```

用另外一台机器，网线接入到 openwrt 机器的 lan 口。访问

http://192.168.3.1/

页面，用默认账号 root, 密码 password 登录。






