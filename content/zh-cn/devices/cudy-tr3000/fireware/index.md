---
title: "固件"
linkTitle: "固件"
weight: 20
date: 2025-11-20
description: >
  Cudy TR3000 openwrt固件
---

## 下载

### 下载地址

https://openwrt.ai/?target=mediatek%2Ffilogic&id=cudy_tr3000-mod

输入 cudy tr3000 搜索，选择弹出来的 "Cudy TR3000 v1 112M"，这里只有最新的一个版本可选。

我另外找到了 kwrt 针对 cudy_tr3000 的固件下载地址：

https://dl.openwrt.ai/firmware/mediatek-filogic/cudy_tr3000-mod/

这里有最新版本和各种历史办法，非常方便。

### 最新 25.xx 固件

我尝试了最新的几个版本，都可以正常刷新，也都能用，比如2026年7月最新的版本：

https://dl.openwrt.ai/firmware/mediatek-filogic/cudy_tr3000-mod/kwrt-07.15.2026-mediatek-filogic-cudy_tr3000-mod-squashfs-sysupgrade.bin

- 说明:	刷机后默认后台为 10.0.0.1，默认用户名 root ，密码: root

使用说明：最新的版本是急于 openwrt 25.xx 版本，有点担心生态的兼容性。所以最后还是换到 openwrt 24.xx 版本使用。

### 稳定的 24.xx 的固件

2025-11-10 这个好像是 24.xx 最后一个版本，先用这个，等 25.xx 版本稳定再换：

https://dl.openwrt.ai/firmware/mediatek-filogic/cudy_tr3000-mod/kwrt-11.10.2025-mediatek-filogic-cudy_tr3000-mod-squashfs-sysupgrade.bin

这个镜像的基本信息：

- 型号:	Cudy TR3000 v1 112M
- 平台:	mediatek/filogic
- 版本:	分支: 24.10
- 内核: 6.6.116
- 生成日期:	2025-11-10 15:46:04
- 说明:	后台为 10.0.0.1，默认用户名 root  密码: root

## 刷固件

在 openwrt 界面刷新固件，很方便。上面 24.xx 和 25.xx 的固件都可以刷入。

