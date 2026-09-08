# Redfish API 使用方式

本页以“确认服务、登录、访问资源、执行请求、注销”为顺序给出完整接入流程。示例均使用占位符，不包含真实设备地址、密码或 Token。

## 1 准备连接信息

### 1.1 确认基础地址

先确认 BMC 的管理地址、监听端口和当前协议：

```text
<protocol>://<device-ip>:<port>
```

`<protocol>` 可填写 `http` 或 `https`。HTTP 与 HTTPS 由服务配置决定，不能因为端口是 `443` 就直接假定它一定使用 HTTPS。

### 1.2 确认服务可访问

使用 `/redfish` 查询版本入口。该接口需要 Basic Auth 或 Token；首次访问通常使用 Basic Auth：

```bash title="查询 Redfish 版本入口"
curl --user '<username>:<password>' \
  --header 'Accept: application/json' \
  '<protocol>://<device-ip>:<port>/redfish'
```

成功响应：

```json title="200 OK"
{
  "v1": "/redfish/v1/"
}
```

如果 HTTPS 测试设备使用自签名证书，可在确认网络环境可信后临时加入 `--insecure`。生产调用不应关闭证书校验。

## 2 获取会话 Token

### 2.1 登录请求

向登录接口提交用户名和密码：

| 项目 | 内容 |
| --- | --- |
| 方法 | `POST` |
| 路径 | `/redfish/v1/login` |
| 认证 | 无需预先认证 |
| 请求体 | JSON |
| 成功状态码 | `200 OK` |

```bash title="登录"
curl --request POST \
  --header 'Content-Type: application/json' \
  --data '{
    "username": "<username>",
    "password": "<password>"
  }' \
  '<protocol>://<device-ip>:<port>/redfish/v1/login'
```

#### 请求字段

| 字段 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `username` | string | 是 | 登录账号。 |
| `password` | string | 是 | 账号密码。 |

### 2.2 登录响应

登录成功后，从 `data.token` 读取会话 Token：

```json title="200 OK"
{
  "code": 200,
  "data": {
    "name": "<username>",
    "token": "<token>"
  },
  "msg": "login success"
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `code` | integer | 业务结果码；登录成功时为 `200`。 |
| `data` | object | 登录成功后返回的会话数据。 |
| `data.name` | string | 已认证账号名称。 |
| `data.token` | string | 后续请求使用的会话 Token。 |
| `msg` | string | 登录结果说明；成功时为 `login success`。 |

<Callout title="安全保存 Token" type="warn">
  Token 等同于当前会话凭据。不要将其写入 URL、业务请求体、日志或版本库，使用结束后应立即注销。
</Callout>

### 2.3 登录失败处理

登录接口的业务结果通过响应体中的 `code` 表示。例如凭据无效时，HTTP 状态可能仍为 `200`，但 `code` 为 `422`：

```json title="登录失败示例"
{
  "code": 422,
  "msg": "<login-error-message>"
}
```

JSON 语法错误时，服务返回 HTTP `400`，响应体中的 `code` 同样为 `422`。调用方应同时检查 HTTP 状态和 `code`，不要只判断其中一个。

## 3 访问 Redfish 资源

### 3.1 选择认证方式

查询同一资源时，可以在 Basic Auth 和 Token 之间切换：

<CodeBlockTabs defaultValue="token">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询根资源"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="basic-auth">
    ```bash title="使用 Basic Auth 查询根资源"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

<Callout title="Token 的携带位置" type="info">
  Token 始终放在 `X-Xsrf-Token` 请求头中。即使接口包含 JSON 请求体，也不要在请求体中加入 `token` 字段。
</Callout>

### 3.2 按资源链接继续查询

根资源响应会返回当前设备提供的资源入口。例如：

```json title="根资源片段"
{
  "Systems": {
    "@odata.id": "/redfish/v1/Systems"
  },
  "Managers": {
    "@odata.id": "/redfish/v1/Managers"
  },
  "AccountService": {
    "@odata.id": "/redfish/v1/AccountService"
  }
}
```

继续访问时，将基础地址与 `@odata.id` 拼接：

```bash title="跟随资源入口"
curl --header 'X-Xsrf-Token: <token>' \
  --header 'Accept: application/json' \
  '<protocol>://<device-ip>:<port>/redfish/v1/Systems'
```

集合资源中的 `Members` 会继续提供单个资源路径。客户端应跟随返回的 `@odata.id`，避免猜测资源编号。

## 4 提交配置或操作

### 4.1 查询 ActionInfo

如果资源的 `Actions` 中包含 `@Redfish.ActionInfo`，应先查询该地址，确认操作参数、必填项、数据类型和允许值：

```json title="Actions 结构示例"
{
  "Actions": {
    "#Resource.Action": {
      "target": "/redfish/v1/example/Actions/Resource.Action",
      "@Redfish.ActionInfo": "/redfish/v1/example/ActionInfo/Resource.Action"
    }
  }
}
```

### 4.2 发送 JSON 请求

写操作需要同时携带认证请求头和接口定义的业务请求体：

<CodeBlockTabs defaultValue="token">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 提交操作"
    curl --request POST \
      --header 'X-Xsrf-Token: <token>' \
      --header 'Content-Type: application/json' \
      --data '{
        "<parameter>": "<value>"
      }' \
      '<protocol>://<device-ip>:<port><action-target>'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="basic-auth">
    ```bash title="使用 Basic Auth 提交操作"
    curl --request POST \
      --user '<username>:<password>' \
      --header 'Content-Type: application/json' \
      --data '{
        "<parameter>": "<value>"
      }' \
      '<protocol>://<device-ip>:<port><action-target>'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

操作成功时，通常返回 `Base.1.11.0.Success` Message。应以具体资源章节提供的真实请求体、响应示例和字段表为准，不要直接使用上面的占位参数调用设备。

## 5 注销会话

### 5.1 注销请求

使用 Token 完成操作后，调用 `logout` 删除当前会话：

| 项目 | 内容 |
| --- | --- |
| 方法 | `POST` |
| 路径 | `/redfish/v1/logout` |
| 认证 | `X-Xsrf-Token` |
| 请求体 | 无 |
| 成功状态码 | `200 OK` |

```bash title="注销"
curl --request POST \
  --header 'Content-Length: 0' \
  --header 'X-Xsrf-Token: <token>' \
  '<protocol>://<device-ip>:<port>/redfish/v1/logout'
```

显式添加 `Content-Length: 0` 可避免部分代理拒绝没有长度信息的空 POST 请求。

### 5.2 注销响应

```json title="200 OK"
{
  "code": 200,
  "data": "",
  "msg": ""
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `code` | integer | 业务结果码；注销成功时为 `200`。 |
| `data` | string | 注销响应数据；成功时为空字符串。 |
| `msg` | string | 注销结果说明；成功时为空字符串。 |

注销成功后，原 Token 再次访问受保护资源会返回：

```json title="401 Unauthorized"
{
  "code": 401,
  "msg": "unauthorized"
}
```

## 6 常见问题

### 6.1 无法建立连接

- 确认设备地址和端口可以从客户端访问。
- 确认 `<protocol>` 与服务当前配置一致。
- HTTPS 出现证书错误时，检查证书信任链和主机名，而不是在生产环境长期使用 `--insecure`。

### 6.2 返回 401

- Basic Auth：检查账号、密码及账号权限。
- Token：检查请求头名称是否为 `X-Xsrf-Token`，并确认 Token 未过期、未注销。
- 不要同时发送错误的 Basic Auth 和有效 Token；每次请求选择一种认证方式即可。

### 6.3 写操作失败

- 确认请求方法和目标路径与资源的 `Actions.target` 一致。
- 查询对应 ActionInfo，检查必填字段、类型和允许值。
- 检查 `Content-Type: application/json` 和 JSON 语法。
- 同时读取 HTTP 状态码、Redfish `error`、`@Message.ExtendedInfo` 或业务 `code`。
