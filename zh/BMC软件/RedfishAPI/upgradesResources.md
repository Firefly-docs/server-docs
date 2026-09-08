# 升级管理资源

升级管理资源用于查询固件和升级任务、上传本地固件，以及对 BMC 或子板执行固件升级。

<Callout title="认证与权限" type="info">
  本页接口支持 HTTP Basic Auth 和会话 Token。Token 通过请求头 `X-Xsrf-Token` 携带。升级任务、BMC 自升级和分片上传等操作需要 `OemUpgrade` 权限。
</Callout>

## 2 升级操作

### 2.1 查询升级队列 ActionInfo

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/UpdateFwService/Actions/UpdateFwServiceActionInfo` |
| 成功状态码 | `200 OK` |

```bash title="使用 Token 查询升级队列参数"
curl --header 'X-Xsrf-Token: <token>' \
  --header 'Accept: application/json' \
  '<protocol>://<device-ip>:<port>/redfish/v1/UpdateFwService/Actions/UpdateFwServiceActionInfo'
```

#### 响应示例

```json title="200 OK·实机响应"
{
  "@odata.id": "/redfish/v1/UpdateFwService/Actions/UpdateFwServiceActionInfo",
  "@odata.type": "#ActionInfo.v1_1_2.ActionInfo",
  "Id": "UpdateFwServiceActionInfo",
  "Name": "UpdateFwService Action Info",
  "Parameters": [
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "DataType": "String",
      "Name": "ImageURI",
      "Required": true
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "DataType": "StringArray",
      "Name": "Targets",
      "Required": true
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "AllowableValues": [
        "CIFS",
        "FTP",
        "SFTP",
        "HTTP",
        "HTTPS",
        "SCP",
        "TFTP",
        "NFS",
        "LOCAL"
      ],
      "DataType": "String",
      "Name": "TransferProtocol",
      "Required": false
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "AllowableValues": [
        "Rockchip",
        "Novauto",
        "Qualcomm",
        "M_Nvidia",
        "Spacemit"
      ],
      "DataType": "String",
      "Name": "Platform",
      "Required": true
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "DataType": "Boolean",
      "Name": "RewriteMac",
      "Required": false
    }
  ]
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | ActionInfo 资源路径。 |
| `@odata.type` | string | ActionInfo 资源类型。 |
| `Id` | string | ActionInfo 资源标识。 |
| `Name` | string | ActionInfo 资源名称。 |
| `Parameters` | array | 升级队列操作的参数列表。 |
| `Parameters[].DisallowedInput` | boolean | 是否禁止输入该参数。 |
| `Parameters[].AllowablePattern` | string | 参数允许的匹配模式。 |
| `Parameters[].AllowableValues` | array | 参数可选值；仅部分参数返回。 |
| `Parameters[].DataType` | string | 参数数据类型。 |
| `Parameters[].Name` | string | 参数名。 |
| `Parameters[].Required` | boolean | 是否必填。 |

### 2.2 添加升级队列任务

| 项目 | 内容 |
| --- | --- |
| 方法 | `POST` |
| 路径 | `/redfish/v1/UpdateFwService/Actions/UpdateFwService.SimpleUpdate` |
| 所需权限 | `OemUpgrade` |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="local">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="local">本地固件</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="network">网络固件</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="local">
    ```bash title="使用 Token 添加本地升级任务"
    curl --request POST \
      --header 'X-Xsrf-Token: <token>' \
      --header 'Content-Type: application/json' \
      --data '{
        "ImageURI": "<firmware-file-name>",
        "Targets": ["<target-id>"],
        "TransferProtocol": "LOCAL",
        "Platform": "<platform>",
        "RewriteMac": false
      }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/UpdateFwService/Actions/UpdateFwService.SimpleUpdate'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="network">
    ```bash title="使用 Token 添加 NFS 升级任务"
    curl --request POST \
      --header 'X-Xsrf-Token: <token>' \
      --header 'Content-Type: application/json' \
      --data '{
        "ImageURI": "nfs://<server-ip>/<firmware-path>",
        "Targets": ["<target-id>"],
        "TransferProtocol": "NFS",
        "Platform": "<platform>",
        "RewriteMac": false
      }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/UpdateFwService/Actions/UpdateFwService.SimpleUpdate'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 请求字段

| 字段 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `ImageURI` | string | 是 | 固件文件名或网络 URI。具体形式由 `TransferProtocol` 决定。 |
| `Targets` | array | 是 | 升级目标 ID 列表，例如 `bmc` 或 `sub01`。 |
| `TransferProtocol` | string | 否 | 传输协议：`CIFS`、`FTP`、`SFTP`、`HTTP`、`HTTPS`、`SCP`、`TFTP`、`NFS` 或 `LOCAL`。 |
| `Platform` | string | 是 | 目标平台。必须从 2.1 当前实机返回的 `AllowableValues` 中选择。 |
| `RewriteMac` | boolean | 否 | 是否重写 MAC 地址的请求值。当前后端还会根据目标设备是否存在已分配 MAC 地址决定实际行为。 |

#### 响应示例

以下为实机返回的 V1 成功响应。本次使用空 `Targets` 数组验证响应格式，未创建实际升级任务。

```json title="200 OK·实机响应"
{
  "error": {
    "@Message.ExtendedInfo": [
      {
        "@odata.type": "#Message.v1_1_1.Message",
        "Message": "UpdateFwService Interface Collection",
        "MessageArgs": [],
        "MessageId": "Base.1.11.0.Success",
        "MessageSeverity": "OK",
        "Resolution": "None"
      }
    ],
    "code": "code",
    "message": "#Message.v1_1_1.Message"
  }
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `error` | object | V1 兼容响应容器。虽然字段名为 `error`，但内部 `MessageId` 表示成功。 |
| `error.@Message.ExtendedInfo` | array | Redfish Message 详细信息。 |
| `error.@Message.ExtendedInfo[].@odata.type` | string | Redfish Message 类型。 |
| `error.@Message.ExtendedInfo[].Message` | string | 升级队列操作消息。 |
| `error.@Message.ExtendedInfo[].MessageArgs` | array | 消息参数。 |
| `error.@Message.ExtendedInfo[].MessageId` | string | 消息标识；成功时为 `Base.1.11.0.Success`。 |
| `error.@Message.ExtendedInfo[].MessageSeverity` | string | 消息严重程度。 |
| `error.@Message.ExtendedInfo[].Resolution` | string | 后续处理建议。 |
| `error.code` | string | V1 兼容代码，当前固定返回 `code`。 |
| `error.message` | string | V1 兼容消息类型。 |

<Callout title="Targets 不得为空" type="warn">
  当前后端只校验 `Targets` 字段是否存在，空数组也会返回成功，但不会为任何目标添加升级任务。客户端必须在请求前校验数组非空。
</Callout>

### 2.3 查询 BMC 自升级 ActionInfo

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/UpdateFwService/Actions/UpdateFwServiceSelfUpdateActionInfo` |
| 成功状态码 | `200 OK` |

```bash title="使用 Token 查询 BMC 自升级参数"
curl --header 'X-Xsrf-Token: <token>' \
  --header 'Accept: application/json' \
  '<protocol>://<device-ip>:<port>/redfish/v1/UpdateFwService/Actions/UpdateFwServiceSelfUpdateActionInfo'
```

#### 响应示例

```json title="200 OK·实机响应"
{
  "@odata.id": "/redfish/v1/UpdateFwService/Actions/UpdateFwServiceSelfUpdateActionInfo",
  "@odata.type": "#ActionInfo.v1_1_2.ActionInfo",
  "Id": "UpdateFwServiceActionInfo",
  "Name": "UpdateFwService Action Info",
  "Parameters": [
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "DataType": "String",
      "Name": "ImageURI",
      "Required": true
    }
  ]
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | ActionInfo 资源路径。 |
| `@odata.type` | string | ActionInfo 资源类型。 |
| `Id` | string | ActionInfo 资源标识。 |
| `Name` | string | ActionInfo 资源名称。 |
| `Parameters` | array | BMC 自升级参数列表。 |
| `Parameters[].DisallowedInput` | boolean | 是否禁止输入该参数。 |
| `Parameters[].AllowablePattern` | string | 参数允许的匹配模式。 |
| `Parameters[].DataType` | string | 参数数据类型。 |
| `Parameters[].Name` | string | 参数名，当前为 `ImageURI`。 |
| `Parameters[].Required` | boolean | 是否必填。 |

### 2.4 执行 BMC 自升级

| 项目 | 内容 |
| --- | --- |
| 方法 | `POST` |
| 路径 | `/redfish/v1/UpdateFwService/Actions/UpdateFwService.SelfUpdate` |
| 所需权限 | `OemUpgrade` |
| 成功状态码 | `200 OK` |

```bash title="使用 Token 执行 BMC 自升级"
curl --request POST \
  --header 'X-Xsrf-Token: <token>' \
  --header 'Content-Type: application/json' \
  --data '{
    "ImageURI": "<locally-visible-firmware-path>"
  }' \
  '<protocol>://<device-ip>:<port>/redfish/v1/UpdateFwService/Actions/UpdateFwService.SelfUpdate'
```

#### 请求字段

| 字段 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `ImageURI` | string | 是 | BMC 本机可直接访问的固件文件路径。后端会查询文件所在块设备、UUID 和挂载点。 |

#### 成功响应结构

成功时返回与 2.2 相同的 V1 嵌套 Message 结构。由于该操作会写入引导配置并触发 BMC 升级，本次未执行成功路径。

```json title="200 OK·后端成功响应结构"
{
  "error": {
    "@Message.ExtendedInfo": [
      {
        "@odata.type": "#Message.v1_1_1.Message",
        "Message": "UpdateFwService Interface Collection",
        "MessageArgs": [],
        "MessageId": "Base.1.11.0.Success",
        "MessageSeverity": "OK",
        "Resolution": "None"
      }
    ],
    "code": "code",
    "message": "#Message.v1_1_1.Message"
  }
}
```

#### 响应字段

响应字段与 2.2 的“响应字段”相同。

<Callout title="实机失败路径已验证" type="info">
  传入不存在的 `ImageURI` 时，实机返回 `400 Bad Request`，`error.@Message.ExtendedInfo` 为 `the file path is not natively visible`。
</Callout>

<Callout title="高风险操作" type="warn">
  执行升级可能重启目标设备或中断当前服务。提交前必须先查询 ActionInfo，确认镜像、平台、传输协议和目标设备匹配。
</Callout>

## 1 升级状态与固件

### 1.1 查询升级任务列表

查询 BMC 和各子板当前的升级任务状态以及整体进度。

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/UpdateFwService/UpdateFwServiceTasksLists` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 所需权限 | `OemUpgrade` |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="查询升级任务列表"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/UpdateFwService/UpdateFwServiceTasksLists'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询升级任务列表"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/UpdateFwService/UpdateFwServiceTasksLists'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

以下是实机返回的核心字段节选。当前设备还包含 `sub02` 至 `sub10`，它们的结构与 `sub01` 相同。

```json title="200 OK·真实响应节选"
{
  "TasksLists": {
    "bmc": {
      "Target": "",
      "TaskUID": 0,
      "TaskState": {
        "En": "",
        "Zh_CN": ""
      },
      "StartTime": "",
      "TaskPercentage": 0,
      "Message": null
    },
    "sub01": {
      "Target": "",
      "TaskUID": 0,
      "TaskState": {
        "En": "",
        "Zh_CN": ""
      },
      "StartTime": "",
      "TaskPercentage": 0,
      "Message": null
    }
  },
  "TaskPercentage": 0
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `TasksLists` | object | 按目标 ID 组织的升级任务映射。 |
| `TasksLists.{target}` | object | 指定目标的升级任务信息，例如 `bmc` 或 `sub01`。 |
| `TasksLists.{target}.Target` | string | 任务目标 ID；无任务时为空字符串。 |
| `TasksLists.{target}.TaskUID` | integer | 升级任务唯一标识；无任务时为 `0`。 |
| `TasksLists.{target}.TaskState` | object | 多语言任务状态。 |
| `TasksLists.{target}.TaskState.En` | string | 英文任务状态。 |
| `TasksLists.{target}.TaskState.Zh_CN` | string | 中文任务状态。 |
| `TasksLists.{target}.StartTime` | string | 任务开始时间。 |
| `TasksLists.{target}.TaskPercentage` | integer | 指定目标的任务进度，范围为 `0`～`100`。 |
| `TasksLists.{target}.Message` | array \| null | 升级日志消息列表；无任务时可为 `null`。 |
| `TaskPercentage` | integer | 当前所有可计算任务的平均进度。 |

### 1.2 查询指定目标升级状态

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/UpdateFwService/{target-id}/Actions/Oem/Firefly/UpdateFwService.ServiceInfo` |
| 路径参数 | `target-id`，例如 `bmc` 或 `sub01` |
| 所需权限 | `OemUpgrade` |
| 成功状态码 | `200 OK` |

```bash title="使用 Token 查询 BMC 升级状态"
curl --header 'X-Xsrf-Token: <token>' \
  --header 'Accept: application/json' \
  '<protocol>://<device-ip>:<port>/redfish/v1/UpdateFwService/bmc/Actions/Oem/Firefly/UpdateFwService.ServiceInfo'
```

#### 响应示例

```json title="200 OK·实机响应"
{
  "Target": "",
  "TaskUID": 0,
  "TaskState": {
    "En": "",
    "Zh_CN": ""
  },
  "StartTime": "",
  "TaskPercentage": 0,
  "Message": null
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `Target` | string | 升级目标 ID。 |
| `TaskUID` | integer | 升级任务标识。 |
| `TaskState` | object | 多语言任务状态。 |
| `TaskState.En` | string | 英文任务状态。 |
| `TaskState.Zh_CN` | string | 中文任务状态。 |
| `StartTime` | string | 任务开始时间。 |
| `TaskPercentage` | integer | 任务进度，范围为 `0`～`100`。 |
| `Message` | array \| null | 升级日志消息列表。 |

### 1.3 查询本地固件列表

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/UpdateFwService/LocalFirmwareLists` |
| 成功状态码 | `200 OK` |

```bash title="使用 Token 查询本地固件列表"
curl --header 'X-Xsrf-Token: <token>' \
  --header 'Accept: application/json' \
  '<protocol>://<device-ip>:<port>/redfish/v1/UpdateFwService/LocalFirmwareLists'
```

#### 响应示例

```json title="200 OK·实机响应"
{
  "FirmwareLists": [
    {
      "name": "CS-B1-3576-JD4-SUB-MINIMAL_Rk3576_debug_260602.img"
    },
    {
      "name": "CS-B1-rk3576-jd4-sub_Android14_HDMI_260605.img"
    }
  ],
  "Types": null
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `FirmwareLists` | array | BMC 本地固件目录中的文件列表。 |
| `FirmwareLists[].name` | string | 固件文件名，可用于本地升级或删除操作。 |
| `Types` | array \| null | 固件类型列表；当前实现返回 `null`。 |

### 1.4 查询可用升级固件路径

查询升级上传目录中的 `.img` 文件。该接口与 1.3 读取的固件目录不同，两个响应不一定一致。

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/UpdateFwService/Actions/UpdateFwService.FirmwarePath` |
| 所需权限 | `OemUpgrade` |
| 成功状态码 | `200 OK` |

```bash title="使用 Token 查询可用升级固件路径"
curl --header 'X-Xsrf-Token: <token>' \
  --header 'Accept: application/json' \
  '<protocol>://<device-ip>:<port>/redfish/v1/UpdateFwService/Actions/UpdateFwService.FirmwarePath'
```

#### 响应示例

```json title="200 OK·实机响应"
{
  "Id": "UpdateFwServiceActionInfo",
  "Name": "UpdateFwService Action Info",
  "Parameters": []
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `Id` | string | 资源标识。 |
| `Name` | string | 资源名称。 |
| `Parameters` | array | 可用固件列表。每个元素包含 `Path` 和 `Name`；当前实机目录为空。 |
| `Parameters[].Path` | string | 固件文件的绝对路径。 |
| `Parameters[].Name` | string | 固件文件名。 |

### 1.5 删除本地固件

| 项目 | 内容 |
| --- | --- |
| 方法 | `DELETE` |
| 路径 | `/redfish/v1/UpdateFwService/LocalFirmwareLists/{firmware-id}` |
| 路径参数 | `firmware-id`，从 1.3 返回的 `FirmwareLists[].name` 获取 |
| 请求体 | 无 |
| 成功状态码 | `200 OK` |

```bash title="使用 Token 删除本地固件"
curl --request DELETE \
  --header 'X-Xsrf-Token: <token>' \
  '<protocol>://<device-ip>:<port>/redfish/v1/UpdateFwService/LocalFirmwareLists/<firmware-id>'
```

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
| `@odata.type` | string | Redfish Message 类型。 |
| `Timestamp` | string | 服务端生成消息时的时间戳。 |
| `MessageId` | string | 成功消息标识。 |
| `Message` | string | 操作结果描述。 |
| `MessageArgs` | array | 消息参数。 |
| `MessageSeverity` | string | 消息严重程度。 |
| `Severity` | string | 兼容严重程度字段。 |
| `Oem` | object \| null | OEM 扩展信息。 |
| `RelatedProperties` | array \| null | 相关资源属性。 |
| `Resolution` | string | 后续处理建议。 |

<Callout title="删除不可恢复" type="warn">
  接口会直接删除 BMC 上的固件文件，并尝试清理关联的解包缓存。本次仅使用不存在的文件名验证了路由和错误响应，未删除现有固件。
</Callout>
