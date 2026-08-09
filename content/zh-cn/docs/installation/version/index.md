---
title: "版本选择"
linkTitle: "版本"
weight: 1
date: 2021-08-26
description: >
  在多种 OpenWRT 版本中进行选择
---

OpenWRT 支持多种方式的安装：

- 虚拟机： 支持 esxi
- 物理机

## 版本选择

### x86-64版本

#### bleachwrt

经过几次对比，目前我选择使用的是这个版本：

https://www.right.com.cn/forum/forum.php?mod=viewthread&tid=8433584&fromuid=559447

下载地址：

https://pan.baidu.com/s/1Y5i7fgaR0b_ieLCZgVVn2A?pwd=nnb7

理由：

- 功能多：高大上版本
- 持续更新

#### 定制 bleachwrt

前几年选择的高大上版本（plus版）。

2026年8月，付费请原作者定制了一个特殊版本，在 plus （插件太多）和 mini （插件太少）之间选择了一个插件刚刚满足我个人需要的版本。之后就固定使用这个版本了。（以后要升级是不是还要再付费定制一次？汗）

定制的插件列表：

| 插件中文名        | Luci App               |
| :---------------- | :--------------------- |
| iStore            | luci-app-store         |
| 实时监控          | luci-app-netdata       |
| 释放内存          | luci-app-ramfree       |
| Web管理           | luci-app-webadmin      |
| TTYD 终端         | luci-app-ttyd          |
| 磁盘管理          | luci-app-diskman       |
| 分区扩容          | luci-app-partexp       |
| 高级设置          | luci-app-advanced      |
| 定时重启          | luci-app-autoreboot    |
| Argon主题设置     | luci-app-argon-config  |
| 文件传输          | luci-app-filetransfer  |
| 关机              | luci-app-poweroff      |
| 腾讯云DDNS        | luci-app-tencentddns   |
| Docker(Dockerman) | luci-app-dockerman     |
| OpenClash         | luci-app-openclash     |
| 微信推送          | luci-app-serverchan    |
| 全能推送          | luci-app-pushbot       |
| 上网时间控制      | luci-app-accesscontrol |
| 网络唤醒          | luci-app-wol           |
| KMS 服务器        | luci-app-vlmcsd        |
| MWAN3 分流助手    | luci-app-mwan3helper   |
| NFS 管理          | luci-app-nfs           |
| CloudDrive2       | luci-app-clouddrive2   |
| 硬盘休眠          | luci-app-hd-idle       |
| 网络共享(SMB)     | luci-app-samba4        |
| qBittorrent       | luci-app-qbittorrent   |
| ZeroTier          | luci-app-zerotier      |
| IP/MAC绑定        | luci-app-arpbind       |
| 流量统计          | luci-app-bandwidthd    |
| 网速测试          | luci-app-netspeedtest  |
| 多线多拨          | luci-app-syncdial      |
| 负载均衡          | luci-app-mwan3         |
| 网络带宽监视器    | luci-app-nlbwmon       |
| 实时流量监测      | luci-app-wrtbwmon      |
| IPV6              | ipv6helper             |

内核版本为：6.12.100



- https://openwrt.mpdn.fun/?dir=mod/lede-x86_64/2026-08-08__21-15-08--10355843356/


### 转换镜像文件

下载之后解压缩之后得到 img 格式的镜像文件，不同安装方式需要转换为其他不同的镜像格式。

