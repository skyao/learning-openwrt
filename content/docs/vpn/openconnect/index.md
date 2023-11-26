---
title: "[归档]OpenConnect VPN"
linkTitle: "[归档]OpenConnect VPN"
weight: 100
date: 2021-08-26
description: >
  使用 OpenConnect VPN
---



> 备注： zerotier 非常好用，就没有继续使用 OpenConnect VPN 了。
> 
> 归档



## 安装

```bash
opkg update
opkg install ocserv luci-app-ocserv
reboot
```

如果遇到报错 "cannot find dependency kernel"：

```bash
Collected errors:
 * pkg_hash_check_unresolved: cannot find dependency kernel (= 5.15.102-1-e469f5589b4c7b368924a6e4f8f7407f) for kmod-tun
 * opkg_install_cmd: Cannot install package luci-app-ocserv.
```

则是当前 openwrt 的 kernel 版本不够新：

```bash
uname -a
Linux BleachWrt 5.15.98 #0 SMP Thu Mar 9 14:21:25 2023 x86_64 GNU/Linux
```

打开页面 https://downloads.openwrt.org/snapshots/targets/x86/64/packages/，找到 kernel ，手工下载最新版本并安装：

```bash
wget https://downloads.openwrt.org/snapshots/targets/x86/64/packages/kernel_5.15.102-1-e469f5589b4c7b368924a6e4f8f7407f_x86_64.ipk
opkg install kernel_5.15.102-1-e469f5589b4c7b368924a6e4f8f7407f_x86_64.ipk
```

随即遇到第二个报错：

```bash
opkg update
opkg install luci-app-ocserv 
Unknown package 'luci-app-ocserv'.
Collected errors:
 * opkg_install_cmd: Cannot install package luci-app-ocserv.
```

只能继续手工下载了，打开

https://mirrors.cloud.tencent.com/lede/releases/

找到最新的 release 版本，如 22.03.3，然后选择对应的平台，如我是 x86_64

https://mirrors.cloud.tencent.com/lede/releases/22.03.3/packages/x86_64/luci/

在这里可以找到 luci-app-ocserv_git-20.110.55046-74da73b_all.ipk   ，手工安装：

```bash
$ wget https://mirrors.cloud.tencent.com/lede/releases/22.03.3/packages/x86_64/luci/luci-app-ocserv_git-20.110.55046-74d
a73b_all.ipk
$ opkg install luci-app-ocserv_git-20.110.55046-74da73b_all.ipk 

Multiple packages (libgcc1 and libgcc1) providing same name marked HOLD or PREFER. Using latest.
Multiple packages (libgcc1 and libgcc1) providing same name marked HOLD or PREFER. Using latest.
Multiple packages (libatomic1 and libatomic1) providing same name marked HOLD or PREFER. Using latest.
Installing luci-app-ocserv (git-20.110.55046-74da73b) to root...
Configuring luci-app-ocserv.
Collected errors:
 * pkg_hash_check_unresolved: cannot find dependency kernel (= 5.15.98-1-c3ae984cf3530f6fa5bf8738be620d7b) for kmod-crypto-user
 * pkg_hash_fetch_best_installation_candidate: Packages for kmod-crypto-user found, but incompatible with the architectures configured
 * pkg_hash_check_unresolved: cannot find dependency kernel (= 5.15.98-1-c3ae984cf3530f6fa5bf8738be620d7b) for kmod-cryptodev
 * pkg_hash_fetch_best_installation_candidate: Packages for kmod-cryptodev found, but incompatible with the architectures configured
```

这里有两个依赖需要更新版本，以匹配前面刚更新的 kernel：

```bash
$ opkg install kmod-cryptodev
$ opkg install luci-app-ocserv
```

`reboot` 重启 openwrt.

## 配置





参考资料：

- https://gao4.top/293.html

- https://www.ioiox.com/archives/89.html
