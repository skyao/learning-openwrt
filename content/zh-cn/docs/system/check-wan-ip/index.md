---
title: "检查是否公网IP"
linkTitle: "公网IP"
weight: 660
date: 2021-08-25
description: >
  检查 wan ip，如果不是公网ip，则重新拨号，确保公网ip可用
---

## 背景

在某处的宽带网络中，wan ip 有时不是公网ip，而是内网ip（100开头的号段），导致无法通过公网/DDNS访问。

因此，需要检查 wan ip，如果不是公网ip，则重新拨号。按照经验，一般拨号3-5次就大概率能获取到公网ip。

## 脚本实现

ssh 登录 openwrt 路由器，添加以下脚本：

```bash
vi /usr/bin/check_wan_ip.sh
```

内容为：

```bash
#!/bin/sh

# 日志文件路径
LOGFILE="/var/log/check_wan_ip.log"
# 日志大小上限（字节，10MB）
MAXSIZE=$((10 * 1024 * 1024))

# 检查日志文件大小，超过则轮转
if [ -f "$LOGFILE" ]; then
    SIZE=$(stat -c%s "$LOGFILE")
    if [ "$SIZE" -ge "$MAXSIZE" ]; then
        mv "$LOGFILE" "$LOGFILE.1"
        echo "$(date '+%F %T') Log rotated, old log moved to $LOGFILE.1" > "$LOGFILE"
    fi
fi

# 开机宽限期（秒）
BOOT_GRACE=120

# 获取当前 WAN 接口的 IPv4 地址
IP=$(ifstatus wan | grep '"address"' | head -n1 | awk -F'"' '{print $4}')

# 定义日志函数（写入文件）
log() {
    echo "$(date '+%F %T') $1" >> "$LOGFILE"
}

# 定义重拨函数
redial() {
    log "Redialing wan..."
    ifdown wan && sleep 5 && ifup wan
}

# 如果没取到 IP，检查启动时间
if [ -z "$IP" ]; then
    UPTIME=$(cut -d. -f1 /proc/uptime)
    if [ "$UPTIME" -lt "$BOOT_GRACE" ]; then
        log "No IP yet, but system just booted ($UPTIME s), skipping check."
        exit 0
    else
        log "No IP found on wan after $UPTIME s, redialing..."
        redial
        exit 0
    fi
fi

# 判断是否为私网/CGNAT
is_private_ip() {
    case $1 in
        10.*) return 0 ;;                                      # 10.0.0.0/8
        192.168.*) return 0 ;;                                 # 192.168.0.0/16
        172.1[6-9].*|172.2[0-9].*|172.3[0-1].*) return 0 ;;    # 172.16.0.0/12
        100.6[4-9].*|100.7[0-9].*|100.8[0-9].*|100.9[0-5].*) return 0 ;; # 100.64.0.0/10
    esac
    return 1
}

if is_private_ip "$IP"; then
    log "Got private/CGNAT IP ($IP) on wan"
    redial
else
    log "Got public IP ($IP) on wan, OK."
fi
```

增加执行权限：

```bash
chmod +x /usr/bin/check_wan_ip.sh
```

## 定期检查

设置每分钟检查一次：

```bash
crontab -e
```

添加以下内容：

```bash
*/1 * * * * /usr/bin/check_wan_ip.sh
```

保存并退出。确保 cron 服务已启用并运行：

```bash
/etc/init.d/cron enable
/etc/init.d/cron start
```

检查日志内容：  

```bash
tail -f /var/log/check_wan_ip.log
```

如果获取到的是公网ip，会看到类似如下的内容：

```properties
2025-10-31 21:12:00 Got public IP (116.21.255.172) on wan, OK.
2025-10-31 21:13:00 Got public IP (116.21.255.172) on wan, OK.
2025-10-31 21:14:00 Got public IP (116.21.255.172) on wan, OK.
```

如果获取到的是私网ip，则会多次尝试拨号，直到获取到公网ip：

```bash
2025-10-31 21:27:00 Got private/CGNAT IP (100.95.147.166) on wan
2025-10-31 21:27:00 Redialing wan...
2025-10-31 21:28:00 Got private/CGNAT IP (100.95.155.241) on wan
2025-10-31 21:28:00 Redialing wan...
2025-10-31 21:29:00 Got private/CGNAT IP (100.95.143.37) on wan
2025-10-31 21:29:00 Redialing wan...
2025-10-31 21:30:00 Got private/CGNAT IP (100.95.143.37) on wan
2025-10-31 21:30:00 Redialing wan...
2025-10-31 21:31:00 Got public IP (49.84.144.173) on wan, OK.
2025-10-31 21:32:00 Got public IP (49.84.144.173) on wan, OK.
```
