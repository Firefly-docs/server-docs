# 管理资源

管理资源用于发现设备管理器，并查询、清空或导出 BMC 管理日志。

<Callout title="认证与访问协议" type="info">
  本页接口支持 HTTP Basic Auth 和会话 Token。Token 通过请求头 `X-Xsrf-Token` 携带，不放在请求体中。示例中的 `<protocol>` 根据服务配置填写 `http` 或 `https`。
</Callout>

## 1 管理资源合集

### 1.1 查询管理资源合集

查询当前账号有权访问的管理器资源。

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Managers` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 请求体 | 无 |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询管理资源合集"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Managers'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询管理资源合集"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Managers'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

以下是测试设备返回的真实响应。

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Managers",
  "@odata.type": "#ManagersCollection.ManagersCollection",
  "Name": "Managers Collection",
  "Members@odata.count": 11,
  "Members": [
    { "@odata.id": "/redfish/v1/Managers/bmc" },
    { "@odata.id": "/redfish/v1/Managers/sub01" },
    { "@odata.id": "/redfish/v1/Managers/sub02" },
    { "@odata.id": "/redfish/v1/Managers/sub03" },
    { "@odata.id": "/redfish/v1/Managers/sub04" },
    { "@odata.id": "/redfish/v1/Managers/sub05" },
    { "@odata.id": "/redfish/v1/Managers/sub06" },
    { "@odata.id": "/redfish/v1/Managers/sub07" },
    { "@odata.id": "/redfish/v1/Managers/sub08" },
    { "@odata.id": "/redfish/v1/Managers/sub09" },
    { "@odata.id": "/redfish/v1/Managers/sub10" }
  ]
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | 管理资源合集路径。 |
| `@odata.type` | string | 管理资源合集的 OData 类型。 |
| `Name` | string | 管理资源合集名称。 |
| `Members@odata.count` | number | 当前账号可访问的管理器数量。 |
| `Members` | array | 管理器成员列表。 |
| `Members[].@odata.id` | string | 指定管理器的资源路径。 |

### 1.2 查询指定管理资源

查询指定管理器的状态、时间、关联资源和控制台能力。以下示例使用管理器标识 `bmc`。

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Managers/{ManagerId}` |
| 路径参数 | `ManagerId`：管理器标识，例如 `bmc` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 请求体 | 无 |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询 bmc 管理资源"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Managers/bmc'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询 bmc 管理资源"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Managers/bmc'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

以下为真实响应的核心字段节选。

```json title="200 OK · 真实响应节选"
{
  "@odata.id": "/redfish/v1/Managers/bmc",
  "@odata.type": "#Managers.v1_20_0.Managers",
  "Actions": null,
  "DateTime": "2026-08-05T02:06:31+00:00",
  "DateTimeLocalOffset": "UTC+08:00",
  "Description": "Managers",
  "Id": "bmc",
  "Links": {
    "AccountService": [{ "Id": "/redfish/v1/AccountService" }],
    "Chassis": [{ "Id": "/redfish/v1/Chassis" }],
    "Oem": [{ "Id": "/redfish/v1/Oem" }],
    "Switch": [{ "Id": "/redfish/v1/Switch" }],
    "Systems": [{ "Id": "/redfish/v1/Systems" }],
    "UpdateFwService": [{ "Id": "/redfish/v1/UpdateFwService" }],
    "UpdateFwService_V2": [{ "Id": "/redfish/v1/UpdateFwService_V2" }]
  },
  "Manufacturer": "Firefly",
  "Name": "Computer System Managers",
  "PowerState": "On",
  "SerialConsole": {
    "ConnectTypesSupported": ["Shell"],
    "MaxConcurrentSessions": 1,
    "ServiceEnabled": true
  },
  "Status": {
    "Health": "Critical",
    "HealthRollup": "",
    "State": ""
  },
  "TimeZoneName": "UTC"
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | 指定管理器的资源路径。 |
| `@odata.type` | string | 管理器资源的 OData 类型。 |
| `Actions` | object \| null | 管理器当前公开的操作集合。 |
| `DateTime` | string | 管理器当前日期和时间。 |
| `DateTimeLocalOffset` | string | 管理器报告的本地时区偏移。 |
| `Description` | string | 管理器资源描述。 |
| `Id` | string | 管理器标识。 |
| `Links` | object | 管理器关联资源入口。 |
| `Links.AccountService` | array | 用户账号服务入口列表。 |
| `Links.AccountService[].Id` | string | 用户账号服务路径。 |
| `Links.Chassis` | array | 机箱资源入口列表。 |
| `Links.Chassis[].Id` | string | 机箱资源路径。 |
| `Links.Oem` | array | OEM 资源入口列表。 |
| `Links.Oem[].Id` | string | OEM 资源路径。 |
| `Links.Switch` | array | 交换机资源入口列表。 |
| `Links.Switch[].Id` | string | 交换机资源路径。 |
| `Links.Systems` | array | 系统资源入口列表。 |
| `Links.Systems[].Id` | string | 系统资源路径。 |
| `Links.UpdateFwService` | array | 固件升级服务入口列表。 |
| `Links.UpdateFwService[].Id` | string | 固件升级服务路径。 |
| `Links.UpdateFwService_V2` | array | 第二版固件升级服务入口列表。 |
| `Links.UpdateFwService_V2[].Id` | string | 第二版固件升级服务路径。 |
| `Manufacturer` | string | 管理器制造商。 |
| `Name` | string | 管理器名称。 |
| `PowerState` | string | 管理器电源状态。 |
| `SerialConsole` | object | 串行控制台能力。 |
| `SerialConsole.ConnectTypesSupported` | array | 支持的控制台连接类型。 |
| `SerialConsole.MaxConcurrentSessions` | number | 最大并发控制台会话数。 |
| `SerialConsole.ServiceEnabled` | boolean | 串行控制台服务是否启用。 |
| `Status` | object | 管理器状态。 |
| `Status.Health` | string | 管理器健康状态。 |
| `Status.HealthRollup` | string | 管理器汇总健康状态。 |
| `Status.State` | string | 管理器运行状态。 |
| `TimeZoneName` | string | 管理器报告的时区名称。 |

## 2 日志资源

管理日志分为四种类型。

| `LogServiceId` | 说明 |
| --- | --- |
| `OperateLog` | 管理操作日志。 |
| `RunLog` | 管理服务运行日志。 |
| `SecurityLog` | 登录、鉴权等安全相关日志。 |
| `UpgradeLog` | 固件或软件升级日志。 |

### 2.1 查询日志服务合集

查询指定管理器支持的全部管理日志类型。

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Managers/{ManagerId}/LogServices` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 所需权限 | `OemDebug` |
| 请求体 | 无 |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="查询管理日志服务合集"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Managers/bmc/LogServices'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询管理日志服务合集"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Managers/bmc/LogServices'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK"
{
  "@odata.context": "/redfish/v1/$metadata#Managers/Members/bmc/LogServices/$entity",
  "@odata.id": "/redfish/v1/Managers/bmc/LogServices",
  "@odata.type": "#LogServiceCollection.LogServiceCollection",
  "Members": [
    { "@odata.id": "/redfish/v1/Managers/bmc/LogServices/OperateLog" },
    { "@odata.id": "/redfish/v1/Managers/bmc/LogServices/RunLog" },
    { "@odata.id": "/redfish/v1/Managers/bmc/LogServices/SecurityLog" },
    { "@odata.id": "/redfish/v1/Managers/bmc/LogServices/UpgradeLog" }
  ],
  "Members@odata.count": 4,
  "Name": "LogService Collection"
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.context` | string | 日志服务合集的 OData 元数据上下文。 |
| `@odata.id` | string | 日志服务合集路径。 |
| `@odata.type` | string | 日志服务合集的 OData 类型。 |
| `Members` | array | 日志服务成员列表。 |
| `Members[].@odata.id` | string | 指定日志服务的访问路径。 |
| `Members@odata.count` | number | 日志服务数量。 |
| `Name` | string | 日志服务合集名称。 |

### 2.2 查询指定日志服务

查询指定日志类型的状态、条目入口以及清空和导出操作入口。

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Managers/{ManagerId}/LogServices/{LogServiceId}` |
| 路径参数 | `LogServiceId`：`OperateLog`、`RunLog`、`SecurityLog` 或 `UpgradeLog` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="查询操作日志服务"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Managers/bmc/LogServices/OperateLog'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询操作日志服务"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Managers/bmc/LogServices/OperateLog'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK"
{
  "@odata.context": "/redfish/v1/$metadata#Managers/Members/bmc/LogServices/Members/$entity",
  "@odata.id": "/redfish/v1/Managers/bmc/LogServices/OperateLog",
  "@odata.type": "#LogService.v1_4_0.LogService",
  "Actions": {
    "#LogService.ClearLog": {
      "ClearType@Redfish.AllowableValue": [""],
      "target": "/redfish/v1/Managers/bmc/LogServices/OperateLog/Actions/LogService.ClearLog"
    },
    "Oem": {
      "Firefly": {
        "#LogService.ExportLog": {
          "@Redfish.ActionInfo": "/redfish/v1/Managers/bmc/LogServices/OperateLog/ExportLogActionInfo",
          "target": "/redfish/v1/Managers/bmc/LogServices/OperateLog/Actions/Oem/Firefly/LogService.ExportLog"
        }
      }
    }
  },
  "DateTime": "2026-08-05T10:06:08.292+08:00",
  "DateTimeLocalOffset": "+08:00",
  "Description": "Collection of log services for this manager",
  "Entries": {
    "@odata.id": "/redfish/v1/Managers/bmc/LogServices/OperateLog/Entries"
  },
  "Id": "OperateLog",
  "Name": "System Service Log",
  "OverWritePolicy": "NeverOverWrites",
  "OverWritePolicy@Redfish.AllowableValues": [
    "Unknown",
    "WrapsWhenFull",
    "NeverOverWrites"
  ],
  "ServiceEnabled": true
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.context` | string | 指定日志服务的 OData 元数据上下文。 |
| `@odata.id` | string | 指定日志服务路径。 |
| `@odata.type` | string | 日志服务的 OData 类型。 |
| `Actions` | object | 日志服务支持的操作集合。 |
| `Actions.#LogService.ClearLog` | object | 清空日志操作。 |
| `Actions.#LogService.ClearLog.ClearType@Redfish.AllowableValue` | array | 清空类型允许值；当前返回一个空字符串。 |
| `Actions.#LogService.ClearLog.target` | string | 清空日志的 POST 路径。 |
| `Actions.Oem` | object | OEM 操作集合。 |
| `Actions.Oem.Firefly` | object | Firefly 日志操作集合。 |
| `Actions.Oem.Firefly.#LogService.ExportLog` | object | 导出日志操作。 |
| `Actions.Oem.Firefly.#LogService.ExportLog.@Redfish.ActionInfo` | string | 导出操作的 ActionInfo 路径。 |
| `Actions.Oem.Firefly.#LogService.ExportLog.target` | string | 导出日志的 POST 路径。 |
| `DateTime` | string | 服务端生成响应时的本地日期和时间。 |
| `DateTimeLocalOffset` | string | 本地时间与 UTC 的偏移。 |
| `Description` | string | 日志服务描述。 |
| `Entries` | object | 日志条目合集入口。 |
| `Entries.@odata.id` | string | 日志条目合集路径。 |
| `Id` | string | 日志服务标识。 |
| `Name` | string | 日志服务名称。 |
| `OverWritePolicy` | string | 当前日志覆盖策略。 |
| `OverWritePolicy@Redfish.AllowableValues` | array | 支持的日志覆盖策略。 |
| `ServiceEnabled` | boolean | 日志服务是否启用。 |

### 2.3 查询日志条目合集

分页查询指定日志服务的条目路径。

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Managers/{ManagerId}/LogServices/{LogServiceId}/Entries` |
| 查询参数 | `skip`：从零开始跳过的条目数；默认 `0` |
| 查询参数 | `top`：返回区间的结束序号；省略时使用当前日志总数 |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 成功状态码 | `200 OK` |

<Callout title="top 不是返回数量" type="warn">
  当前后端按 `skip + 1` 到 `top` 的序号区间生成成员。例如 `skip=0&top=3` 返回第 1 至第 3 条；`skip=1&top=3` 只返回第 2 至第 3 条。`top` 当前不是常见分页语义中的“返回数量”。
</Callout>

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="查询前三条操作日志"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Managers/bmc/LogServices/OperateLog/Entries?skip=0&top=3'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询前三条操作日志"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Managers/bmc/LogServices/OperateLog/Entries?skip=0&top=3'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK"
{
  "@odata.context": "/redfish/v1/$metadata#LogEntryCollection.LogEntryCollection",
  "@odata.id": "/redfish/v1/Managers/bmc/LogServices/OperateLog/Entries",
  "@odata.type": "#LogEntryCollection.LogEntryCollection",
  "Description": "Collection of entries for this log service",
  "Members": [
    { "@odata.id": "/redfish/v1/Managers/bmc/LogServices/OperateLog/Entries/1" },
    { "@odata.id": "/redfish/v1/Managers/bmc/LogServices/OperateLog/Entries/2" },
    { "@odata.id": "/redfish/v1/Managers/bmc/LogServices/OperateLog/Entries/3" }
  ],
  "Members@odata.count": 3,
  "Name": "Log Service Entries Collection",
  "Oem": {
    "Firefly": {
      "MaxCount": 30
    }
  }
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.context` | string | 日志条目合集的 OData 元数据上下文。 |
| `@odata.id` | string | 日志条目合集路径；不包含查询字符串。 |
| `@odata.type` | string | 日志条目合集的 OData 类型。 |
| `Description` | string | 日志条目合集描述。 |
| `Members` | array | 当前查询区间内的日志条目列表。 |
| `Members[].@odata.id` | string | 指定日志条目的访问路径。 |
| `Members@odata.count` | number | 当前响应实际返回的成员数量。 |
| `Name` | string | 日志条目合集名称。 |
| `Oem` | object | OEM 扩展信息。 |
| `Oem.Firefly` | object | Firefly 日志扩展信息。 |
| `Oem.Firefly.MaxCount` | number | 指定日志服务当前保存的日志总数。 |

### 2.4 查询指定日志条目

根据条目序号查询一条管理日志的内容。

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Managers/{ManagerId}/LogServices/{LogServiceId}/Entries/{EntryId}` |
| 路径参数 | `EntryId`：从日志条目合集的 `Members[].@odata.id` 获取 |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="查询第 1 条操作日志"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Managers/bmc/LogServices/OperateLog/Entries/1'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询第 1 条操作日志"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Managers/bmc/LogServices/OperateLog/Entries/1'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

以下响应来自真实日志条目，其中客户端地址已替换为占位符。

```json title="200 OK · 客户端地址已脱敏"
{
  "@odata.context": "/redfish/v1/$metadata#Managers/Members/bmc/LogServices/OperateLog/Entries/Members/$entity",
  "@odata.id": "/redfish/v1/Managers/bmc/LogServices/OperateLog/Entries/1",
  "@odata.type": "#LogEntry.v1_1_4.LogEntry",
  "Created": "2026-05-14T10:22:16.795+08:00",
  "Description": "Manager OperateLog Log",
  "EventTimestamp": "2026-05-14T10:22:16.795+08:00",
  "Id": "1",
  "Message": "访问：/redfish/v1/UpdateFwService/Actions/UpdateFwService.SimpleUpdate；操作：控制 SimpleUpdate_Queue fail；原因：The property 'ImageURI' is a required property and must be included in the request.",
  "Name": "Log Entry 1",
  "Oem": {
    "Firefly": {
      "Client": "<client-ip>",
      "Interface": "LAN",
      "User": ""
    }
  },
  "Severity": "Critical"
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.context` | string | 日志条目的 OData 元数据上下文。 |
| `@odata.id` | string | 指定日志条目路径。 |
| `@odata.type` | string | 日志条目的 OData 类型。 |
| `Created` | string | 日志记录创建时间。 |
| `Description` | string | 日志条目描述。 |
| `EventTimestamp` | string | 日志事件发生时间。 |
| `Id` | string | 当前查询中的日志条目序号。 |
| `Message` | string | 日志消息正文。 |
| `Name` | string | 日志条目名称。 |
| `Oem` | object | OEM 扩展信息。 |
| `Oem.Firefly` | object | Firefly 日志扩展信息。 |
| `Oem.Firefly.Client` | string | 发起相关请求的客户端地址。 |
| `Oem.Firefly.Interface` | string | 请求来源接口；当前固定返回 `LAN`。 |
| `Oem.Firefly.User` | string | 发起相关请求的用户名。 |
| `Severity` | string | 日志严重级别，可能为 `OK`、`Warning` 或 `Critical`。 |

### 2.5 清空日志

永久删除指定管理器和日志类型下的全部记录。

| 项目 | 内容 |
| --- | --- |
| 方法 | `POST` |
| 路径 | `/redfish/v1/Managers/{ManagerId}/LogServices/{LogServiceId}/Actions/LogService.ClearLog` |
| 请求体 | 空 JSON 对象 `{}` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 所需权限 | `OemDebug` |
| 成功状态码 | `200 OK` |

<Callout title="不可恢复操作" type="warn">
  该接口会从数据库中永久删除指定类型的全部日志，并整理日志数据库空间。执行前应先通过导出接口备份日志。本次实机验证未执行清空操作。
</Callout>

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="清空操作日志"
    curl --request POST \
      --user '<username>:<password>' \
      --header 'Content-Type: application/json' \
      --data '{}' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Managers/bmc/LogServices/OperateLog/Actions/LogService.ClearLog'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="使用 Token 清空操作日志"
    curl --request POST \
      --header 'X-Xsrf-Token: <token>' \
      --header 'Content-Type: application/json' \
      --data '{}' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Managers/bmc/LogServices/OperateLog/Actions/LogService.ClearLog'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK"
{
  "@odata.type": "#Message.v1_1_1.Message",
  "Timestamp": "1785836138",
  "MessageId": "Base.1.11.0.Success",
  "Message": "The request completed successfully.",
  "MessageArgs": [],
  "MessageSeverity": "OK",
  "Severity": "",
  "Oem": null,
  "RelatedProperties": null,
  "Resolution": "None"
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.type` | string | Redfish 消息资源的 OData 类型。 |
| `Timestamp` | string | 服务端生成响应时记录的时间戳。 |
| `MessageId` | string | 消息标识，成功时为 `Base.1.11.0.Success`。 |
| `Message` | string | 请求处理结果说明。 |
| `MessageArgs` | array | 消息格式化参数列表；本响应为空数组。 |
| `MessageSeverity` | string | 消息严重级别，成功时为 `OK`。 |
| `Severity` | string | 兼容保留的严重级别字段；本响应为空字符串。 |
| `Oem` | object \| null | OEM 扩展消息；本响应为 `null`。 |
| `RelatedProperties` | array \| null | 与消息相关的属性路径；本响应为 `null`。 |
| `Resolution` | string | 建议的处理方式；成功时为 `None`。 |

### 2.7 导出日志

将指定日志类型导出为 Microsoft Excel 2007+ 格式的 `.xlsx` 文件。

| 项目 | 内容 |
| --- | --- |
| 方法 | `POST` |
| 路径 | `/redfish/v1/Managers/{ManagerId}/LogServices/{LogServiceId}/Actions/Oem/Firefly/LogService.ExportLog` |
| ActionInfo | `/redfish/v1/Managers/{ManagerId}/LogServices/{LogServiceId}/ExportLogActionInfo` |
| 请求体 | 空请求体 |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 所需权限 | `OemDebug` |
| 成功状态码 | `200 OK` |
| 响应类型 | XLSX 二进制文件 |

<Callout title="必须发送 Content-Length" type="warn">
  当前代理会拒绝没有 `Content-Length` 的 POST，并返回 `502 POST or PUT without Content-Length`。curl 示例使用 `--data ''` 发送长度为零的请求体，确保请求包含 `Content-Length: 0`。
</Callout>

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="导出操作日志"
    curl --request POST \
      --user '<username>:<password>' \
      --header 'Content-Type: application/json' \
      --data '' \
      --output 'bmc_OperateLog.xlsx' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Managers/bmc/LogServices/OperateLog/Actions/Oem/Firefly/LogService.ExportLog'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="使用 Token 导出操作日志"
    curl --request POST \
      --header 'X-Xsrf-Token: <token>' \
      --header 'Content-Type: application/json' \
      --data '' \
      --output 'bmc_OperateLog.xlsx' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Managers/bmc/LogServices/OperateLog/Actions/Oem/Firefly/LogService.ExportLog'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

实机使用 Basic Auth 和 Token 均返回 `200 OK`，下载文件通过 ZIP 结构检查，可识别为 Microsoft Excel 2007+ 文件。以下为 Basic Auth 验证时的真实响应头：

```http title="200 OK"
Content-Disposition: attachment;filename=bmc_OperateLog.xlsx
Content-Type: application/vnd.openxmlformats-officedocument.spreadsheetml.sheet
Content-Length: 7561
```

#### 响应字段

| 响应头 | 类型 | 说明 |
| --- | --- | --- |
| `Content-Disposition` | string | 下载文件名，格式为 `{ManagerId}_{LogServiceId}.xlsx`。 |
| `Content-Type` | string | XLSX 文件的媒体类型。 |
| `Content-Length` | number | 本次导出文件的字节数，随日志内容变化。 |

<Callout title="导出文件字段" type="info">
  XLSX 的首行包含 `ID`、`CreatedAt`、`Level`、`caller`、`msg`、`type`、`log_id` 和 `corename` 八列。导出操作会新增操作日志，因此再次查询日志总数时可能比导出前增加。
</Callout>
