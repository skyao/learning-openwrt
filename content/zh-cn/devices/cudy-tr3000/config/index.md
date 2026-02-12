---
title: "配置"
linkTitle: "配置"
weight: 30
date: 2025-11-20
description: >
  配置 Cudy TR3000 openwrt
---

## 连接

插入网线接到cudy tr3000的lan口，设置 dhcp，正常能获取 10.0.0.x 的 ip 地址。

浏览器打开

http://10.0.0.1

用默认的帐号 root/root 登录。

打开 系统 -> 管理权，修改密码。

wan 口先接入其他路由器，方便配置时进行各种下载。

打开 网络 -> 接口 , 默认 wan 使用 dhcp 正常已经连接网络了。

删除 wan6, 编辑 lan，将 lan 的 ip 地址修改为 192.168.5.1 。

保存后打开 192.168.5.1 重新登录。

## 更新

打开 istore -> 维护， 更新 istore。



## usb 网络

```bash
opkg update
opkg install kmod-usb-net-rndis usbutils
```

安装过程：

```bash
opkg install kmod-usb-net-rndis usbutils
Downloading https://dl.openwrt.ai/releases/24.10/targets/mediatek/filogic/6.6.116/Packages.gz
Updated list of available packages in /var/opkg-lists/kwrt_core
Downloading https://dl.openwrt.ai/releases/24.10/packages/aarch64_cortex-a53/base/Packages.gz
Updated list of available packages in /var/opkg-lists/kwrt_base
Downloading https://dl.openwrt.ai/releases/24.10/packages/aarch64_cortex-a53/packages/Packages.gz
Updated list of available packages in /var/opkg-lists/kwrt_packages
Downloading https://dl.openwrt.ai/releases/24.10/packages/aarch64_cortex-a53/luci/Packages.gz
Updated list of available packages in /var/opkg-lists/kwrt_luci
Downloading https://dl.openwrt.ai/releases/24.10/packages/aarch64_cortex-a53/routing/Packages.gz
Updated list of available packages in /var/opkg-lists/kwrt_routing
Downloading https://dl.openwrt.ai/releases/24.10/packages/aarch64_cortex-a53/kiddin9/Packages.gz
Updated list of available packages in /var/opkg-lists/kwrt_kiddin9
Installing kmod-usb-net-rndis (6.6.116-r1) to root...
Downloading https://dl.openwrt.ai/releases/24.10/targets/mediatek/filogic/6.6.116/kmod-usb-net-rndis_6.6.116-r1_aarch64_cortex-a53.ipk
Installing kmod-mii (6.6.116-r1) to root...
Downloading https://dl.openwrt.ai/releases/24.10/targets/mediatek/filogic/6.6.116/kmod-mii_6.6.116-r1_aarch64_cortex-a53.ipk
Installing kmod-usb-net (6.6.116-r1) to root...
Downloading https://dl.openwrt.ai/releases/24.10/targets/mediatek/filogic/6.6.116/kmod-usb-net_6.6.116-r1_aarch64_cortex-a53.ipk
Installing kmod-usb-net-cdc-ether (6.6.116-r1) to root...
Downloading https://dl.openwrt.ai/releases/24.10/targets/mediatek/filogic/6.6.116/kmod-usb-net-cdc-ether_6.6.116-r1_aarch64_cortex-a53.ipk
Installing usbutils (017-r1) to root...
Downloading https://dl.openwrt.ai/releases/24.10/packages/aarch64_cortex-a53/packages/usbutils_017-r1_aarch64_cortex-a53.ipk
Installing libusb-1.0-0 (1.0.27-r1) to root...
Downloading https://dl.openwrt.ai/releases/24.10/packages/aarch64_cortex-a53/base/libusb-1.0-0_1.0.27-r1_aarch64_cortex-a53.ipk
Installing libevdev (1.13.1-r1) to root...
Downloading https://dl.openwrt.ai/releases/24.10/packages/aarch64_cortex-a53/packages/libevdev_1.13.1-r1_aarch64_cortex-a53.ipk
Installing libudev-zero (1.0.3-r1) to root...
Downloading https://dl.openwrt.ai/releases/24.10/packages/aarch64_cortex-a53/packages/libudev-zero_1.0.3-r1_aarch64_cortex-a53.ipk
Installing usbids (0.387-r1) to root...
Downloading https://dl.openwrt.ai/releases/24.10/packages/aarch64_cortex-a53/packages/usbids_0.387-r1_aarch64_cortex-a53.ipk
Configuring libevdev.
Configuring libusb-1.0-0.
Configuring kmod-mii.
Configuring libudev-zero.
Configuring usbids.
Configuring usbutils.
Configuring kmod-usb-net.
Configuring kmod-usb-net-cdc-ether.
Configuring kmod-usb-net-rndis.
```

拔掉网线，usb连接手机，选择usb网络共享。

openwrt 页面进入 网络 -> 网口配置

eth0 显示“已断开”，这个是网线。

多了一个 usb0, 当前还是显示为 “已断开”，在 wan 这一行勾选 usb0， eth0 的勾选会自动取消。

点击 “保存并应用” 。再刷新页面，usb0 显示为绿色已经连接。

## zerotier

### 安装 zerotier

在页面登录之后，进入 istore 页面，进入 istore ，选择更新 istore。

搜索 zerotier，找到 zerotier 之后点击安装：

```bash
istore (完成于： 11/22/2025, 4:50:20 PM) > is-opkg install 'app-meta-zerotier'

Try mirror server https://repo.istoreos.com/repo
Installing app-meta-zerotier (1.3.0-1) to root...
Downloading https://repo.istoreos.com/repo/all/meta/app-meta-zerotier_1.3.0-1_all.ipk
Installing libminiupnpc (2.2.8-r1) to root...
Downloading https://dl.openwrt.ai/releases/24.10/packages/aarch64_cortex-a53/packages/libminiupnpc_2.2.8-r1_aarch64_cortex-a53.ipk
Installing libnatpmp1 (20230423-r1) to root...
Downloading https://dl.openwrt.ai/releases/24.10/packages/aarch64_cortex-a53/packages/libnatpmp1_20230423-r1_aarch64_cortex-a53.ipk
Installing zerotier (1.14.1-r3) to root...
Downloading https://dl.openwrt.ai/releases/24.10/packages/aarch64_cortex-a53/packages/zerotier_1.14.1-r3_aarch64_cortex-a53.ipk
Installing luci-app-zerotier (26.218.43438~65032d3) to root...
Downloading https://dl.openwrt.ai/releases/24.10/packages/aarch64_cortex-a53/kiddin9/luci-app-zerotier_26.218.43438~65032d3_all.ipk
Installing luci-i18n-zerotier-zh-cn (git-25.156.35898-bfd1fcd) to root...
Downloading https://repo.istoreos.com/repo/all/nas_luci/luci-i18n-zerotier-zh-cn_git-25.156.35898-bfd1fcd_all.ipk
Configuring libminiupnpc.
Configuring libnatpmp1.
Configuring zerotier.
disabled in /etc/config/zerotier
Configuring luci-app-zerotier.
Configuring luci-i18n-zerotier-zh-cn.
Configuring app-meta-zerotier.
Try mirror server https://repo.istoreos.com/repo
Package zerotier (1.14.1-r3) installed in root is up to date.
Package luci-app-zerotier (26.218.43438~65032d3) installed in root is up to date.
Package luci-i18n-zerotier-zh-cn (git-25.156.35898-bfd1fcd) installed in root is up to date.
```

注意不要直接安装 zerotier 或者 luci-app-zerotier

```bash
# 这个会安装 zerotier，但是没有界面
opkg install  zerotier
# 这个会安装 luci 界面，但有些 openwrt 比如我的 cudy tr3000 用的 kwrt 也会没有页面现实。
opkg install luci-app-zerotier
```

也不要在 系统 -》软件包内安装 openwrt 或者 luci-app-zerotier，问题都一样，没有页面。

### 设置 zerotier 插件

openwrt 下打开 vpn -> zerotier, 

Global configuration：

- 启用: 勾选
- Allow input traffic: 勾选

Network configuration, 配一行就可以了：

- NetworkId
- Allow managed IP/route: 勾选
- Allow global IP/route: 勾选
- Allow default route: 勾选
- Allow input: 勾选
- Allow forward: 勾选
- Forward interfaces： 不选，默认就是all （Leave empty for all.）

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

## openclash

### 安装

进入 系统 -> 软件包，先更新列表，再在过滤器中输入 openclash，显示 luci-app-openclash，点击安装。

```bash
opkg --force-overwrite install luci-app-openclash

Installing luci-app-openclash (0.47.028-r30) to root...
Downloading https://dl.openwrt.ai/releases/24.10/packages/aarch64_cortex-a53/kiddin9/luci-app-openclash_0.47.028-r30_all.ipk
Installing libruby3.3 (3.3.9-r1) to root...
Downloading https://dl.openwrt.ai/releases/24.10/packages/aarch64_cortex-a53/packages/libruby3.3_3.3.9-r1_aarch64_cortex-a53.ipk
Installing ruby (3.3.9-r1) to root...
Downloading https://dl.openwrt.ai/releases/24.10/packages/aarch64_cortex-a53/packages/ruby_3.3.9-r1_aarch64_cortex-a53.ipk
Installing ruby-digest (3.3.9-r1) to root...
Downloading https://dl.openwrt.ai/releases/24.10/packages/aarch64_cortex-a53/packages/ruby-digest_3.3.9-r1_aarch64_cortex-a53.ipk
Installing ruby-enc (3.3.9-r1) to root...
Downloading https://dl.openwrt.ai/releases/24.10/packages/aarch64_cortex-a53/packages/ruby-enc_3.3.9-r1_aarch64_cortex-a53.ipk
Installing ruby-pstore (3.3.9-r1) to root...
Downloading https://dl.openwrt.ai/releases/24.10/packages/aarch64_cortex-a53/packages/ruby-pstore_3.3.9-r1_aarch64_cortex-a53.ipk
Installing ruby-bigdecimal (3.3.9-r1) to root...
Downloading https://dl.openwrt.ai/releases/24.10/packages/aarch64_cortex-a53/packages/ruby-bigdecimal_3.3.9-r1_aarch64_cortex-a53.ipk
Installing ruby-date (3.3.9-r1) to root...
Downloading https://dl.openwrt.ai/releases/24.10/packages/aarch64_cortex-a53/packages/ruby-date_3.3.9-r1_aarch64_cortex-a53.ipk
Installing ruby-stringio (3.3.9-r1) to root...
Downloading https://dl.openwrt.ai/releases/24.10/packages/aarch64_cortex-a53/packages/ruby-stringio_3.3.9-r1_aarch64_cortex-a53.ipk
Installing libyaml (0.2.5-r1) to root...
Downloading https://dl.openwrt.ai/releases/24.10/packages/aarch64_cortex-a53/packages/libyaml_0.2.5-r1_aarch64_cortex-a53.ipk
Installing ruby-psych (3.3.9-r1) to root...
Downloading https://dl.openwrt.ai/releases/24.10/packages/aarch64_cortex-a53/packages/ruby-psych_3.3.9-r1_aarch64_cortex-a53.ipk
Installing ruby-yaml (3.3.9-r1) to root...
Downloading https://dl.openwrt.ai/releases/24.10/packages/aarch64_cortex-a53/packages/ruby-yaml_3.3.9-r1_aarch64_cortex-a53.ipk
Configuring libruby3.3.
Configuring ruby.
Configuring ruby-digest.
Configuring ruby-enc.
Configuring ruby-pstore.
Configuring ruby-stringio.
Configuring ruby-bigdecimal.
Configuring ruby-date.
Configuring libyaml.
Configuring ruby-psych.
Configuring ruby-yaml.
Configuring luci-app-openclash.
```

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

找到下载下来的 clash-linux-arm64.tar.gz，解压得到 clash 文件，然后上传到 clash 目录下

```bash
scp ./clash root@192.168.5.1:/etc/openclash/core/clash_meta
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


卸载 WireGuard：

```bash
$ opkg list-installed | grep wireguard
kmod-wireguard - 6.6.116-r1
luci-proto-wireguard - 26.313.53093~ae2aec8
wireguard-tools - 1.0.20210914-r4

$ opkg remove kmod-wireguard luci-proto-wireguard wireguard-tools
Removing package kmod-wireguard from root...
Removing package luci-proto-wireguard from root...
Removing package wireguard-tools from root...
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