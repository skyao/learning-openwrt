---
title: "HP544+网卡的驱动设置"
linkTitle: "HP544+网卡"
weight: 500
date: 2021-08-26
description: >
  在 openERT 下使用 HP544+ 网卡的问题
---

## 总结

先说总结：尽量选择默认支持 mlx4-core 的 openwrt 固件，这样 HP544+ 网卡可以直接使用。

自行添加驱动非常的麻烦，而且未必能生效。

> 以下为历史记录，未能成功，留作存档。

## 处理过程

参考 https://www.snailium.com/server/home-server-improvement-virtualization.html 为 hp544+网卡安装驱动

```bash
opkg update
opkg install luci
opkg install kmod-mlx4-core
```

报错：cannot find dependency kernel for kmod-pps

原因是 openwrt 的 kernel 版本不够新，要求 5.15.86-1，可以通过 `uname -a`  命令来查看当前 kernel 版本。


参考：https://cloud.tencent.com/developer/article/2101934

打开页面 https://downloads.openwrt.org/snapshots/targets/x86/64/packages/，找到 kernel 


```bash
wget https://downloads.openwrt.org/snapshots/targets/x86/64/packages/kernel_5.15.85-1-9f9e11a5e946333b83ba37f6864e5c49_x86_64.ipk
opkg install kernel_5.15.85-1-9f9e11a5e946333b83ba37f6864e5c49_x86_64.ipk
```

之后继续执行安装：

```bash
opkg update
opkg install luci
opkg install kmod-mlx4-core

Multiple packages (libgcc1 and libgcc1) providing same name marked HOLD or PREFER. Using latest.
Upgrading kmod-mlx4-core on root from 5.15.78-1 to 5.15.86-1...
Downloading http://downloads.openwrt.org/snapshots/targets/x86/64/packages/kmod-mlx4-core_5.15.86-1_x86_64.ipk
Removing obsolete file /lib/modules/5.15.78/mlx4_core.ko.
Removing obsolete file /lib/modules/5.15.78/mlx4_en.ko.
Configuring kmod-mlx4-core.

reboot
```

执行 `ip addr` 命令发现还是无法识别网卡，继续：

```bash
opkg install kmod-mlx4-core_5.15.85-1_x86_64.ipk
Package kmod-mlx4-core (5.15.85-1) installed in root is up to date.
Collected errors:
 * pkg_hash_check_unresolved: cannot find dependency kernel (= 5.15.78-1-32cd5cb5ee6845016feef0041546326a) for kmod-pps
 * pkg_hash_fetch_best_installation_candidate: Packages for kmod-pps found, but incompatible with the architectures configured
 * pkg_hash_check_unresolved: cannot find dependency kernel (= 5.15.78-1-32cd5cb5ee6845016feef0041546326a) for kmod-ptp
 * pkg_hash_fetch_best_installation_candidate: Packages for kmod-ptp found, but incompatible with the architectures configured


root@OpenWrt:~# uname -a
Linux OpenWrt 5.15.78 #0 SMP Sun Nov 27 04:01:29 2022 x86_64 GNU/Linux


wget https://downloads.openwrt.org/snapshots/targets/x86/64/packages/kmod-pps_5.15.85-1_x86_64.ipk
opkg install kmod-pps_5.15.85-1_x86_64.ipk

wget https://downloads.openwrt.org/snapshots/targets/x86/64/packages/kmod-ptp_5.15.85-1_x86_64.ipk
opkg install kmod-ptp_5.15.85-1_x86_64.ipk

opkg install kmod-mlx4-core_5.15.85-1_x86_64.ipk
Package kmod-mlx4-core (5.15.85-1) installed in root is up to date.

reboot
```



```bash
Collected errors:
 * pkg_hash_check_unresolved: cannot find dependency kernel (= 5.15.86-1-9f9e11a5e946333b83ba37f6864e5c49) for kmod-pps
 * pkg_hash_check_unresolved: cannot find dependency kernel (= 5.15.86-1-9f9e11a5e946333b83ba37f6864e5c49) for kmod-ptp
 * pkg_hash_check_unresolved: cannot find dependency kernel (= 5.15.86-1-9f9e11a5e946333b83ba37f6864e5c49) for kmod-mlx4-core

wget https://downloads.openwrt.org/snapshots/targets/x86/64/packages/kernel_5.15.86-1-9f9e11a5e946333b83ba37f6864e5c49_x86_64.ipk
opkg install kernel_5.15.86-1-9f9e11a5e946333b83ba37f6864e5c49_x86_64.ipk

Multiple packages (libgcc1 and libgcc1) providing same name marked HOLD or PREFER. Using latest.
Upgrading kmod-mlx4-core on root from 5.15.85-1 to 5.15.86-1...
Downloading http://downloads.openwrt.org/snapshots/targets/x86/64/packages/kmod-mlx4-core_5.15.86-1_x86_64.ipk
Removing obsolete file /lib/modules/5.15.85/mlx4_en.ko.
Removing obsolete file /lib/modules/5.15.85/mlx4_core.ko.
Configuring kmod-mlx4-core.
```


### 启动mlx4-core


`etx/modules.d/mlx4-core`

```properties
mlx4_core
mlx4_en
```

网卡驱动已经安装，lspci 可以识别网卡，但是网卡一直无法识别。

## 后记

找了一个支持的固件就直接搞定了

https://www.right.com.cn/forum/thread-8267087-1-1.html

