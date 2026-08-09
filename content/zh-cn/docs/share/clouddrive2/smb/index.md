---
title: "通过smb进行访问"
linkTitle: "smb访问"
weight: 10
date: 2021-08-26
description: >
  通过 smb 访问 CloudDrive2 挂载的 115 网盘
---

## openwrt 服务器端

### smb 共享

http://192.168.3.1/cgi-bin/luci/admin/nas/samba4

在 openwrt 的页面进入 "NAS" -> "网络共享"

主机名修改，如 SkyRouter3。

先删除默认的 sda1 共享，sda1 下是 EFI 启动用的 boot 和 efi 文件，不需要共享。

- 名称：115
- 目录：/mnt/CloudNAS/115open/CloudDrive， 直接指向 CloudDrive2 的真实挂载根目录
- 允许匿名 (Allow guest)：勾选，免密码直接访问（推荐局域网使用）
- 只读 (Read-only)： 取消勾选，确保开启写入权限
- 创建掩码 (Create mask)： 0777，赋予新建文件最高读写权限
- 目录掩码 (Directory mask)：0777，赋予新建文件夹最高读写权限

在 "编辑模板" tab 中找到这一行：

```properties
invalid users = root
```

修改为：

```properties
# invalid users = root
force user = root
force group = root
```

备注：这会强制将 Samba 客户端的一切写入/创建指令都映射为 OpenWrt 的 root 用户身份，从而绕过 Linux 本地对 FUSE 挂载点的权限拦截！

重启 openwrt 的 samba4 服务：

```bash
/etc/init.d/samba4 restart
```

此时访问 `smb://192.168.3.1/` 就可以看到 openwrt 共享的 115 目录了。

## smb client 设置

### linux mint

先确保安装了 smb client：

```bash
sudo apt install cifs-utils
```

在 `/etc/fstab` 追加 SMB 配置:

```bash
sudo vi /etc/fstab
```

在末尾追加：

```properties
//192.168.3.1/115 /home/sky/mounts/skyrouter3-115-smb cifs guest,user,noauto,uid=1000,gid=1000,iocharset=utf8,noperm 0 0
```

参数解释：

- guest：无需密码，匿名登录。

- user：关键点，允许普通用户无需 sudo 执行挂载/卸载。

- uid=1000,gid=1000：把挂载后的文件所有者映射为你客户端的本地用户 sky（通常 UID 是 1000），这样在 GUI 文件管理器或命令行里写文件完全不会碰壁。

- noperm：让客户端跳过本地权限检查，全部交给服务端的 force user = root 处理。

更新 systemd 缓存:

```bash
sudo systemctl daemon-reload
```

在 zsh 中增加两个快捷命令：

```bash
vi ~/.zshrc
```

增加内容:

```bash
# 115 SMB mount and umount
alias skyrouter3-115-smb-mount='mkdir -p $HOME/mounts/skyrouter3-115-smb && mount $HOME/mounts/skyrouter3-115-smb'
alias skyrouter3-115-smb-umount='umount -l $HOME/mounts/skyrouter3-115-smb'
```

之后用起来就很方便了，115smbmount / 115smbmount 两个命令直接用，测试如下：

```bash
source ~/.zshrc   
skyrouter3-115-smb-mount
skyrouter3-115-smb-umount
```

smb 下读写都可以，方便做文件同步。

### windows 11

待补充


