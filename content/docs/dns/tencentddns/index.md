---
title: "腾讯云DDNS设置"
linkTitle: "腾讯云DDNS"
weight: 10
date: 2021-08-26
description: >
  通过 腾讯云DDNS 实现动态域名解析
---



## 背景

希望通过动态域名方便的访问运行在家里的几台服务器。

由于我的域名目前是在腾讯云进行的域名解析，因此需要实现在 openwrt 中使用 腾讯云DDNS。

## 准备工作

### 创建子域名

在腾讯云控制台，选择要使用的域名：

https://console.dnspod.cn/dns/list

在其下创建一个新的 A 记录，如 `dev`，记录值随便填写一个，后面会被 ddns 插件覆盖为正确的值。

由于是免费套餐，TTL 最小只能设置为 600 （单位秒，也就是十分钟）。

### 创建 API token

由于 ddns 插件是通过 API token 来执行 dns 记录更新，因此需要提前创建 API token。打开网页：

https://console.dnspod.cn/account/token/token

> 注意： 使用的 DNSPod Token，不是 腾讯云 API 密钥

创建密钥，设置为:

```
dev.skyao.net
ID	:383XXX
Token	:3de14556b8a8bXXXXXXXXXXXXXX
```

小心保存好这个 ID 和 token,后面要用。

## 配置openwrt

###  安装TencentDDNS

很幸运的是腾讯云提供了一个腾讯云DDNS 的 openwrt 插件：

https://openapp.qq.com/docs/OpenWRT/ddns.html

> 更新：这个网址现在（2023年6月）已经打不开了。但好在下载地址还可以使用：
>
> http://openwrt-tencentddns-1301800460.cos.ap-guangzhou.myqcloud.com/luci-app-tencentddns_0.1.0-1_all.ipk

这个插件很久没有更新了，不过目前还可以用。

安装方式参考上面文档的说明，在 openwrt 的 系统 -》软件包 页面进行网络安装：

```bash
Downloading http://openwrt-tencentddns-1301800460.cos.ap-guangzhou.myqcloud.com/luci-app-tencentddns_0.1.0-1_all.ipk
Installing luci-app-tencentddns (0.1.0-1) to root...
Configuring luci-app-tencentddns.
Collected errors:
 * pkg_hash_check_unresolved: cannot find dependency kernel (= 5.15.86-1-6d22281f0efc3afbdd740ed895611758) for kmod-crypto-hash
 * pkg_hash_fetch_best_installation_candidate: Packages for kmod-crypto-hash found, but incompatible with the architectures configured
 * pkg_hash_check_unresolved: cannot find dependency kernel (= 5.15.86-1-6d22281f0efc3afbdd740ed895611758) for kmod-crypto-null
 * pkg_hash_fetch_best_installation_candidate: Packages for kmod-crypto-null found, but incompatible with the architectures configured
 * pkg_hash_check_unresolved: cannot find dependency kernel (= 5.15.86-1-6d22281f0efc3afbdd740ed895611758) for kmod-crypto-aead
 * pkg_hash_fetch_best_installation_candidate: Packages for kmod-crypto-aead found, but incompatible with the architectures configured
 * pkg_hash_check_unresolved: cannot find dependency kernel (= 5.15.86-1-6d22281f0efc3afbdd740ed895611758) for kmod-crypto-manager
 * pkg_hash_fetch_best_installation_candidate: Packages for kmod-crypto-manager found, but incompatible with the architectures configured
 * pkg_hash_check_unresolved: cannot find dependency kernel (= 5.15.86-1-6d22281f0efc3afbdd740ed895611758) for kmod-crypto-user
 * pkg_hash_fetch_best_installation_candidate: Packages for kmod-crypto-user found, but incompatible with the architectures configured
 * pkg_hash_check_unresolved: cannot find dependency kernel (= 5.15.86-1-6d22281f0efc3afbdd740ed895611758) for kmod-crypto-authenc
 * pkg_hash_fetch_best_installation_candidate: Packages for kmod-crypto-authenc found, but incompatible with the architectures configured
 * pkg_hash_check_unresolved: cannot find dependency kernel (= 5.15.86-1-6d22281f0efc3afbdd740ed895611758) for kmod-cryptodev
 * pkg_hash_fetch_best_installation_candidate: Packages for kmod-cryptodev found, but incompatible with the architectures configured
```

一堆报错，我正要考虑这些依赖问题时，发现 openwrt 的左边导航条增加一个了 “腾讯云设置” 的分类，下面有 腾讯云 DDNS。实际验证确认上述报错不影响使用，就先忽略吧。

> 备注：后续网络安装时遇到无法安装的情况，报错文件不存在，莫名其妙。后来发现可以ssh到openwrt下，然后wget下载，再 okpg install 。

### 配置腾讯云 DDNS

进入  腾讯云 DDNS 设置界面，选择启动，勾选 "清除所有同名记录", 密钥ID 和 密钥Token 填写前面保存的 DNSPod Token 的内容。WAN-IP来源 选择 wan，检查时间设置为最小的 1 分钟。

点击保存&应用：

```bash
2023-02-08 12:27:27 WAN-IP: 116.21.255.9
2023-02-08 12:27:27 DOMAIN-IP: 116.21.255.9
2023-02-08 12:27:27 IP dont need UPDATE...
```

### 验证ddns

此时去腾讯云的页面，可以看到dev这个a记录的值已经被修改为正确的ip地址：

https://console.dnspod.cn/dns/skyao.net/record

用 nslookup 命令检查：

```bash
nslookup dev.skyao.net
Server:		127.0.0.53
Address:	127.0.0.53#53

Non-authoritative answer:
Name:	dev.skyao.net
Address: 116.21.255.9
```

> 备注：由于dns的缓冲，如果之前有解析过这个域名，则可能会继续沿用上一次解析的结果，稍等10分钟待 dns ttl 失效。

## 参考文档

- [利用DNSPod实现动态域名解析DDNS (解析内网、外网或IPV6地址)](https://blog.csdn.net/Imkiimki/article/details/83794355)