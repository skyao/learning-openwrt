---
title: "安装配置windows节点"
linkTitle: "windows节点"
weight: 30
date: 2028-08-20
description: >
   安装配置 easytier 的windows节点
---

## 安装

https://www.easytier.cn/guide/download.html

选择 Windows x86_64 图形界面程序 GUI, 正常安装。


## 组网

参考： https://www.easytier.cn/guide/gui/basic.html

点击新建网络，修改如下配置：

- Network Name
- Network Secret

Initial Nodes 里面选择 Add Initial Node，设置 tcp skyao.net，然后点 Run Network. 启动后 Peer Info 如下所示：

| Virtual IPv4    | Hostname   | Route Cost | Protocol | Latency | Upload  | Download | Loss Rate | NAT Type        | Version         |
| --------------- | ---------- | ---------- | -------- | ------- | ------- | -------- | --------- | --------------- | --------------- |
| 10.126.126.2/24 | skynas2    | Local      |          |         |         |          |           | Port Restricted | 2.6.4-8428a89d~ |
| 10.126.126.1/24 | skyao-site | p2p        | tcp      | 16ms    | 7.4 KiB | 1.4 KiB  | 0%        | Port Restricted | 2.6.4-8428a89d  |

测试：

```bash
$ ping 10.126.126.2

Pinging 10.126.126.2 with 32 bytes of data:
Reply from 10.126.126.2: bytes=32 time<1ms TTL=128
Reply from 10.126.126.2: bytes=32 time<1ms TTL=128
Reply from 10.126.126.2: bytes=32 time<1ms TTL=128
Reply from 10.126.126.2: bytes=32 time<1ms TTL=128

Ping statistics for 10.126.126.2:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms

$ ping 10.126.126.1

Pinging 10.126.126.1 with 32 bytes of data:
Reply from 10.126.126.1: bytes=32 time=14ms TTL=64
Reply from 10.126.126.1: bytes=32 time=14ms TTL=64
Reply from 10.126.126.1: bytes=32 time=15ms TTL=64
Reply from 10.126.126.1: bytes=32 time=14ms TTL=64

Ping statistics for 10.126.126.1:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 14ms, Maximum = 15ms, Average = 14ms
```

尝试 ssh 到 10.126.126.1，成功登录到腾讯云服务器。



## 常见错误

### 错误配置导致无限循环

通过 Swith Mode 切换到 Remote 模式，默认 tcp://127.0.0.1:15999 。

我在切换模式中选择了远程模式，填写地址之后发现无法连接。然后  easytier gui 不停的重试，无法中断。即使关闭之后再打开，也还是继续连接远程失败然后不停重试

解决的方法是，进入如下路径：

```bash
%LOCALAPPDATA%\
```

删除 com.kkrainbow.easytier 目录。然后重新打开 easytier gui，就可以重新开始配置。

