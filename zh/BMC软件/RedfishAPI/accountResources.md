# 用户资源

用户资源用于管理 BMC 本地账号、角色权限和密码策略。

<Callout title="认证与权限" type="info">
  本页接口支持 HTTP Basic Auth 和会话 Token。Token 通过请求头 `X-Xsrf-Token` 携带。查询或管理其他账号时，当前用户通常需要 `ConfigureUsers` 权限。
</Callout>

## 1 角色管理资源

当前实现使用账号的 `Role` 字段区分角色，并通过 `/Privileges` 资源查询或配置具体权限。

<Callout title="Roles 资源当前不可用" type="warn">
  `/redfish/v1/AccountService` 响应中包含 `/redfish/v1/AccountService/Roles` 和 `/redfish/v1/AccountService/PrivilegeMap` 链接，但当前后端没有注册这两组路由。实机请求会返回 `404 page not found`，客户端不应继续跟随这两个链接。
</Callout>

### 1.1 查询角色可用权限

查询 `Administrator` 和 `CommonUser` 角色可配置的权限参数。管理员角色返回功能权限，普通用户角色返回可访问的板卡核心 ID。

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/AccountService/Privileges/Help` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 请求体 | 无 |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="查询角色可用权限"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/AccountService/Privileges/Help'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询角色可用权限"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/AccountService/Privileges/Help'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK·结构示例"
{
  "Parameters": {
    "Administrator": [
      "Login",
      "ConfigureComponents",
      "ConfigureSelf",
      "ConfigureUsers",
      "OemConfigureRemote",
      "OemConfigureNetwork",
      "OemConfigureFirewall",
      "OemPowerControl",
      "OemUpgrade",
      "OemDebug",
      "OemConfigureNtp"
    ],
    "CommonUser": [
      "<core-id>"
    ]
  }
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `Parameters` | object | 按角色分类的可用权限。 |
| `Parameters.Administrator` | array | 管理员可配置的功能权限字符串列表。 |
| `Parameters.CommonUser` | array | 当前设备上可分配给普通用户的板卡核心 ID 列表。 |

### 1.2 查询账号权限合集

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/AccountService/Privileges` |
| 成功状态码 | `200 OK` |

```bash title="使用 Token 查询账号权限合集"
curl --header 'X-Xsrf-Token: <token>' \
  --header 'Accept: application/json' \
  '<protocol>://<device-ip>:<port>/redfish/v1/AccountService/Privileges'
```

#### 响应示例

```json title="200 OK"
{
  "@odata.context": "/redfish/v1/$metadata#ManagerPrivilegeCollection.ManagerPrivilegeCollection",
  "@odata.id": "/redfish/v1/AccountService/Privileges",
  "@odata.type": "#ManagerPrivilegeCollection.ManagerPrivilegeCollection",
  "Members": [
    {
      "@odata.id": "/redfish/v1/AccountService/Privileges/admin"
    }
  ],
  "Members@odata.count": 1,
  "Name": "Privilege Collection"
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.context` | string | 权限合集的 OData 元数据上下文。 |
| `@odata.id` | string | 权限合集路径。 |
| `@odata.type` | string | 权限合集类型。 |
| `Members` | array | 账号权限资源链接列表。 |
| `Members[].@odata.id` | string | 指定账号的权限资源路径。 |
| `Members@odata.count` | integer | 权限资源数量。 |
| `Name` | string | 权限合集名称。 |

### 1.3 查询指定账号权限

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/AccountService/Privileges/{account-id}` |
| 路径参数 | `account-id`，账号名 |
| 成功状态码 | `200 OK` |

```bash title="使用 Token 查询指定账号权限"
curl --header 'X-Xsrf-Token: <token>' \
  --header 'Accept: application/json' \
  '<protocol>://<device-ip>:<port>/redfish/v1/AccountService/Privileges/<account-id>'
```

#### 响应示例

```json title="200 OK·Administrator"
{
  "@odata.context": "/redfish/v1/$metadata#ManagerPrivilege.ManagerPrivilege",
  "@odata.id": "/redfish/v1/AccountService/Privileges/<account-id>",
  "@odata.type": "#ManagerPrivilege.v1_0_0.ManagerPrivilege",
  "AssignedPrivileges": [
    "Login",
    "ConfigureSelf",
    "ConfigureUsers"
  ],
  "Description": "Privilege",
  "Name": "Privilege",
  "Oem": {
    "Firefly": {
      "Role": "Administrator",
      "UserName": "<account-id>"
    }
  }
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.context` | string | 账号权限资源的 OData 上下文。 |
| `@odata.id` | string | 当前账号权限资源路径。 |
| `@odata.type` | string | 账号权限资源类型。 |
| `AssignedPrivileges` | array \| null | 已分配权限。`Administrator` 返回功能权限，`CommonUser` 返回板卡核心 ID，未分配时可为 `null`。 |
| `Description` | string | 资源描述。 |
| `Name` | string | 资源名称。 |
| `Oem` | object | OEM 扩展信息。 |
| `Oem.Firefly` | object | Firefly 账号权限信息。 |
| `Oem.Firefly.Role` | string | 账号角色。 |
| `Oem.Firefly.UserName` | string | 账号名。 |

### 1.4 配置指定账号权限

`Administrator` 账号传入功能权限列表；`CommonUser` 账号传入板卡核心 ID 列表。请先调用 1.1 获取当前可用值。

| 项目 | 内容 |
| --- | --- |
| 方法 | `PATCH` |
| 路径 | `/redfish/v1/AccountService/Privileges/{account-id}` |
| 请求体 | JSON 字符串数组 |
| 成功状态码 | `200 OK` |

```bash title="使用 Token 配置普通用户可访问板卡"
curl --request PATCH \
  --header 'X-Xsrf-Token: <token>' \
  --header 'Content-Type: application/json' \
  --data '["<core-id>"]' \
  '<protocol>://<device-ip>:<port>/redfish/v1/AccountService/Privileges/<account-id>'
```

#### 请求字段

| 字段 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| 请求体 | array | 是 | 权限字符串列表。数组元素必须来自 1.1 返回的对应角色列表。 |

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
| `Timestamp` | string | 服务端生成消息时的时间戳字符串。 |
| `MessageId` | string | 消息标识；成功时为 `Base.1.11.0.Success`。 |
| `Message` | string | 操作结果描述。 |
| `MessageArgs` | array | 消息格式化参数；成功时为空数组。 |
| `MessageSeverity` | string | 消息严重程度；成功时为 `OK`。 |
| `Severity` | string | 兼容严重程度字段。 |
| `Oem` | object \| null | OEM 扩展信息。 |
| `RelatedProperties` | array \| null | 与消息相关的资源属性。 |
| `Resolution` | string | 后续处理建议。 |

## 2 账号管理资源

### 2.1 查询账号合集

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/AccountService/Accounts` |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="查询账号合集"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/AccountService/Accounts'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询账号合集"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/AccountService/Accounts'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK"
{
  "@odata.context": "/redfish/v1/$metadata#ManagerAccountCollection.ManagerAccountCollection",
  "@odata.id": "/redfish/v1/AccountService/Accounts",
  "@odata.type": "#ManagerAccountCollection.ManagerAccountCollection",
  "Members": [
    {
      "@odata.id": "/redfish/v1/AccountService/Accounts/admin"
    }
  ],
  "Members@odata.count": 1,
  "Name": "Accounts Collection"
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.context` | string | 账号合集的 OData 上下文。 |
| `@odata.id` | string | 账号合集路径。 |
| `@odata.type` | string | 账号合集类型。 |
| `Members` | array | 账号资源链接列表。 |
| `Members[].@odata.id` | string | 指定账号的资源路径。 |
| `Members@odata.count` | integer | 账号数量。 |
| `Name` | string | 账号合集名称。 |

### 2.2 查询指定账号

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/AccountService/Accounts/{account-id}` |
| 成功状态码 | `200 OK` |

```bash title="使用 Token 查询指定账号"
curl --header 'X-Xsrf-Token: <token>' \
  --header 'Accept: application/json' \
  '<protocol>://<device-ip>:<port>/redfish/v1/AccountService/Accounts/<account-id>'
```

#### 响应示例

```json title="200 OK·实机结构"
{
  "@odata.context": "/redfish/v1/$metadata#ManagerAccount.ManagerAccount",
  "@odata.id": "/redfish/v1/AccountService/Accounts/<account-id>",
  "@odata.type": "#ManagerAccount.v1_10_0.ManagerAccount",
  "CreateTime": "2026-08-05T13:20:59.197+08:00",
  "Description": "User Account",
  "Email": "user@example.com",
  "Enabled": true,
  "Locked": false,
  "Name": "User Account",
  "Password": "",
  "RoleId": "CommonUser",
  "UpdateTime": "2026-08-05T13:20:59.197+08:00",
  "UserName": "<account-id>"
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.context` | string | 账号资源的 OData 上下文。 |
| `@odata.id` | string | 当前账号资源路径。 |
| `@odata.type` | string | 账号资源类型。 |
| `CreateTime` | string | 账号创建时间。 |
| `Description` | string | 账号资源描述。 |
| `Email` | string | 账号邮箱。 |
| `Enabled` | boolean | 账号是否启用。 |
| `Locked` | boolean | 账号是否锁定。 |
| `Name` | string | 账号资源名称。 |
| `Password` | string | 密码字段。查询时固定返回空字符串，不会返回密码或密码哈希。 |
| `RoleId` | string | 账号角色，例如 `Administrator` 或 `CommonUser`。 |
| `UpdateTime` | string | 账号最后更新时间。 |
| `UserName` | string | 账号名。 |

### 2.3 创建账号

| 项目 | 内容 |
| --- | --- |
| 方法 | `POST` |
| 路径 | `/redfish/v1/AccountService/Accounts` |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="创建账号"
    curl --request POST \
      --user '<username>:<password>' \
      --header 'Content-Type: application/json' \
      --data '{
        "UserName": "api-user",
        "Password": "<new-password>",
        "Role": "CommonUser",
        "Email": "user@example.com",
        "Locked": false,
        "Enabled": true
      }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/AccountService/Accounts'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="使用 Token 创建账号"
    curl --request POST \
      --header 'X-Xsrf-Token: <token>' \
      --header 'Content-Type: application/json' \
      --data '{
        "UserName": "api-user",
        "Password": "<new-password>",
        "Role": "CommonUser",
        "Email": "user@example.com",
        "Locked": false,
        "Enabled": true
      }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/AccountService/Accounts'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 请求字段

| 字段 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `UserName` | string | 是 | 新账号名，不能与已有账号重复。 |
| `Password` | string | 是 | 新账号密码，必须满足当前用户服务的长度和复杂度策略。 |
| `Role` | string | 是 | 账号角色。当前创建参数支持 `Administrator` 和 `CommonUser`。 |
| `Email` | string | 是 | 账号邮箱。 |
| `Locked` | boolean | 是 | 初始是否锁定。 |
| `Enabled` | boolean | 是 | 初始是否启用。 |

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
| `Timestamp` | string | 服务端生成消息时的时间戳字符串。 |
| `MessageId` | string | 消息标识；成功时为 `Base.1.11.0.Success`。 |
| `Message` | string | 操作结果描述。 |
| `MessageArgs` | array | 消息格式化参数；成功时为空数组。 |
| `MessageSeverity` | string | 消息严重程度；成功时为 `OK`。 |
| `Severity` | string | 兼容严重程度字段。 |
| `Oem` | object \| null | OEM 扩展信息。 |
| `RelatedProperties` | array \| null | 与消息相关的资源属性。 |
| `Resolution` | string | 后续处理建议。 |

### 2.4 修改指定账号

可修改账号的密码、角色、邮箱、启用状态和锁定状态。接口不允许修改用户名。

| 项目 | 内容 |
| --- | --- |
| 方法 | `PATCH` |
| 路径 | `/redfish/v1/AccountService/Accounts/{account-id}` |
| 成功状态码 | `200 OK` |

```bash title="使用 Token 修改账号"
curl --request PATCH \
  --header 'X-Xsrf-Token: <token>' \
  --header 'Content-Type: application/json' \
  --data '{
    "Email": "updated@example.com",
    "Enabled": true,
    "Locked": false
  }' \
  '<protocol>://<device-ip>:<port>/redfish/v1/AccountService/Accounts/<account-id>'
```

#### 请求字段

| 字段 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `Password` | string | 否 | 新密码。修改后该账号现有会话会被撤销。 |
| `Role` | string | 否 | 新角色。角色变更时，不适用于新角色的旧权限会被清理。 |
| `Email` | string | 否 | 新邮箱。当前实现不使用空字符串清空邮箱。 |
| `Locked` | boolean | 否 | 是否锁定账号。 |
| `Enabled` | boolean | 否 | 是否启用账号。 |
| `UserName` | string | 禁止 | 用户名由 URL 中的 `account-id` 确定；请求体中不得传入非空 `UserName`。 |

<Callout title="修改自身账号的限制" type="warn">
  当前账号修改自身时，只能修改密码或邮箱；不允许修改自身角色、`Enabled` 或 `Locked`。
</Callout>

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
| `Timestamp` | string | 服务端生成消息时的时间戳字符串。 |
| `MessageId` | string | 消息标识；成功时为 `Base.1.11.0.Success`。 |
| `Message` | string | 操作结果描述。 |
| `MessageArgs` | array | 消息格式化参数；成功时为空数组。 |
| `MessageSeverity` | string | 消息严重程度；成功时为 `OK`。 |
| `Severity` | string | 兼容严重程度字段。 |
| `Oem` | object \| null | OEM 扩展信息。 |
| `RelatedProperties` | array \| null | 与消息相关的资源属性。 |
| `Resolution` | string | 后续处理建议。 |

### 2.5 删除指定账号

| 项目 | 内容 |
| --- | --- |
| 方法 | `DELETE` |
| 路径 | `/redfish/v1/AccountService/Accounts/{account-id}` |
| 请求体 | 无 |
| 成功状态码 | `200 OK` |

```bash title="使用 Token 删除账号"
curl --request DELETE \
  --header 'X-Xsrf-Token: <token>' \
  '<protocol>://<device-ip>:<port>/redfish/v1/AccountService/Accounts/<account-id>'
```

<Callout title="内置账号不可删除" type="warn">
  后端会拒绝删除保留的内置账号。删除账号后，该账号的现有会话也会失效。
</Callout>

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
| `Timestamp` | string | 服务端生成消息时的时间戳字符串。 |
| `MessageId` | string | 消息标识；成功时为 `Base.1.11.0.Success`。 |
| `Message` | string | 操作结果描述。 |
| `MessageArgs` | array | 消息格式化参数；成功时为空数组。 |
| `MessageSeverity` | string | 消息严重程度；成功时为 `OK`。 |
| `Severity` | string | 兼容严重程度字段。 |
| `Oem` | object \| null | OEM 扩展信息。 |
| `RelatedProperties` | array \| null | 与消息相关的资源属性。 |
| `Resolution` | string | 后续处理建议。 |

### 2.6 查询账号请求参数

查询后端生成的账号参数帮助信息。

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/AccountService/Accounts/Help` |
| 成功状态码 | `200 OK` |

```bash title="使用 Token 查询账号请求参数"
curl --header 'X-Xsrf-Token: <token>' \
  --header 'Accept: application/json' \
  '<protocol>://<device-ip>:<port>/redfish/v1/AccountService/Accounts/Help'
```

#### 响应示例

```json title="200 OK·当前实现"
{
  "Parameters": [
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "DataType": "String",
      "Name": "Enabled",
      "Required": true
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "DataType": "String",
      "Name": "Enabled",
      "Required": true
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "AllowableValues": [
        "Administrator",
        "CommonUser"
      ],
      "DataType": "String",
      "Name": "Role",
      "Required": true
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "DataType": "String",
      "Name": "Email",
      "Required": true
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "DataType": "Boolean",
      "Name": "Locked",
      "Required": true
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "DataType": "Boolean",
      "Name": "Enabled",
      "Required": true
    }
  ]
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `Parameters` | array | 账号请求参数元数据列表。 |
| `Parameters[].DisallowedInput` | boolean | 是否禁止输入该参数。 |
| `Parameters[].AllowablePattern` | string | 允许值的匹配模式。 |
| `Parameters[].AllowableValues` | array | 可选值列表；仅部分参数返回。 |
| `Parameters[].DataType` | string | 参数数据类型。 |
| `Parameters[].Name` | string | 参数名。 |
| `Parameters[].Required` | boolean | 是否必填。 |

<Callout title="Help 响应的已知问题" type="warn">
  当前 `Help` 响应中前两个字符串参数被错误标记为 `Enabled`，实际创建账号请求字段为 `UserName` 和 `Password`。调用时应以 2.3 的请求字段表为准。
</Callout>

## 3 用户服务资源

### 3.1 查询用户服务配置

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/AccountService` |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="查询用户服务配置"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/AccountService'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询用户服务配置"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/AccountService'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK·实机响应"
{
  "@odata.context": "/redfish/v1/$metadata#AccountService",
  "@odata.id": "/redfish/v1/AccountService",
  "@odata.type": "#AccountService.v1_12_0.AccountService",
  "AccountLockoutDuration": 5,
  "AccountLockoutThreshold": 5,
  "Accounts": {
    "@odata.id": "/redfish/v1/AccountService/Accounts"
  },
  "Id": "AccountService",
  "MaxPasswordLength": 20,
  "MinPasswordLength": 5,
  "Name": "Account Service",
  "Oem": {
    "PasswordComplexityCheckEnabled": false,
    "PasswordComplexitynumber": false,
    "PasswordHistoryRecords": true,
    "PasswordLowercaseletters": false,
    "PasswordSpecialcharacters": false,
    "PasswordUppercaseletters": false
  },
  "PrivilegeMap": {
    "@odata.id": "/redfish/v1/AccountService/PrivilegeMap"
  },
  "Roles": {
    "@odata.id": "/redfish/v1/AccountService/Roles"
  }
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.context` | string | AccountService 的 OData 上下文。 |
| `@odata.id` | string | 用户服务资源路径。 |
| `@odata.type` | string | AccountService 资源类型。 |
| `AccountLockoutDuration` | integer | 达到锁定阈值后的锁定持续时间。 |
| `AccountLockoutThreshold` | integer | 触发账号锁定的失败尝试次数。 |
| `Accounts` | object | 账号合集资源链接。 |
| `Accounts.@odata.id` | string | 账号合集路径。 |
| `Id` | string | 资源标识。 |
| `MaxPasswordLength` | integer | 密码最大长度。 |
| `MinPasswordLength` | integer | 密码最小长度。 |
| `Name` | string | 资源名称。 |
| `Oem` | object | Firefly 密码策略扩展。 |
| `Oem.PasswordComplexityCheckEnabled` | boolean | 是否启用密码复杂度检查。 |
| `Oem.PasswordComplexitynumber` | boolean | 密码是否必须包含数字。 |
| `Oem.PasswordHistoryRecords` | boolean | 是否启用密码历史记录策略。 |
| `Oem.PasswordLowercaseletters` | boolean | 密码是否必须包含小写字母。 |
| `Oem.PasswordSpecialcharacters` | boolean | 密码是否必须包含特殊字符。 |
| `Oem.PasswordUppercaseletters` | boolean | 密码是否必须包含大写字母。 |
| `PrivilegeMap` | object | 权限映射资源链接；当前链接目标未注册。 |
| `PrivilegeMap.@odata.id` | string | 权限映射资源路径。 |
| `Roles` | object | 角色合集资源链接；当前链接目标未注册。 |
| `Roles.@odata.id` | string | 角色合集资源路径。 |

### 3.2 配置用户服务

修改密码长度、账号锁定和密码复杂度策略。该接口支持部分更新，仅需传入要修改的字段。

| 项目 | 内容 |
| --- | --- |
| 方法 | `PATCH` |
| 路径 | `/redfish/v1/AccountService` |
| 成功状态码 | `200 OK` |

```bash title="使用 Token 配置用户服务"
curl --request PATCH \
  --header 'X-Xsrf-Token: <token>' \
  --header 'Content-Type: application/json' \
  --data '{
    "MinPasswordLength": 8,
    "MaxPasswordLength": 20,
    "AccountLockoutThreshold": 5,
    "AccountLockoutDuration": 5,
    "Oem": {
      "PasswordHistoryRecords": true,
      "PasswordComplexityCheckEnabled": true,
      "PasswordUppercaseletters": true,
      "PasswordLowercaseletters": true,
      "PasswordComplexitynumber": true,
      "PasswordSpecialcharacters": true
    }
  }' \
  '<protocol>://<device-ip>:<port>/redfish/v1/AccountService'
```

#### 请求字段

| 字段 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `MinPasswordLength` | integer | 否 | 密码最小长度。 |
| `MaxPasswordLength` | integer | 否 | 密码最大长度。 |
| `AccountLockoutThreshold` | integer | 否 | 触发账号锁定的失败尝试次数。 |
| `AccountLockoutDuration` | integer | 否 | 账号锁定持续时间。 |
| `Oem` | object | 否 | Firefly 密码策略扩展。 |
| `Oem.PasswordHistoryRecords` | boolean | 否 | 是否启用密码历史记录。 |
| `Oem.PasswordComplexityCheckEnabled` | boolean | 否 | 是否启用密码复杂度检查。 |
| `Oem.PasswordUppercaseletters` | boolean | 否 | 是否要求大写字母。 |
| `Oem.PasswordLowercaseletters` | boolean | 否 | 是否要求小写字母。 |
| `Oem.PasswordComplexitynumber` | boolean | 否 | 是否要求数字。 |
| `Oem.PasswordSpecialcharacters` | boolean | 否 | 是否要求特殊字符。 |

#### 响应示例

配置成功后不返回通用 Message，而是返回更新后的完整 AccountService 资源。

```json title="200 OK·实机响应"
{
  "@odata.context": "/redfish/v1/$metadata#AccountService",
  "@odata.id": "/redfish/v1/AccountService",
  "@odata.type": "#AccountService.v1_12_0.AccountService",
  "AccountLockoutDuration": 5,
  "AccountLockoutThreshold": 5,
  "Accounts": {
    "@odata.id": "/redfish/v1/AccountService/Accounts"
  },
  "Id": "AccountService",
  "MaxPasswordLength": 20,
  "MinPasswordLength": 5,
  "Name": "Account Service",
  "Oem": {
    "PasswordComplexityCheckEnabled": false,
    "PasswordComplexitynumber": false,
    "PasswordHistoryRecords": true,
    "PasswordLowercaseletters": false,
    "PasswordSpecialcharacters": false,
    "PasswordUppercaseletters": false
  },
  "PrivilegeMap": {
    "@odata.id": "/redfish/v1/AccountService/PrivilegeMap"
  },
  "Roles": {
    "@odata.id": "/redfish/v1/AccountService/Roles"
  }
}
```

响应字段与 3.1 的“响应字段”相同，各策略字段为更新后的值。

<Callout title="密码复杂度生效方式" type="info">
  `PasswordComplexityCheckEnabled` 为 `true` 时，后端才会根据其他密码策略字段检查新密码。新建账号和修改密码前，建议先查询当前用户服务配置。
</Callout>
