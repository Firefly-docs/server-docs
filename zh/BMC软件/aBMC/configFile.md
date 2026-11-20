# 配置文件说明

## 简介

`config.ini` 是 aBMC 服务的主配置文件，集中管理 Web 服务监听、日志输出、固件升级、子板私网网段和 Redis 连接等启动期配置。配置在 aBMC 服务启动时一次性加载并严格校验，修改配置后必须重启服务才会生效。

配置文件按功能划分为六个 section：

| Section | 说明 |
| --- | --- |
| `[Server]` | Web 管理页面 / Redfish API 主服务与 Webhook 服务的监听配置 |
| `[Log]` | 日志级别与控制台输出配置 |
| `[Update]` | 固件升级目录、超时时间与支持平台开关 |
| `[CSA1]` | BMC 与 SOPHGO（算能）子板通信使用的私网网段 |
| `[Redis]` | Redis 依赖的连接配置 |
| `[ADB]` | 已弃用，配置项无实际效果，不再提供详细说明 |

<Callout title="配置文件位置与权限" type="info">
配置文件位于 `/var/firefly-aBMC/config/config.ini`，权限为 `0600`（仅 root 可读写），使用 bmc 等非 root 账户查看或修改时需要加 `sudo`。
</Callout>

## 配置文件管理机制

### 配置由 aBMC 自动维护 [step]

配置文件头部标注了 `This file is managed by aBMC.`，表示该文件由 aBMC 在启动时自动维护：

1. 配置文件不存在时，aBMC 按内置默认值自动创建。
2. 补齐缺失的 section 和配置项：未显式配置的项以注释形式展示其默认值，取消注释后即可手动修改其值。
3. 清理无法识别的 section 和配置项，包括历史版本已移除的配置。
4. 规范化历史写法：旧版本的冒号端口写法（如 `HttpPort = :443`）会自动改写为 `443`。
5. 将文件权限收紧为 `0600`，配置目录权限为 `0750`。

### 配置值书写格式 [step]

| 格式 | 说明 |
| --- | --- |
| `键 = 值` | 等号两侧的空白可选；注释以 `;` 或 `#` 开头。 |
| 布尔值 | 只接受小写 `true` 或 `false`，其他写法会导致启动失败。 |
| 数值 | 按十进制整数解析。 |
| 字符串 | 一般不需要引号；包含首尾空白或引号等特殊字符时，可使用双引号（支持 `\` 转义）或单引号（仅处理 `\'` 转义）包裹。 |
| 空值 | 等号后留空表示显式空值，例如 `Password =` 表示空密码。 |

同一文件内不允许出现重复的 section 或重复的键。

### 使配置生效 [step]

1. 修改配置并保存。
2. 重启 aBMC 服务：

    ```bash
    sudo systemctl restart aBMC
    ```

## 配置项说明

### [Server] 服务配置

| 配置项 | 默认值 | 说明 |
| --- | --- | --- |
| Host | `0.0.0.0` | 主服务与 Webhook 服务的监听地址，支持 IP 或域名。 |
| HttpPort | `443` | 主服务监听端口，提供 Web 管理页面和 Redfish API；启用 HTTPS 时该端口即 HTTPS 端口。取值范围 `1–65535`。 |
| HttpsMode | `true` | 是否以 HTTPS 提供主服务。 |
| WebHookPort | `23456` | Webhook 服务监听端口。该服务独立于主服务，用于接收 Prometheus Alertmanager 告警并转发企业微信、钉钉机器人消息。 |
| WebShow | `true` | 是否提供 Web 管理前端。`false` 时浏览器访问首页返回空白页，Redfish API 不受影响，适用于仅通过 API 集成的场景。 |

### [Log] 日志配置

| 配置项 | 默认值 | 说明 |
| --- | --- | --- |
| EnableConsole | `true` | 内部事件日志是否输出到控制台。 |
| MinLevel | `info` | 最低日志级别，可选 `debug`、`info`、`warn`、`error`。 |

<Callout title="提示" type="info">
日志级别越低输出越详细。`debug` 会显著增加日志量，一般仅在排查问题时临时启用。
</Callout>

### [Update] 固件升级配置

| 配置项 | 默认值 | 说明 |
| --- | --- | --- |
| UpdateImgPath | `/userdata/aBMC/Firmware` | 固件镜像上传与存放目录，Web/API 上传的镜像保存在此。 |
| UpdateTimeoutMinute | `20` | 单次固件升级的超时时间，单位为分钟。 |
| SupportRockchip | `true` | 控制升级接口的可选平台列表中是否提供 Rockchip。 |
| SupportSophgo | `true` | 控制升级接口的可选平台列表中是否提供 Sophgo（算能）。 |
| SupportNovauto | `true` | 控制升级接口的可选平台列表中是否提供 Novauto。 |
| SupportQualcomm | `true` | 控制升级接口的可选平台列表中是否提供 Qualcomm（高通）。 |
| SupportNvidia | `true` | 控制升级接口的可选平台列表中是否提供 NVIDIA。 |
| SupportSpacemit | `true` | 控制升级接口的可选平台列表中是否提供 Spacemit（进迭）。 |

### [CSA1] 子板私网配置

| 配置项 | 默认值 | 说明 |
| --- | --- | --- |
| PrivateIpv4CIDR | `192.168.150.100/24` | BMC 与 CSA1、B1/B2_BM1684X 等 SOPHGO（算能）机型子板通信使用的私网网段。 |

该配置的使用规则：

1. 取值必须是合法的 IPv4 CIDR，且应填写主机地址形式，例如 `192.168.150.100/24`，而不是网络地址形式 `192.168.150.0/24`。
2. BMC 侧私网地址取 CIDR 中的主机地址，默认为 `192.168.150.100`；子板私网地址按核心板编号映射为 `编号 + 20`，例如 `sub01` 默认对应 `192.168.150.21`。
3. 该网段用于 BMC 私网口配置、子板私网 IP 分配、SSH 通信。
4. GS1-N2 机型不使用该配置，其 BMC 与子板固定通过 `172.22.250.0/16` 网段通信。

### [Redis] 配置

aBMC 使用 Redis 进行内部事件发布订阅和数据缓存，系统默认已内置 Redis 实例。

| 配置项 | 默认值 | 说明 |
| --- | --- | --- |
| Host | `localhost` | Redis 服务器地址，支持 IP 或域名。 |
| Port | `6379` | Redis 服务端口，取值范围 `1–65535`。 |
| Password | 空 | Redis 认证密码；无密码时留空（`Password =`）。 |
| DB | `14` | 使用的 Redis 数据库编号，取值范围 `0–15`。 |

<Callout title="注意" type="warn">
此处配置的是 aBMC 连接 Redis 的客户端参数。修改端口、密码或数据库编号时，必须确保 Redis 服务端同步使用相同配置，否则 aBMC 启动或运行时会连接失败。
</Callout>

## 常见问题 FAQ

### 1.修改配置后 aBMC 服务无法启动

配置在启动时严格校验，任何非法值（端口越界、布尔值拼写错误、日志级别非法、IPv4 CIDR 格式错误等）都会导致启动失败。

### 2.自己添加的配置项重启后消失了

主配置文件由 aBMC 管理，无法识别的 section 和配置项会在启动时被自动清理。

### 3.修改 Redis 端口或密码后服务异常

`[Redis]` 配置是 aBMC 连接 Redis 的客户端参数，需要与 Redis 服务端的实际配置一致。修改后请确认 Redis 服务端已使用相同端口、密码，且数据库编号在 `0–15` 范围内，然后重启 aBMC。

### 4.为什么配置文件中部分配置项是注释状态

未显式配置的项会以注释形式展示其默认值，需要修改时可以先取消注释，再修改其值。
