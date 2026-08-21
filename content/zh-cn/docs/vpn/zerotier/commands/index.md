---
title: "zerotier命令"
linkTitle: "命令"
weight: 30
date: 2021-08-26
description: >
  zerotier常用命令
---

在 OpenWrt 上，管理 ZeroTier 主要分为两个部分：**通用命令行工具 (`zerotier-cli`)** 和 **OpenWrt 系统服务管理**。

### zeroTier 核心命令 (`zerotier-cli`)

这些命令直接与 ZeroTier 进程交互，用于查看状态、加入网络等。

*   查看当前状态和 Node ID (节点 ID)

    ```bash
    zerotier-cli info
    ```

    > **输出示例:** `200 info 8a6b22xxxx 1.10.2 ONLINE`
    > 这里的 `8a6b22xxxx` 就是你的 **Node ID**，你需要在 ZeroTier 管理后台批准这个 ID。

*   加入网络

    ```bash
    zerotier-cli join <Network_ID>
    ```

    > 示例: `zerotier-cli join a0cbf4b62a12xxxx`

*   离开网络

    ```bash
    zerotier-cli leave <Network_ID>
    ```

*   查看已加入的网络 (及 IP 地址)

    ```bash
    zerotier-cli listnetworks
    ```

    > **重点：** 这个命令会显示 `<dev>` 列（如 `zt7u3....`），这是 ZeroTier 生成的虚拟网卡名称，你在 OpenWrt 的“网络 -> 接口”设置中需要用到它。

*   查看对等节点 (Peers)

    ```bash
    zerotier-cli peers
    ```

    > 用于排查故障，查看是否与控制器（Planet）或其他节点（Leaf）建立了直接连接（Direct）还是中继连接（Relay）。

*   绑定特定物理 IP (高级)

    如果有多条宽带，强制绑定某一个 IP：

    ```bash
    zerotier-cli bond <Network_ID> <IP_Address>
    ```

---

### OpenWrt 服务管理命令

在 OpenWrt 上，建议通过系统服务来控制 ZeroTier 的启动和停止，确保持久化运行。

*   启动服务

    ```bash
    /etc/init.d/zerotier start
    ```

*   停止服务

    ```bash
    /etc/init.d/zerotier stop
    ```

*   重启服务

    （修改配置后通常需要执行此命令）

    ```bash
    /etc/init.d/zerotier restart
    ```

*   设置开机自启

    ```bash
    /etc/init.d/zerotier enable
    ```

### OpenWrt UCI 配置命令 (推荐方式)

虽然 `zerotier-cli join` 可以加入网络，但在 OpenWrt 中，**最佳实践**是将网络 ID 写入配置文件 `/etc/config/zerotier`，这样重启路由器后配置不会丢失。

*   **查看当前 UCI 配置**

    ```bash
    cat /etc/config/zerotier
    ```

*   **通过命令添加网络 ID (永久保存)**

    假设你的 Network ID 是 `a0cbf4b62a12xxxx`：

    ```bash
    uci add_list zerotier.sample_config.join='a0cbf4b62a12xxxx'
    uci commit zerotier
    /etc/init.d/zerotier restart
    ```

    *(注意：`sample_config` 是默认的配置段名称，如果你的配置文件里改了名字，请相应替换)*
OpenWrt 网页后台配置接口和防火墙。

