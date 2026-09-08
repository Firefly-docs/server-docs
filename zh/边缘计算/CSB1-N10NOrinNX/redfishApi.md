# Redfish API

Redfish API 是 BMC 对外提供的资源管理接口。客户端通过 HTTP 或 HTTPS 访问统一的资源路径，使用 JSON 读取设备状态、查询资源关系，并执行配置、控制和维护操作。

本文档描述当前 Firefly BMC 后端已经实现的接口。接口路径、字段和行为以对应版本的后端实现及设备实际响应为准。

<Callout title="建议的阅读顺序" type="info">
  首次接入时，建议依次阅读“接口通用说明”“使用方式”和“基本资源”，再进入具体资源章节。
</Callout>

## 1 资源模型

Redfish 将 BMC 能力组织为可发现的资源。每个资源使用固定 URL 标识，响应中的 `@odata.id` 用于指向当前资源或关联资源。

### 1.1 服务入口

客户端通常从以下两个入口开始发现资源：

| 路径 | 作用 |
| --- | --- |
| `/redfish` | 查询服务支持的 Redfish 主版本入口。 |
| `/redfish/v1` | 查询 Service Root，以及系统、管理、用户、OEM 等资源入口。 |

典型访问顺序如下：

```text
/redfish
└── /redfish/v1
    ├── /redfish/v1/Systems
    ├── /redfish/v1/Managers
    ├── /redfish/v1/AccountService
    ├── /redfish/v1/Oem
    ├── /redfish/v1/Switch
    └── /redfish/v1/UpdateFwService
```

<Callout title="以实际入口为准" type="info">
  Service Root 中的资源入口由后端按设备能力注册。不同产品、板卡状态或软件版本返回的入口可能不同，客户端应优先跟随响应中的 `@odata.id`，不要自行拼接未返回的资源路径。
</Callout>

### 1.2 资源、集合与操作

接口主要分为三类：

| 类型 | 说明 | 常见形式 |
| --- | --- | --- |
| 资源集合 | 返回同类资源的成员列表和数量。 | `Members`、`Members@odata.count` |
| 单个资源 | 返回指定设备、服务或配置对象的完整信息。 | `/Systems/{id}` |
| 操作接口 | 执行配置、启停、重启、删除或其他动作。 | `Actions`、`ActionInfo`、POST |

资源响应中的 `Actions` 描述可执行操作，`@Redfish.ActionInfo` 指向该操作的参数定义。调用写操作前，应先查看对应章节的请求字段、允许值和影响范围。

## 2 文档范围

### 2.1 资源章节

本手册按业务资源组织接口：

| 章节 | 内容 |
| --- | --- |
| [Basic资源](basicResources.md) | Redfish 版本入口和 Service Root。 |
| [Chassis资源](chassisResources.md) | 机箱状态、风扇、UID 和总电源管理。 |
| [Systems资源](systemResources.md) | 系统状态、网络、时间、磁盘、电源、端口映射和服务管理。 |
| [Manage资源](managerResources.md) | BMC 管理资源和日志资源。 |
| [OEM 资源](oemResources.md) | 远程管理和告警等 Firefly 扩展能力。 |
| [Users资源](accountResources.md) | 角色、账号和用户服务管理。 |
| [Switchs资源](switchResources.md) | 交换机二层、三层、VLAN 和聚合管理。 |
| [Upgrades管理资源](upgradesResources.md) | 固件查询、升级状态和升级操作。 |

### 2.2 实现与标准的关系

当前接口沿用 Redfish 的资源、OData 元数据和 Message 响应结构，同时包含 Firefly OEM 字段和产品能力扩展。因此：

- 不应仅根据 Redfish 标准版本推断某个接口一定存在。
- 不应忽略 `Oem`、`Actions` 或 `ActionInfo` 中的实现信息。
- 同一接口在不同设备上的标识、数量、状态和可选参数可能不同。
- 文档中的响应示例用于说明真实结构，不代表所有设备都会返回相同的业务值。

## 3 接入原则

### 3.1 认证与传输

受保护接口支持 HTTP Basic Auth 或会话 Token。Token 通过 `X-Xsrf-Token` 请求头携带，不放入查询参数或业务请求体。

服务可配置为 HTTP 或 HTTPS。文档统一使用以下地址格式：

```text
<protocol>://<device-ip>:<port>
```

其中 `<protocol>` 根据设备配置填写 `http` 或 `https`。端口号不能单独用于判断协议。

### 3.2 操作安全

执行电源、网络、账号、升级、删除等写操作前，应先查询当前资源和对应 `ActionInfo`，确认目标资源、必填字段及允许值。网络配置可能导致当前连接中断，升级和电源操作可能影响业务运行，应在维护窗口执行。

<Callout title="不要在文档或日志中保存凭据" type="warn">
  示例中的用户名、密码和 Token 均为占位符。真实密码和 Token 不应写入文档、命令历史、日志或版本库。
</Callout>
## 详细文档

如需了解 Redfish API 的资源路径、请求参数、响应结构和调用示例，请参阅 [aBMC Redfish API 使用手册](/docs/server/bmc-software/RedfishAPI/oiverview)。