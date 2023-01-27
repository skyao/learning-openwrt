---
title: "设置OpenWRT的openclash"
linkTitle: "openclash"
weight: 10
date: 2021-08-26
description: >
  通过 openclash 进行网络代理
---

## 安装

我使用的 openwrt 固件默认安装了 openclash：

http://192.168.0.1/cgi-bin/luci/admin/services/openclash

## 配置

在配置文件订阅中，添加订阅地址，然后保存，再更新配置。

也可以启用自动更新，比如每周一更新一次。

## 启用



## 避免bt流量走代理

由于同时开启了 openclash 做代理，因此会遇到一个问题：有些 bt 的流量会被 openclash 代理，从而无端的耗费了宝贵的代理流量。

全局设置 -》 模式设置 中，勾选：

- ***\*仅允许常用端口流量\****
- 实验性：绕过中国大陆 IP

全局设置 -》访问控制 中：

- 不走代理的局域网LAN IP
- 不走代理的局域网设备 Mac

参考资料：

- https://www.v2ex.com/t/757230
