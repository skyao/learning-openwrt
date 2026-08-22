---
title: "安装配置共享节点"
linkTitle: "共享节点"
weight: 20
date: 2028-08-20
description: >
   安装配置 easytier 的共享节点
---

我有一台腾讯云的服务器，有固定 IP 地址，因此可以用来安装配置 easytier 的共享节点以加速连接。

## 准备

### 开启防火墙

在防火墙中放行：

- TCP 11010
- UDP 11010
- TCP 11011
- TCP 11012

## 安装

参考官方文档： https://easytier.cn/guide/installation.html

一键安装脚本（仅 Linux）：

```bash
wget -O /tmp/easytier.sh "https://raw.githubusercontent.com/EasyTier/EasyTier/main/script/install.sh" && sudo bash /tmp/easytier.sh install --gh-proxy https://ghfast.top/
```

脚本执行成功后，EasyTier 的二进程程序会安装到 /opt/easytier 目录下，配置文件位于 /opt/easytier/config/default.conf。

```bash
$ easytier ls -lh
total 25M
drwxr-xr-x 2 root root 4.0K Aug 22 17:11 config
-rwxr-xr-x 1 root root 3.0M May 12 21:24 easytier-cli
-rwxr-xr-x 1 root root 7.3M May 12 21:26 easytier-core
-rwxr-xr-x 1 root root 6.8M May 12 21:26 easytier-web
-rwxr-xr-x 1 root root 7.4M May 12 21:18 easytier-web-embed
$ easytier pwd
/opt/easytier
```

并且已经自动安装了 systemd 服务，服务名有点怪，名为 easytier@default.service ：

```bash
sudo systemctl status easytier@default.service

● easytier@default.service - EasyTier Service
     Loaded: loaded (/etc/systemd/system/easytier@.service; enabled; preset: enabled)
     Active: active (running) since Sat 2026-08-22 23:32:32 CST; 10s ago
   Main PID: 653730 (easytier-core)
      Tasks: 14 (limit: 2297)
     Memory: 58.9M
        CPU: 648ms
     CGroup: /system.slice/system-easytier.slice/easytier@default.service
             └─653730 /opt/easytier/easytier-core -c /opt/easytier/config/default.conf

Aug 22 23:32:41 skyao-site easytier-core[653730]:                         ),
Aug 22 23:32:41 skyao-site easytier-core[653730]:                         response_code: NXDomain,
Aug 22 23:32:41 skyao-site easytier-core[653730]:                         trusted: true,
Aug 22 23:32:41 skyao-site easytier-core[653730]:                         authorities: None,
Aug 22 23:32:41 skyao-site easytier-core[653730]:                     },
Aug 22 23:32:41 skyao-site easytier-core[653730]:                 },
Aug 22 23:32:41 skyao-site easytier-core[653730]:             ),
Aug 22 23:32:41 skyao-site easytier-core[653730]:         },
Aug 22 23:32:41 skyao-site easytier-core[653730]:     },
Aug 22 23:32:41 skyao-site easytier-core[653730]: )
```

## 配置

默认配置为：

```properties
instance_name = "default"
dhcp = true
listeners = [
    "tcp://0.0.0.0:11010",
    "udp://0.0.0.0:11010",
    "wg://0.0.0.0:11011",
    "ws://0.0.0.0:11011/",
    "wss://0.0.0.0:11012/",
]
exit_nodes = []
rpc_portal = "0.0.0.0:0"

[[peer]]
uri = "tcp://public.easytier.top:11010"

[network_identity]
network_name = "default"
network_secret = "default"

[flags]
default_protocol = "udp"
dev_name = ""
enable_encryption = true
enable_ipv6 = true
mtu = 1380
latency_first = false
enable_exit_node = false
no_tun = false
use_smoltcp = false
foreign_network_whitelist = "*"
disable_p2p = false
p2p_only = false
relay_all_peer_rpc = false
disable_tcp_hole_punching = false
disable_udp_hole_punching = false
```

修改 network_name 和 network_secret

```properties

instance_name = "tencentcloudserver"
network_name = "skynet"
network_secret = "SuperStrongPass_ChangeMe_123456"

default_protocol = "tcp"
latency_first = true
```

修改后的配置为：

```properties
instance_name = "tencentcloudserver"
dhcp = true
listeners = [
    "tcp://0.0.0.0:11010",
    "udp://0.0.0.0:11010",
    "wg://0.0.0.0:11011",
    "ws://0.0.0.0:11011/",
    "wss://0.0.0.0:11012/",
]
exit_nodes = []
rpc_portal = "0.0.0.0:0"

[[peer]]
uri = "tcp://public.easytier.top:11010"

[network_identity]
network_name = "skynet"
network_secret = "SuperStrongPass_ChangeMe_123456"

[flags]
default_protocol = "tcp"
dev_name = ""
enable_encryption = true
enable_ipv6 = true
mtu = 1380
latency_first = true
enable_exit_node = false
no_tun = false
use_smoltcp = false
foreign_network_whitelist = "*"
disable_p2p = false
p2p_only = false
relay_all_peer_rpc = false
disable_tcp_hole_punching = false
disable_udp_hole_punching = false
```

重启：

```bash
sudo systemctl restart easytier@default.service
```
