---
title: "设置OpenWRT的定时重启"
linkTitle: "定时重启"
weight: 30
date: 2021-08-26
description: >
  通过定时重启来减少问题的概率
---

虽然说 openwrt 挺稳定的，但是考虑到长期运行容易积累问题，因此还是定期重启一次会比较好。

打开 系统 -》 定时重启：

http://192.168.0.1/cgi-bin/luci/admin/system/autoreboot

设置为每周一的早上4点重启一次。

一周重启一次应该够用了。