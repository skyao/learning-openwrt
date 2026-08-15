---
title: "配置"
linkTitle: "配置"
weight: 30
date: 2025-11-20
description: >
  配置 Cudy TR3000 openwrt
---

备注：以下配置以 24.xx 版本为例，也就是 kwrt-11.10.2025-mediatek-filogic-cudy_tr3000-mod-squashfs-sysupgrade.bin 这个固件。

## 连接

插入网线接到cudy tr3000的lan口，设置 dhcp，正常能获取 10.0.0.x 的 ip 地址。

浏览器打开

http://10.0.0.1

用默认的帐号 root/root 登录。

打开 系统 -> 管理权，修改密码，并添加 SSH 密钥。

wan 口先接入其他路由器，方便配置时进行各种下载。

打开 网络 -> 接口 , 默认 wan 使用 dhcp 正常已经连接网络了。

删除 wan6, 编辑 lan，将 lan 的 ip 地址修改为 192.168.5.1/24 。

保存后打开 192.168.5.1 重新登录。

## 更新

打开 istore -> 维护， 更新 istore。

## usb 网络

可以通过 opkg 命令执行安装：

```bash
opkg update
opkg install kmod-usb-net-rndis usbutils
```

也可以用软件包页面安装。

拔掉网线，usb连接手机，选择usb网络共享。

openwrt 页面进入 网络 -> 网口配置

eth0 显示“已断开”，这个是网线。

多了一个 usb0, 当前还是显示为 “已断开”，在 wan 这一行勾选 usb0， eth0 的勾选会自动取消。

点击 “保存并应用” 。再刷新页面，usb0 显示为绿色已经连接。

## zerotier

### 安装 zerotier

在页面登录之后，进入 系统 -》 软件包。

搜索 zerotier，找到 luci-app-zerotier 之后点击安装，会自动安装 luci-app-zerotier 和 zerotier。zerotier 的版本为 1.16.0-r1。

### 设置 zerotier 插件

openwrt 下打开 vpn -> zerotier, 

Global configuration：

- 启用: 勾选
- Allow input traffic: 勾选
- 本地配置路径：不要指定
- 配置路径：不要指定

Network configuration, 配一行就可以了：

- NetworkId: 填写自己的
- 允许管理IP/路由: 勾选
- 允许全局IP/路由
- 许默认路由
- 允许 DNS
- 允许入站: 勾选
- 允许转发: 勾选
- IP 动态伪装: 勾选

### 配置 zerotier 服务

登录 https://my.zerotier.com/ ，打开自己的 network， 刷新  Members 成员列表，能看到有一个新的成员出现，默认是没有通过认证的。

- 勾选 Authorized
- name：cudy
- description： cudy-ax3000路由器
- ip地址设置为 192.168.192.50 

再刷新列表，就能看到这个新的成员分配到新的地址，而且显示 auth 通过。

打开 网络 -> 防火墙 -> 常规设置：

- 入站数据： 接受
- 出站数据： 接受
- 转发： 接受

ssh 登录到 openwrt，默认密码 root/root。

```bash
$ ip addr

10: ztw4lflcku: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 2800 qdisc fq_codel state UNKNOWN group default qlen 1000
    link/ether 96:4e:39:22:37:d1 brd ff:ff:ff:ff:ff:ff
    inet 192.168.192.50/24 brd 192.168.192.255 scope global ztw4lflcku
       valid_lft forever preferred_lft forever
```

验证一下。

分别在 cudy  和一台连接 cudy 的机器上执行：

```bash
ping 192.168.192.20
ping 192.168.192.30
ping 192.168.192.40
ping 192.168.192.50

ping 192.168.2.1
ping 192.168.3.1
ping 192.168.4.1
ping 192.168.5.1
```

正常此时应该可以 ping 通的.

### 故障排除：路由无法转发

有时会遇到这个故障，体现为：

1. openwrt 路由器可以正常上网，正常连接 zerotier 网络，也能 ping zerotier 网络中的其他主机
2. 连接到 openwrt 路由器的机器，可以上网，但是无法使用 zerotier 网络，ping zerotier 网络中的其他主机报错：
   
   ```bash
   ping 192.168.192.100
   PING 192.168.192.100 (192.168.192.100) 56(84) bytes of data.
   From 192.168.5.1 icmp_seq=1 Destination Port Unreachable
   From 192.168.5.1 icmp_seq=2 Destination Port Unreachable
   From 192.168.5.1 icmp_seq=3 Destination Port Unreachable
   ```
参考豆包： https://www.doubao.com/chat/38437751886886914

解决方案：

```bash
# 将zt接口放入firewall的lan区域：具体名字通过 ip addr 获取
# 将设备 ztw4lflcku 加入lan zone的device列表
uci add_list firewall.@zone[0].device='ztw4lflcku'
uci commit firewall
/etc/init.d/firewall restart
```

## openclash

### 安装

由于安装的是最新的 25.x 版本的 kwrt，内核 6.12,所以 istore 中没有合适的 openclash 版本可以直接安装。

从这里下载类似 luci-app-openclash_0.47.133_all.ipk 的文件：

https://github.com/vernesong/OpenClash/releases

最简单的安装方式是通过 istore 的手动安装功能，通过页面上传并安装。

### 配置

打开 服务 -> openclash， 提示没有下载内核，选择下载。

但下载非常慢，很容易失败，从运行日志中可以看到：

```bash
2025-11-22 17:15:39 【/tmp/clash_meta.tar.gz】下载失败：【curl: (28) Operation timed out after 60000 milliseconds with 768899 out of 7198810 bytes received】
2025-11-22 17:15:21 【/tmp/openclash_last_version】下载失败：【curl: (28) Operation too slow. Less than 1 bytes/sec transferred the last 30 seconds】
2025-11-22 17:13:33 下载进度：【clash_meta.tar.gz - 12%】
2025-11-22 17:13:28 下载进度：【clash_meta.tar.gz - 11%】
2025-11-22 17:13:23 下载进度：【clash_meta.tar.gz - 10%】
2025-11-22 17:13:17 【/tmp/openclash_last_version】下载失败：【curl: (28) Operation too slow. Less than 1 bytes/sec transferred the last 30 seconds】
```

打开 openclash 的 插件设置 -> 版本更新，找到 meta 内核的"下载最新版本内核", 点下载。

备注：最近这个路径报错 not found。需要到 https://github.com/vernesong/mihomo-oix/releases/ 这里，找到类似 mihomo-linux-arm64-alpha-oix-4d6fda7.gz 的下载下来。


找到下载下来的 clash-linux-arm64.tar.gz，解压得到 clash 文件，然后上传到 clash 目录下

```bash
scp ./clash root@192.168.5.1:/etc/openclash/core/clash_meta

# 登录上去 openwrt，增加执行权限
chmod +x /etc/openclash/core/clash_meta
```

刷新 openclash 页面，可以看到内核已经更新成功。

在配置订阅，配置管理依次操作，设置好配置文件，然后启动 openclash。可以查看运行日志，看是否遇到问题。

成功启动后，打开 覆盖设置 -> 设置 SOCKS5/HTTP(S) 认证信息, 取消勾选, 不开启代理的认证。

### fake ip 造成的 ssh 和 ping 失败

如果 openclash 配置了 fake ip，可能会造成 ssh 和 ping 失败。体现为：

```bash
$ ping skyao.net

PING skyao.net (198.18.0.20) 56(84) bytes of data.
From pw.lan (192.168.5.1) icmp_seq=1 Destination Port Unreachable
From pw.lan (192.168.5.1) icmp_seq=2 Destination Port Unreachable

$ nslookup ecs3.skyao.net 
Server:		8.8.8.8
Address:	8.8.8.8#53

Name:	ecs3.skyao.net
Address: 198.18.0.17

$ ssh sky@ecs3.skyao.net
Connection closed by 198.18.0.13 port 22
```

简单说，就是 openclash 的 fake ip 机制生效了，dns 解析为 fake ip 如 198.18 号段，但 ping 和 ssh 时，请求没有被 openclash 劫持并反查 fack ip，而是真的去请求 198.18.0.17 这个 ip 地址，当然会失败。

没有找到特别好的解决办法，只能暂时关闭部分域名解析的 fake ip，让这部分域名的 dns 解析使用真实的 ip 地址，而不是 fake ip。暂时规避这个问题。

解决方案：

- 打开 openclash 的配置，“覆写配置” -> "DNS设置"，勾选 "Fake-IP-Filter"，Fake-IP-Filter-Mode 选择 "黑名单模式"，输入要屏蔽的域名，比如 skyao.net，然后保存。

目前这个问题暂时只在 cudy tr3000 上遇到（也许和 qwrt 有关？）。

## 精简

虽然默认的固件已经很精简了，但有些软件包还是占用了一些空间，可以精简一下。

卸载 Passwall：

```bash
$ opkg list-installed | grep passwall
luci-app-passwall - 25.11.1-r1

$ opkg remove luci-app-passwall
Removing package luci-app-passwall from root...
Not deleting modified conffile /usr/share/passwall/rules/proxy_host.

$ rm /usr/share/passwall/rules/proxy_host
```

### 刷新页面

插件已经卸载，但 LuCI（OpenWrt 的 Web 界面）缓存里还保留了旧的菜单链接，点击时会报错。

执行以下命令删除缓存文件：

```bash
rm -f /tmp/luci-indexcache
rm -f /tmp/luci-modulecache/*
```

重启路由器。

## 其他

设置无线，mac地址过滤。

## 总结

刷新到了最新版本的 openwrt 固件，然后安装了 zerotier，openclash 两个插件，支持 usb 网络连接手机。

基本够用了。