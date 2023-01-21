---
title: "设置OpenWRT的nfs共享"
linkTitle: "nfs共享"
weight: 10
date: 2021-08-26
description: >
  通过 nfs 进行网络共享
---

## 准备工作

准备用 openwrt 这台机器兼职做下载和 nas 服务器。因此硬盘比较多，有用来下载/上传的，也有用来 nas 共享的。

### 直接格式化磁盘

有一块硬盘没有格式化就安装在 openwrt 了，看到 openwrt 的页面可以进行简单的磁盘操作：

1. 打开 系统 -》磁盘管理 页面，找到该磁盘，点击修改
2. 修改分区表格式：推荐GPT
3. 创建分区，并格式化为 ext4 格式


### 安装 ntfs3g

部分已有的硬盘是 ntfs 格式，因为有数据，就继续保持 ntfs 格式了。好在 openwrt 下有 ntfs-3g 的支持，读写不是问题。

```bash
opkg update
opkg install ntfs-3g
Multiple packages (libgcc1 and libgcc1) providing same name marked HOLD or PREFER. Using latest.
Multiple packages (libgcc1 and libgcc1) providing same name marked HOLD or PREFER. Using latest.
Installing ntfs-3g (2022.5.17-1-fuseint) to root...
Downloading https://mirrors.cloud.tencent.com/lede/snapshots/packages/x86_64/packages/ntfs-3g_2022.5.17-1-fuseint_x86_64.ipk
Configuring ntfs-3g.
Create '/etc/filesystems'.
Add 'ntfs-3g' to known filesystems.
Collected errors:
 * pkg_hash_check_unresolved: cannot find dependency kernel (= 5.15.88-1-9f9e11a5e946333b83ba37f6864e5c49) for kmod-fuseroot@BleachWrt:~#
```

内核版本不匹配, uname 可以看到目前的版本是  5.15.86，而上面要求  5.15.88：

```bash
uname -a
Linux BleachWrt 5.15.86 #0 SMP Thu Jan 12 16:00:15 2023 x86_64 GNU/Linux
```

在这里页面上搜索 kernel ：

https://downloads.openwrt.org/snapshots/targets/x86/64/packages/

记下来地址，然后登陆到 openwrt 下手工下载安装：

```bash
wget https://downloads.openwrt.org/snapshots/targets/x86/64/packages/kernel_5.15.88-1-9f9e11a5e946333b83ba37f6864e5c49_x86_64.ipk

opkg install kernel_5.15.88-1-9f9e11a5e946333b83ba37f6864e5c49_x86_64.ipk
Multiple packages (libgcc1 and libgcc1) providing same name marked HOLD or PREFER. Using latest.
Upgrading kernel on root from 5.15.86-1-6d22281f0efc3afbdd740ed895611758 to 5.15.88-1-9f9e11a5e946333b83ba37f6864e5c49...
Configuring kernel.
```

然后安装：

```bash
opkg install mount-utils ntfs-3g lsblk blockd block-mount
```

安全起见，重启一下 openwrt。

## 挂载 ntfs 分区

尝试挂载 ntfs 分区：

```bash
 mount -t ntfs-3g /dev/sda2 /mnt/sda2

$MFTMirr does not match $MFT (record 3).
Failed to mount '/dev/sda2': I/O error
NTFS is either inconsistent, or there is a hardware fault, or it's a
SoftRAID/FakeRAID hardware. In the first case run chkdsk /f on Windows
then reboot into Windows twice. The usage of the /f parameter is very
important! If the device is a SoftRAID/FakeRAID then first activate
it and mount a different device under the /dev/mapper/ directory, (e.g.
/dev/mapper/nvidia_eahaabcc1). Please see the 'dmraid' documentation
for more details.
```


```bash
opkg update
opkg install ntfs-3g-utils

ntfsfix /dev/sda2

Mounting volume... $MFTMirr does not match $MFT (record 3).
FAILED
Attempting to correct errors...
Processing $MFT and $MFTMirr...
Reading $MFT... OK
Reading $MFTMirr... OK
Comparing $MFTMirr to $MFT... FAILED
Correcting differences in $MFTMirr record 3...OK
Processing of $MFT and $MFTMirr completed successfully.
Setting required flags on partition... OK
Going to empty the journal ($LogFile)... OK
Checking the alternate boot sector... OK
NTFS volume version is 3.1.
NTFS partition /dev/sda2 was processed successfully.
```

之后再 mount 就可以成功了，也可以看到磁盘下的文件了：

```bash
mount -t ntfs-3g /dev/sda2 /mnt/sda2
ls  /mnt/sda2
```

同样的方式处理其他几个要挂载的 ntfs 盘符：

```bash
ntfsfix /dev/sdb2
mount -t ntfs-3g /dev/sdb2 /mnt/sdb2
ls  /mnt/sdb2

ntfsfix /dev/sdc2
mount -t ntfs-3g /dev/sdc2 /mnt/sdc2
ls  /mnt/sdc2

ntfsfix /dev/sde2
mount -t ntfs-3g /dev/sde2 /mnt/sde2
ls  /mnt/sde2
```

注意还要设置访问权限，否则会出现连接上去后因为没有读权限导致 mount 的目录里面为空的情况：

```bash
chown nobody:nogroup /mnt/sda2
chmod -R 777  /mnt/sda2
chown nobody:nogroup /mnt/sdb2
chmod -R 777  /mnt/sdb2
chown nobody:nogroup /mnt/sdc2
chmod -R 777  /mnt/sdc2
chown nobody:nogroup /mnt/sde2
chmod -R 777  /mnt/sde2
```

注意：上面的 mount 命令只能保证这一次mount成功，openwrt 重启之后这些 mount 命令的结果也就消失了。因此还是需要找到一个能永久性 mount 盘符的方式。


https://openwrt.org/docs/guide-user/storage/fstab

先 blkid 找到各个盘符的 uuid，备用。

```bash
blkid
/dev/nvme0n1p1: SEC_TYPE="msdos" LABEL_FATBOOT="kernel" LABEL="kernel" UUID="1234-ABCD" BLOCK_SIZE="512" TYPE="vfat" PARTUUID="ee937a51-f5d2-8f30-3541-d2b15aea9701"
/dev/nvme0n1p2: TYPE="squashfs" PARTUUID="ee937a51-f5d2-8f30-3541-d2b15aea9702"
/dev/loop0: LABEL="rootfs_data" UUID="735dbfe5-4bf8-439f-94cb-50ed2997f567" BLOCK_SIZE="4096" TYPE="f2fs"
/dev/nvme0n1p3: UUID="0e5f8ea0-e28a-4755-a378-88428a56cb07" BLOCK_SIZE="4096" TYPE="ext4" PARTLABEL="primary" PARTUUID="3991a910-417a-45a1-b0d6-6688f4ba327f"
/dev/sdb2: LABEL="movie" BLOCK_SIZE="512" UUID="D2BE4708BE46E517" TYPE="ntfs" PARTLABEL="Basic data partition" PARTUUID="077af77b-159f-4601-9d8b-41ad3d7b0879"
/dev/sdc2: UUID="5e92052b-05b6-4ef0-ab8d-4e94fb57a67e" BLOCK_SIZE="4096" TYPE="ext4" PARTLABEL="Basic data partition" PARTUUID="be13e060-ac3a-4df5-9608-518dbed32f68"
/dev/sda2: LABEL="movie3" BLOCK_SIZE="512" UUID="EC588A335889FC98" TYPE="ntfs" PARTLABEL="Basic data partition" PARTUUID="f82cea53-7626-4246-8a1a-b92c6e7fe251"
/dev/sdd2: LABEL="movie2" BLOCK_SIZE="512" UUID="6E603CB9603C89BB" TYPE="ntfs" PARTLABEL="Basic data partition" PARTUUID="14062973-ee65-4f58-9a51-a4b418405350"
/dev/sde1: UUID="1a39276d-e359-49d3-874c-798d32d6b508" BLOCK_SIZE="4096" TYPE="ext4" PARTLABEL="primary" PARTUUID="481e1446-14b8-4437-9783-48cf6b721724"
/dev/nvme0n1p128: PARTUUID="ee937a51-f5d2-8f30-3541-d2b15aea9780"
```

下面的命令可以得到目前的挂载情况：

```bash
block detect > /etc/config/fstab
```

修改 `/etc/config/fstab` , 设置对应的 mount 关系，基本原则就是将 uuid 对应的盘符挂载到 target 所示的路径。但是还是遇到问题，表现为 openwrt 无法自动挂载某些盘符。用 block mount 命令手工挂载时会报错：


```bash
block umount
block mount
block: mounting /dev/sda2 (ntfs) as /mnt/movie1 failed (22) - Invalid argument
block: mounting /dev/sdb2 (ntfs) as /mnt/movie2 failed (22) - Invalid argument
block: mounting /dev/nvme0n1p2 (squashfs) as /mnt/nvme0n1p2 failed (16) - Resource busy
```

奇怪的是此时用 mount 命令手工挂载是正常的：

```bash
mount -t ntfs-3g /dev/sda2 /mnt/movie1
mount -t ntfs-3g /dev/sdb2 /mnt/movie2
```

google一圈发现有人遇到类似问题，解决方法是把硬盘拿到 windows 下扫描一遍后再插回到 openwrt 就可以了。

- https://github.com/coolsnowwolf/lede/issues/9051


```bash
chown nobody:nogroup /mnt/movie
chmod -R 777  /mnt/movie
chown nobody:nogroup /mnt/movie2
chmod -R 777  /mnt/movie2
chown nobody:nogroup /mnt/movie3
chmod -R 777  /mnt/movie3
chown nobody:nogroup /mnt/download
chmod -R 777  /mnt/download
chown nobody:nogroup /mnt/upload
chmod -R 777  /mnt/upload
chown nobody:nogroup /mnt/shared
chmod -R 777  /mnt/shared
```

## 客户端访问授权

可以直接通过命令行修改 `/etc/exports` 文件：

```bash
/mnt/download   192.168.0.0/16(rw,sync,root_squash,all_squash,insecure,no_subtree_check)
/mnt/upload     192.168.0.0/16(rw,sync,root_squash,all_squash,insecure,no_subtree_check)
/mnt/shared     192.168.0.0/16(rw,sync,root_squash,all_squash,insecure,no_subtree_check)
/mnt/movie       192.168.0.0/16(rw,sync,no_subtree_check)
/mnt/movie2       192.168.0.0/16(rw,sync,no_subtree_check)
/mnt/movie3       192.168.0.0/16(rw,sync,no_subtree_check)
```

然后执行下面命令进行export：

```bash
sudo exportfs -a
```

这个操作也可以通过 openwrt 的界面来完成，打开 网络存储 -》nfs管理页面（ http://192.168.0.1/cgi-bin/luci/admin/nas/nfs）操作即可。

## 参考资料

- [OpenWRT配置NTFS硬盘自动挂载](https://zhuanlan.zhihu.com/p/554873559)
- [openwrt 挂载硬盘NFS共享经验分享，非SMB共享](https://www.right.com.cn/forum/thread-8247943-1-1.html)