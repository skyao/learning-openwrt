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

启动后可以通过 openclash 菜单中的 "运行日志" 来查看启动过程，尤其是启动失败的原因。

{{% alert title="特别注意" color="warning" %}}
如果启动时卡住，尤其是下载失败，请注意很可能是 dns 污染造成下载失败。
{{% /alert %}}

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

## 代理服务器信息

- HTTP(S) 代理端口： 7890

- SOCKS5 代理端口： 7891

- HTTP(S)&SOCKS5 混合代理端口： 7893

平时使用时，通过下面的命令进行代理设置：

```bash
export all_proxy=socks5://192.168.0.1:7891;export http_proxy=http://192.168.0.1:7890;export https_proxy=http://192.168.0.1:7890
```
方便起见，修改 `~/.zshrc` 或者 `/etc/profile` ：

```bash
# proxy enable
alias proxyon='export all_proxy=socks5://192.168.0.1:7891;export http_proxy=http://192.168.0.1:7890;export https_proxy=http://192.168.0.1:7890'
alias proxyoff='unset all_proxy http_proxy https_proxy'
```

## socks5 / https 认证信息

默认 openclash 会设置 socks5 / https 认证，导致出现如下的错误提示：

```bash
$ wget https://github.com/envoyproxy/envoy/releases/download/v1.23.3/envoy-1.23.3-linux-x86_64
--2023-06-27 21:20:18--  https://github.com/envoyproxy/envoy/releases/download/v1.23.3/envoy-1.23.3-linux-x86_64
Connecting to 192.168.0.1:7890... connected.
Proxy tunneling failed: Proxy Authentication RequiredUnable to establish SSL connection.
```

简单起见，去掉认证。

具体做法：

- 点击 openclash 菜单中的 "覆写设置" 
- "基本设置" 拉到最下方，找到 "设置 SOCKS5/HTTP(S) 认证信息"
- 取消用户名和密码的勾选
- 保存配置 / 应用配置

```bash
$ wget https://github.com/envoyproxy/envoy/releases/download/v1.23.3/envoy-1.23.3-linux-x86_64
```

验证结果：

```bash
--2023-06-27 21:22:02--  https://github.com/envoyproxy/envoy/releases/download/v1.23.3/envoy-1.23.3-linux-x86_64
Connecting to 192.168.0.1:7890... connected.
Proxy request sent, awaiting response... 302 Found
Location: https://objects.githubusercontent.com/github-production-release-asset-2e65be/65214191/
......
Connecting to 192.168.0.1:7890... connected.
Proxy request sent, awaiting response... 200 OK
Length: 61566056 (59M) [application/octet-stream]
Saving to: ‘envoy-1.23.3-linux-x86_64’

envoy-1.23.3-linux-x86_64     100%[=================================================>]  58.71M  23.4MB/s    in 2.5s 
```

