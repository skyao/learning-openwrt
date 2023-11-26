---
title: "设置 ShadowSocksR Plus+"
linkTitle: "ShadowSocksR Plus+"
weight: 20
date: 2021-08-26
description: >
  通过 ShadowSocksR Plus+ 进行网络代理
---

## 安装

TP Link XRD 6088 路由器所使用的 openwrt 固件默认安装了 ShadowSocksR Plus+， 而不是我之前熟悉的 openclash。

## 配置

在 "服务器节点" 配置页面的 "SS/SSR/V2/TROJAN 订阅 URL" 中输入订阅地址， 然后点击 "更新订阅 URL 列表"，再点击 "更新所有订阅服务器节点"  进行更新。

然后就可以在底下的 服务器节点列表 中看到订阅的各个服务器。

> 也可以启用自动更新。

{{% alert title="注意" color="warning" %}}
这里不能方便的在多个订阅地址之间切换，有点不方便
{{% /alert %}}

## 启用

在服务器节点列表选择一个合适的服务器节点（比如 ping 比较快的），点击 “应用”。

{{% alert title="注意" color="warning" %}}
这里不能自动选择可用节点，有点不方便
{{% /alert %}}

## 避免bt流量走代理

由于同时开启了 openclash 做代理，因此会遇到一个问题：有些 bt 的流量会被 openclash 代理，从而无端的耗费了宝贵的代理流量。

解决方法：在 "客户端" 页面的 “ShadowSocksR Plus+ 设置” 中，找到 "需要代理的端口" ，选择 "仅常用端口（不走P2P流量到代理）"。

这样就可以避免P2P流量走代理了，如果觉得还不放心，可以再加一个IP地址限制：

在 "访问控制" 页面的 "LAN IP 访问控制" 中，找到 "不走代理的局域网 LAN IP"，在这里设置 bt 机器所在的 IP 即可。

## 代理服务器信息

在 "高级设置" 页面的 "SOCKS5 代理服务端（全局）" 中

- 服务器：可以设置为 "与全局服务器相同"，也可以设置为使用其他的服务器（这一点比较灵活）
- 本地端口： 这里为了和之前 openclash 的端口保持一致，我设置为 7891

### http 代理问题

由于 ShadowSocksR Plus+ 仅提供 socks5 代理，不提供 http 代理，因此需要使用到 privoxy 来将 socks5 代理转为 http 代理。

见下一节。
