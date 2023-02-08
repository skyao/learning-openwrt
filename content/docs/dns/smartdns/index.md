---
title: "设置OpenWRT的smartdns"
linkTitle: "smartdns"
weight: 20
date: 2021-08-26
description: >
  通过 smartdns 进行 dns 解析加速
---

## 背景

### smartdns 介绍

官方网站

https://pymumu.github.io/smartdns/

###  开启 smartdns

openwrt 一般自带 smartdns 支持：

http://192.168.0.1/cgi-bin/luci/admin/services/smartdns

## 配置

勾选启用，本地端口设置为53，配置为主dns

### 设置上游服务器

国内服务器：

服务器名称	服务器地IP	协议类型
114	114.114.114.114	UDP
百度	180.76.76.76	UDP
DNSPod	dns.pub/dns-query	https
阿里	dns.alidns.com/dns-query	https

国外服务器：

服务器名称	服务器地IP	协议类型
Google	dns.google/dns-query	https
CloudFare	dns.cloudflare.com/dns-query	https
Quad9	dns.quad9.net/dns-query	https
OpenDNS	doh.opendns.com/dns-query	https

### 高级设置

测速模式： ping,tcp:80,tcp:433

勾选：

- tcp 服务器
- 双栈IP优选
- 域名预加载
- 缓存过期服务

设置：


- 缓存大小： 默认的  20000
- 域名TTL： 30
- 域名TTL最小值： 30
- 域名TTL最大值： 300
- 回应的域名TTL最大值： 300

### 域名地址

在域名规则-》域名地址中可以设置自定义的域名地址：

```properties
address /skyserver/192.168.0.10
address /skyserver2/192.168.0.20
address /skyserver3/192.168.0.30
address /skyserver4/192.168.0.40
address /skyserver5/192.168.0.50
address /skynas/192.168.0.70
address /skywork/192.168.0.90
```

> 备注：很奇怪我设置之后无法生效，域名解析失败

### 验证

```bash
nslookup baidu.com
服务器:  smartdns
Address:  192.168.0.1

非权威应答:
名称:    baidu.com
Address:  110.242.68.66


nslookup baidu.com 8.8.8.8
服务器:  dns.google
Address:  8.8.8.8

非权威应答:
名称:    baidu.com
Addresses:  110.242.68.66
          39.156.66.10
```

## 后记

开启 smartdns 之后发现偶尔会遇到莫名其妙的问题，暂时先不用吧。

## 参考资料

- [OpenWRT设置SmartDNS+ADGuardHome+小喵咪](https://blog.csdn.net/qq_24649627/article/details/123418021)
