---
title: "克隆后的修改"
linkTitle: "克隆"
weight: 20
date: 2021-08-26
description: >
  克隆之后修改 zerotier
---

## 克隆后的问题

在 pve 内，通过将 openwrt 虚拟机进行备份，然后复制到其他机器，再通过恢复功能重建 openwrt 虚拟机之后，由于 zerotier 还是沿用的之前的认证信息，因此会导致冒认身份。

### 重置身份

此时正确的方式，应该是删除原有的身份信息，然后重新生成新的身份信息，再在 zerotier 管理页面对新的身份进行认证。

```bash
# 1. 停止服务
/etc/init.d/zerotier stop

# 2. 彻底清空配置目录 (破釜沉舟)
# ls /var/lib/  把和 zerotier 相关的目录清理掉
rm -rf /var/lib/zerotier-luci
rm -rf /var/lib/zerotier-one
rm -rf /etc/config/zero/*

# 3. 启动服务 (生成全新的身份)
/etc/init.d/zerotier start

# 4. 获取最新的 Node ID
sleep 3
zerotier-cli info

```

但我当时错误的选择了删除 zerotier，然后通过 istore 重新安装 zerotier，结果导致了一系列问题，处理起来非常的麻烦。本来最简单的办法应该是重置 zerotier，然后导入配置或者重新是手工再次配置一遍，但我的机器在远程，我只能远程操作（通过ddns+端口映射），一旦网络断开整个机器就会失去控制。

### libminiupnpc 依赖问题

先查看 zerotier 的 info 信息：

```bash
$ zerotier-cli info
Error loading shared library libminiupnpc.so.18: No such file or directory (needed by /usr/bin/zerotier-cli)
```

安装缺失的依赖 libminiupnpc：

```bash
opkg update
opkg install libminiupnpc
```

也可能是安装了其他版本：

```bash
ls /usr/lib/libminiupnpc.so*

/usr/lib/libminiupnpc.so.2.2.3  /usr/lib/libminiupnpc.so.2.2.8
```

这种情况下做一个 soft link 就好了:

```bash
ln -s /usr/lib/libminiupnpc.so.2.2.8 /usr/lib/libminiupnpc.so.18
```

### 路径不一致问题

在 openwrt 页面上，通过 istore 安装 zerotier 之后，在 zerotier 页面开启 zerotier， 并设置好参数，加入ZeroTier 网络 ID，然后保存并应用。

但，会发现 zerotier 并没有成功连接， 而且 zerotier-cli info 报错：

```bash
$ zerotier-cli info
zerotier-cli: missing port and zerotier-one.port not found in /var/lib/zerotier-one
```

这个问题卸载 zerotier，然后清理所有 zerotier 文件后，再次安装，依然有同样问题。后来 ps 时发现，开机启动的 zerotier-one 进行，使用的是 `/var/lib/zerotier-luci` 这个工作目录，而不是默认的 `/var/lib/zerotier-one`。

```bash
ps | grep zerotier
 5748 root      1188 S    grep zerotier
31281 root      1196 S    {zerotier-nat} /bin/sh /usr/libexec/zerotier-nat
31282 root      1256 S    {zerotier-dns} /bin/sh /usr/libexec/zerotier-dns
31283 root     34220 S    /usr/bin/zerotier-one /var/lib/zerotier-luci
```

这应该是从 istore 上安装的 zerotier 的问题，要解决这个问题最彻底的方式是修改 zerotier 的启动脚本：

```bash
vi /etc/init.d/zerotier
```

需要做两个改动：

1. start 命令

    强制指定正确路径，并且在不存在时创建。

     ```bash
     start_service() {
        
        config_get ...
        config_get ...
        config_get ...
        
        # 强制指定正确路径
        config_path="/etc/config/zero"
        mkdir -p "${config_path}"
     ```

2. stop 命令

    这里不要删除目录

     ```bash
     stop_service() {
        # rm -rf "${CONFIG_PATH}"
        return 0
     ```

配置方式可以用 zerotier 页面进行配置。

防火墙配置，可以用最简单的方案：

1. 添加接口

     - 进入 网络 (Network) -> 接口 (Interfaces)。
     - 点击左下角的 “添加新接口” (Add new interface)。
     - 名称：输入 VPN (或者 ZeroTier)。
     - 协议：选择 “不配置协议” (Unmanaged)。
     - 设备 (Device)：在下拉列表中，寻找 ztw4lflcku (或者你刚才查到的名字)。
     - 注意：如果没有直接显示，可以手动输入 ztw4lflcku，或者选择“自定义接口”后输入。
     - 点击 创建 (Create)。

2. 设置防火墙

     在刚才创建的 ZeroTier 接口的修改页面，点击 防火墙设置 (Firewall Settings) ，然后分配防火墙区域为 lan



