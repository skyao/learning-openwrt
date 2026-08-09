---
title: "安装CloudDrive2"
linkTitle: "安装"
weight: 10
date: 2026-08-09
description: >
  安装配置 CloudDrive2
---


## 安装 CloudDrive2

在 openwrt 上安装 CloudDrive2 插件，并进行配置。

可以通过 istore 进行安装，在 istore 中找到 CloudDrive2 插件，点击安装即可（注意这次安装的 openwrt 的 clouddrive2 插件）。

然后在 openwrt 下找到 CloudDrive2 插件，继续再安装（这次是安装 clouddrive2 的 docker 容器），全部默认，但要勾选上共享 `/mnt` 目录。

安装完成后，状态显示： clouddrive2 运行中。点击 "打开clouddrive2" 按钮，进入 clouddrive2 的 web 界面。

备注：定制了自带 clouddrive2 的 openwrt 版本就可以直接使用。

## 注册和登录

可以先注册一个 clouddrive2 的账号，然后登录。

## CloudDrive2 系统设置

右上角点用户图表，菜单中选择 "设置"，进入 CloudDrive2 系统设置页面。

- 启动延迟（秒）: 60 秒，等拨号就绪再启动

- 目录缓存时间（秒）： 修改为0,表示永不过期

- 启用缓存持久化

## 添加 115 为云存储

通常 clouddrive2 的 web 界面地址为，其中端口默认是 19798：

[http://192.168.3.1:19798/](http://192.168.3.1:19798/)

右上角选择语言为中文。

云存储中，点击"添加"，然后选择 "115open"，认证方式选择 "OAuth认证"。在点击 "使用以下方式授权 115open" 按钮，在弹出的页面中登录115并完成授权。

115open 中，点击管理：

- 最大下载线程数: 默认2,修改为10（备注：发现改不了，只能用默认值2，估计是需要用付费版本）

- 勾选 使用 HTTP 下载

- 最小读取长度 (KB):： 2048 KB

- 最大缓冲池大小 (MB): 256MB

### 挂载115网盘

进入 挂载 页面，点击 "添加挂载点"，选择：

- 源目录：`/115open`

- 挂载点： `/mnt/CloudNAS/115open`

此时，可以 ssh 登录 openwrt，或者在 openwrt 的页面上浏览目录 `/mnt/CloudNAS/115open`，就可以看到挂载的 115 网盘的文件：

```bash
ls -l /mnt/CloudNAS/115open/CloudDrive/
drwxr-xr-x    1 root     root             0 Nov 11 19:26 backup
drwxr-xr-x    1 root     root             0 Nov 14 04:37 data
drwxr-xr-x    1 root     root             0 Oct 21 23:29 game
drwxr-xr-x    1 root     root             0 Oct 21 23:45 movie
drwxr-xr-x    1 root     root             0 Nov 10 01:42 musiz
drwxr-xr-x    1 root     root             0 Oct 30 22:15 temp
drwxr-xr-x    1 root     root             0 Oct 21 23:29 tv
......
```



