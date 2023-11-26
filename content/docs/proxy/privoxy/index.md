---
title: "privoxy"
linkTitle: "privoxy"
weight: 30
date: 2021-08-26
description: >
  通过 privoxy 将 socks5 代理转为 http 代理
---

## 背景

由于 ShadowSocksR Plus+ 仅提供 socks5 代理，不提供 http 代理，因此需要使用到 privoxy 来将 socks5 代理转为 http 代理。

## 安装 privoxy

命令行安装：

```bash
opkg update
opkg install privoxy
```

也可以在管理页面的软件包中搜索 privoxy 之后安装。

```bash
vi /etc/config/privoxy
```

## 配置 privoxy

打开 privoxy 配置文件，删除现有内容后输入如下内容（因为我们只需要一个 sock5 转 http 的功能）：

```bash
config	privoxy	'privoxy'
	option	confdir		'/etc/privoxy'
	option	logdir		'/var/log'
	option	logfile		'privoxy.log'
	list listen_address	'0.0.0.0:7890'
	option  forward_socks5 '/ 0.0.0.0:7891 .'
```

保存后执行

```bash
/etc/init.d/privoxy restart	
```

重启 privoxy 即可。

## 参考资料

- https://bugxia.com/1292.html
