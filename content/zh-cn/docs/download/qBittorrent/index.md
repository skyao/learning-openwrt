---
title: "设置OpenWRT的qBittorrent"
linkTitle: "qBittorrent"
weight: 10
date: 2021-08-26
description: >
  通过 qBittorrent 进行下载
---

## 安装

在 openwrt 自带的 istore 中，找到 qBittorrent，直接安装即可使用。

## 配置

配置页面：

http://192.168.0.1/cgi-bin/luci/admin/nas/qBittorrent

修改基本设置：

- 下载文件存放目录： 设置为默认的下载存放路径，方便使用
- 连接端口： 推荐在防火墙 -> 流量规则 中，打开这个端口获得更好的下载速度

Bittorent 设置：

- 连接数限制： 默认500,修改为2000
- 种子连接数限制： 默认 100
- 种子排队设置： 最大活动的下载数

## 下载

通过页面进行下载设置：

http://192.168.0.1:8080/

## 远程访问

可以打开web ui的远程访问功能，通过 ddns 和端口映射就可以远程控制 qBittorrent 。

特别注意：如果遇到报错 Unauthorized ，则需要在 qBittorrent 配置中的 web ui 选项中，取消以下的勾选：

- 启用跨站请求伪造 (CSRF) 保护
- 启用 Host header 属性验证
