---
title: "基本配置"
linkTitle: "配置"
weight: 60
date: 2026-08-08
description: >
  Openwrt安装之后的基本配置
---

## 优先配置

先点击 System，找到 System Properties / Language and Style，将 Language 修改为简体中文。

网络 ->> 接口 ->> wan 中，设置为 pppoe ，先拨号，连接网络。 

## 系统配置

### System/系统

主题继续用默认的 Argon，其他的都丑的不行。进入 Argon 主题设置页面，主题模式选择“强制亮色”。

基本设置中，主机名设置为 skyrouterX 如 skyrouter3.

### 管理权

修改管理员密码。

为了方便 ssh，添加SSH 密钥

### 软件包

点击更新软件包。

### TTYD 终端

修改网络之后，要重启这里才能使用。

## 服务

### MWAN3 分流助手

发现启用 GFW IPSet 之后，网络访问就会因为找不到 dns 服务器而域名解析失败。

关闭 GFW IPSet 之后就好了。

## NAS

### 网络共享

主机名修改，如 SkyRouter3。

删除默认的 sda1 共享，sda1 下是 EFI 启动用的 boot 和 efi 文件，不需要共享。