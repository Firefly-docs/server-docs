# 基本资源

基本资源用于发现 Redfish 服务版本和设备公开的资源集合。客户端可以先查询版本资源，再访问对应版本的根资源，并根据响应中的 `@odata.id` 继续访问系统、管理、用户等资源。

<Callout title="认证要求" type="info">
  本页接口支持 HTTP Basic Auth 和会话 Token。Token 不放在请求体中，而是通过请求头 `X-Xsrf-Token` 携带。请勿将真实密码或 Token 写入脚本、日志或版本库。
</Callout>

<Callout title="访问协议" type="info">
  Redfish 服务可在 HTTP 与 HTTPS 之间切换。示例中的 `<protocol>` 根据服务配置填写 `http` 或 `https`，`<port>` 填写实际监听端口。端口号不能单独决定协议，例如服务也可以在 `443` 端口提供 HTTP。
</Callout>

**获取会话 Token**

向登录接口提交用户名和密码。只有登录时需要在请求体中携带认证信息。

```bash title="登录并获取 Token"
curl --request POST \
  --header 'Content-Type: application/json' \
  --data '{
    "username": "<username>",
    "password": "<password>"
  }' \
  '<protocol>://<device-ip>:<port>/redfish/v1/login'
```

登录成功后，从响应的 `data.token` 获取 Token：

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

后续请求将返回的 Token 放入 `X-Xsrf-Token` 请求头：

```http title="Token 请求头"
X-Xsrf-Token: <token>
```

## 1 版本资源

查询服务支持的 Redfish 主版本及其入口路径。客户端可使用返回的路径继续发现对应版本的 Service Root。

### 请求

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 请求体 | 无 |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询 Redfish 版本入口"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询 Redfish 版本入口"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

### 响应示例

以下内容是测试设备返回的真实响应，HTTP 状态码为 `200 OK`。

```json title="200 OK"
{
  "v1": "/redfish/v1/"
}
```

### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `v1` | string | Redfish v1 Service Root 的访问路径。 |

<Callout title="版本字段的区别" type="info">
  `/redfish` 返回主版本入口，例如 `v1`；`/redfish/v1` 响应中的 `RedfishVersion` 返回实现所遵循的详细协议版本，例如 `1.9.0`。两者含义不同。
</Callout>

### 错误响应

未提供有效认证信息时，服务返回 `401 Unauthorized`，响应格式与根资源接口一致。

## 2 根资源合集

查询 Redfish Service Root。响应包含服务自身的标识、协议版本、设备 UUID，以及当前设备提供的其他资源入口。

### 请求

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 请求体 | 无 |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询根资源合集"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询根资源合集"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

<Callout title="使用规范路径" type="info">
  建议使用不带尾斜杠的 `/redfish/v1`。当前服务访问 `/redfish/v1/` 时会返回 `301 Moved Permanently`，并重定向到 `/redfish/v1`。
</Callout>

### 响应示例

以下内容是使用有效账号请求当前测试环境 `http://172.16.100.172:443` 后得到的真实响应，HTTP 状态码为 `200 OK`。切换到 HTTPS 不会改变响应结构；`UUID` 等设备相关字段在其他设备上会不同。

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/",
  "@odata.type": "#ServiceRoot.v1.15.0.ServiceRoot",
  "AccountService": {
    "@odata.id": "/redfish/v1/AccountService"
  },
  "BmcVersion": "1.0.1",
  "Id": "RootService",
  "Managers": {
    "@odata.id": "/redfish/v1/Managers"
  },
  "Name": "Root Service",
  "Oem": {
    "@odata.id": "/redfish/v1/Oem"
  },
  "RedfishVersion": "1.9.0",
  "Switch": {
    "@odata.id": "/redfish/v1/Switch"
  },
  "Systems": {
    "@odata.id": "/redfish/v1/Systems"
  },
  "UUID": "138D3DBD-B2C1-4BD7-8D9A-112B2789E284",
  "UpdateFwService": {
    "@odata.id": "/redfish/v1/UpdateFwService"
  },
  "UpdateFwService_V2": {
    "@odata.id": "/redfish/v1/UpdateFwService_V2"
  }
}
```

### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | 根资源的唯一访问路径，当前实现返回 `/redfish/v1/`。 |
| `@odata.type` | string | Service Root 的 OData 类型，当前实现为 `#ServiceRoot.v1.15.0.ServiceRoot`。 |
| `Id` | string | 根资源标识，当前实现为 `RootService`。 |
| `Name` | string | 根资源名称，当前实现为 `Root Service`。 |
| `RedfishVersion` | string | 当前实现支持的 Redfish 协议版本。 |
| `BmcVersion` | string | 当前 BMC 服务版本。 |
| `UUID` | string | 当前设备的全局唯一标识符。 |
| `AccountService` | object | 用户服务资源入口。 |
| `Managers` | object | BMC 管理资源入口。 |
| `Oem` | object | Firefly OEM 扩展资源入口。 |
| `Switch` | object | 交换机管理资源入口。 |
| `Systems` | object | 系统资源入口。 |
| `UpdateFwService` | object | 固件升级服务入口。 |
| `UpdateFwService_V2` | object | 第二版固件升级服务入口。 |

<Callout title="资源入口以实际响应为准" type="info">
  Service Root 中的资源入口由后端运行时注册。当前测试设备的响应中没有 `Chassis` 字段，因此客户端不应假定所有文档分类都会出现在根资源响应中，应以实际返回的字段为准。
</Callout>

### 错误响应

未提供有效认证信息时，服务返回：

```json title="401 Unauthorized"
{
  "code": 401,
  "msg": "unauthorized"
}
```

<Callout title="及时注销" type="info">
  Token 使用结束后，应携带该 Token 请求 `POST /redfish/v1/logout`。注销成功后，原 Token 立即失效。
</Callout>

```bash title="注销当前会话"
curl --request POST \
  --header 'X-Xsrf-Token: <token>' \
  '<protocol>://<device-ip>:<port>/redfish/v1/logout'
```
