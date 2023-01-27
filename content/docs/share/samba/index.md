---
title: "设置OpenWRT的samba共享"
linkTitle: "samba共享"
weight: 20
date: 2021-08-26
description: >
  通过 samba 进行网络共享
---

## 准备工作

### 准备 samba 用户

为 root 用户添加 samba 密码：

```bash
smbpasswd -a root
```

增加一个新的 linux 用户并设置 home 目录：

```bash
useradd sky -g root
passwd sky
mkdir /home/sky
chown sky /home/sky/
```

并为其设置 samba 密码：

```bash
smbpasswd -a sky
```

### 确认 samba 版本

openwrt 默认安装了以下 samba 的包：

- samba4-libs
- samba4-server
- autosamba
- luci-app-samba4
- luci-i18n-samba4-zh-cn

功能是预设提供的，因此只要配置好就行。

> 注意: 这里用的是 samba 4 版本。

## 设置

### 页面设置

在 openwrt 的 网络存储 -》网络共享页面进行设置：

http://192.168.0.1/cgi-bin/luci/admin/nas/samba4

共享设置如下：

| name     | 目录          | 容许用户 |
| -------- | ------------- | -------- |
| download | /mnt/download | root,sky |
| shared   | /mnt/shared   | root,sky |
| movie    | /mnt/movie    | root,sky |
| movie2   | /mnt/movie2   | root,sky |
| movie3   | /mnt/movie3   | root,sky |

开始容许匿名用户，**创建权限掩码** 默认为 0666，**目录权限掩码** 默认为 0777

### 修改配置文件

`vi /etc/config/samba4` 可以直接修改配置文件，对应上面页面的功能设置。

页面修改之后，保存并应用，内容就会存储在这个配置文件中。

>但是: 在 `/etc/samba/smb.conf` 文件中也有非常类似的设置，还不清楚这两个配置文件之间的从属关系

发现一个问题： 页面修改之后，我删除了某个内容，但重启还能看到删除的内容，只好手工登录上去，同时修改 /etc/config/samba4 和 `/etc/samba/smb.conf` 文件。

### 重启 samba4

设置完成之后，重启 samba4 ：

```bash
service samba4 restart
```

## 使用情况

- macbook 下正常访问
- linux下报错：
  - 浏览目录时报错：failed to retrieve share list from server invalid argument 
  - 可以通过 `smb://192.168.0.1/download/` 这样的方式直接访问共享内容，不报错
- windows下正常：
- 高清播放器：

### 相关命令

```bash
# 启动服务
service samba4 start
# 停止服务
service samba4 stop
# 重启服务
service samba4 restart
# 服务状态
service samba4 status

# 配置文件检查
testparm -v
```






## 参考资料

- [Openwrt下折腾 Samba4 配置](https://blog.csdn.net/kk380446/article/details/114213014)