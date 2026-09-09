# 基本资源

基本资源用于发现 Redfish 服务版本和设备公开的资源集合。客户端可以先查询版本资源，再访问对应版本的根资源，并根据响应中的 `@odata.id` 继续访问系统、管理、用户等资源。

## GET – 查询 Redfish 版本信息

【功能描述】

查询服务支持的 Redfish 主版本及其入口路径。客户端可使用返回的路径继续发现对应版本的 Service Root。

【请求】

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 请求体 | 无 |
| 成功状态码 | `200 OK` |

【命令】

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

【参数说明】

无

【输出实例】

```json title="200 OK"
{
  "v1": "/redfish/v1/"
}
```

【输出说明】

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `v1` | string | Redfish v1 Service Root 的访问路径。 |

## GET – 查询 Redfish 根资源

【功能描述】

查询 Redfish Service Root。响应包含服务自身的标识、协议版本、设备 UUID，以及当前设备提供的其他资源入口。

【请求】

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 请求体 | 无 |
| 成功状态码 | `200 OK` |

【命令】

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

【参数说明】

无

【输出实例】

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

【输出说明】

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