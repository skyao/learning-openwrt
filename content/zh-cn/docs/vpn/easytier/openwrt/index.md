---
title: "安装配置openwrt节点"
linkTitle: "openwrt"
weight: 40
date: 2028-08-20
description: >
   安装配置 easytier 的 openwrt 节点
---

## 安装

从这里下载：

https://github.com/EasyTier/luci-app-easytier/releases

下载类似 EasyTier-v2.6.4-x86_64-22.03.7.zip  的文件。

解压缩后得到的文件如下：

```bash
$ ls -lh

total 28M
-rw-r--r-- 1 sky 197121 11M May 16 12:14 easytier-noweb_2.6.4_x86_64.ipk
-rw-r--r-- 1 sky 197121 18M May 16 12:14 easytier_2.6.4_x86_64.ipk
-rw-r--r-- 1 sky 197121 70K May 16 12:14 luci-app-easytier_2.6.4_all.ipk
-rw-r--r-- 1 sky 197121 15K May 16 12:14 luci-i18n-easytier-zh-cn_git-26.136.03667-39d7eda_all.ipk
```

登录到 openwrt 上，查看：

```bash
$ cat /etc/os-release | grep -E "OPENWRT_ARCH|OPENWRT_RELEASE"
OPENWRT_ARCH="x86_64"
OPENWRT_RELEASE="bleachwrt mod 20260808 @ R26.05.20"
```

打开 openwrt 的页面，进入 istore，手工安装。

依次安装 easytier_2.6.4_x86_64.ipk：

```bash
-rw-r--r--    1 root     root      18406334 Aug 23 01:13 /tmp/is-root/tmp/easytier_2.6.4_x86_64.ipk
MD5: 25166daf201a472104d48fc93fe7c546
Save installed pkg list before installing
Installing pkg file
Multiple packages (libgcc1 and libgcc1) providing same name marked HOLD or PREFER. Using latest.
Installing easytier (2.6.4) to root...
Configuring easytier.
Save installed pkg list after installing
The following packages were added:
easytier
```

luci-app-easytier_2.6.4_all.ipk:

```bash
-rw-r--r--    1 root     root         70828 Aug 23 01:16 /tmp/is-root/tmp/luci-app-easytier_2.6.4_all.ipk
MD5: 95574df716a4c674ccb351c0a8730d5f
Save installed pkg list before installing
Installing pkg file
Multiple packages (libgcc1 and libgcc1) providing same name marked HOLD or PREFER. Using latest.
Installing luci-app-easytier (2.6.4) to root...
Configuring luci-app-easytier.
start..
Save installed pkg list after installing
The following packages were added:
luci-app-easytier
```

luci-i18n-easytier-zh-cn_git-26.136.03667-39d7eda_all.ipk:

```bash
-rw-r--r--    1 root     root         15213 Aug 23 01:16 /tmp/is-root/tmp/luci-i18n-easytier-zh-cn_git-26.136.03667-39d7eda_all.ipk
MD5: 9f6eba905428cebd38a2cf12ec766e1b
Save installed pkg list before installing
Installing pkg file
Multiple packages (libgcc1 and libgcc1) providing same name marked HOLD or PREFER. Using latest.
Installing luci-i18n-easytier-zh-cn (git-26.136.03667-39d7eda) to root...
Configuring luci-i18n-easytier-zh-cn.
Save installed pkg list after installing
The following packages were added:
luci-i18n-easytier-zh-cn
```

安装完成后在 vpn 菜单下就能看到 easytier 




## 组网

### openclash配置

在配置 easytier 之前，必须先配置好 openclash，不然 openclash 会干扰了 EasyTier。

完整可用自定义规则（直接复制到 OpenClash → 自定义规则 / 附加规则）

```bash
# ==============================================
# EasyTier 组网绕过规则（放在规则列表最顶部！优先级最高）
# ==============================================
# 1. EasyTier 虚拟组网网段（你当前 10.126.126.0/24）
- IP-CIDR,10.126.126.0/24,DIRECT,no-resolve
# 2. 本地局域网网段（你的 LAN：192.168.2.0/24）
- IP-CIDR,192.168.2.0/24,DIRECT,no-resolve
# 3. EasyTier 公共节点IP（你正在连接的节点）
- IP-CIDR,159.75.84.176/32,DIRECT,no-resolve

# ----------------可选补充----------------
# 如果后续增加其他异地子网代理，继续在这里添加
# - IP-CIDR,192.168.3.0/24,DIRECT,no-resolve
```

备注：无效，先关闭 openclash 再配置 easytier。

### 配置

easytier 配置中，基本设置：

- 勾选 启动

- 启动方式：选择配置文件

- 配置文件中修改

   ```bash
   instance_name = "skyrouter2"
   instance_id = "f9f05421-7c6b-47d6-a67d-4d58d49f51d8"
   dhcp = true
   listeners = []
   exit_nodes = []
   rpc_portal = "0.0.0.0:15888"
   
   [network_identity]
   network_name = "skynet"
   network_secret = "SuperStrongPass_ChangeMe_123456"
   
   [[peer]]
   uri = "tcp://159.75.84.176:11010"
   ```

高级设置中：

勾选访问控制的所有选项。



## 常见错误



