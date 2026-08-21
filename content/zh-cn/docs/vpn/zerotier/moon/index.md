---
title: "配置moon"
linkTitle: "Moon"
weight: 40
date: 2021-08-26
description: >
  为 zerotier 配置 moon
---

## 准备工作

为了加速 zerotier 的连接，避免直接连接国外服务器可能造成的不稳定。我使用我腾讯云上的轻量级服务器机器，安装 moon。

- ip： 159.75.84.176

为了保证网络放行，需要通过腾讯云控制台修改服务器的防火墙设置：添加规则，容许 UDP 9993 端口访问。

![](images/firewall.png)

```bash
nc -z -v -u 159.75.84.176 9993
Connection to 159.75.84.176 9993 port [udp/*] succeeded!
```

## 安装 zerotier

安装 ZeroTier：

```bash
curl -s https://install.zerotier.com | bash
```

停止服务，生成 Moon 配置：

```bash
systemctl stop zerotier-one
mkdir -p /var/lib/zerotier-moon
cd /var/lib/zerotier-one
# 删除旧的文件（如果有）
rm -f moon.json *.moon
# 基于官方公钥生成moon模板
zerotier-idtool initmoon identity.public > moon.json
```

修改 moon.json：

```bash
vi moon.json
```

找到 `"roots": []`，修改为自己机器的公网 IP：

```json
"roots": [
  {
   "identity": "7c134f5bbc:0:7d85e58b6f929608f406f1ff746487424ea9db0b8ef4cb1cf553c4dfe100e70eede501e2eed7fab84a94d0abf70ccf928ef65ee32bddcb0212d44c6c94559f38",
   "stableEndpoints": ["159.75.84.176/9993"]
  }
 ],
```

保存退出。再生成 moon 签名文件：

```bash
zerotier-idtool genmoon moon.json
wrote 0000007c134f5bbc.moon (signed world with timestamp 1785743655956)
```

当前目录生成文件类似：`000000xxxxxxxxxx.moon`。 

```bash
ls *.moon
0000007c134f5bbc.moon
```

将 moon 配置载入 zerotier 工作目录：

```bash
rm -r moons.d
mkdir moons.d
cp ./*.moon ./moons.d/
# 确认就一个 moon 文件
ls moons.d 
0000007c134f5bbc.moon
```

启动并开机自启 zerotier-one：

```bash
systemctl start zerotier-one
systemctl enable zerotier-one
```

获取 Moon 节点 ID（这是关键参数）：

```bash
zerotier-cli info
```

输出示例：

```bash
200 info 7cxxxxxx 1.16.2 ONLINE
```

`7cxxxxxxx` 就是 **MOON_ID**，记录下来备用。

### zerotier 客户端

在 openwrt 下，执行：

```bash
zerotier-cli orbit 7c134f5bbc 159.75.84.176
service zerotier restart
```

等待一段时间，

```bash
zerotier-cli peers 
```



7c134f5bbc



zerotier-cli orbit 7c134f5bbc 7c134f5bbc



[root@Kwrt:05:51 PM /etc/zerotier] # /etc/init.d/zerotier restart
[root@Kwrt:05:52 PM /etc/zerotier] # zerotier-cli orbit 7c134f5bbc 7c134f5bbc
200 orbit OK
[root@Kwrt:05:52 PM /etc/zerotier] # zerotier-cli peers

