---
title: "规避DNS污染问题"
linkTitle: "DNS污染"
weight: 9
date: 2021-08-26
description: >
  通过修改 hosts 文件来规避DNS污染
---

ssh 登录 openwrt ，修改 hosts 文件：

```bash
vi /etc/hosts
```

添加如下内容：

```properties
199.232.68.133 raw.githubusercontent.com
```

目前已知的有明显影响的地方：

- `raw.githubusercontent.com` 被污染之后，会导致很多下载失败，比如 openclash / clash 启动时都要下载文件，另外各种在线下载也会失败。
- 待收集



