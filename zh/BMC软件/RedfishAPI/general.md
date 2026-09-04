# 接口通用说明

本页说明 Redfish API 的地址、认证、请求、响应和错误处理约定。各资源页面只补充接口自身的路径、参数及业务字段。

## 1 地址与路径

### 1.1 基础地址

文档中的完整请求地址统一写为：

```text
<protocol>://<device-ip>:<port><resource-path>
```

| 占位符 | 说明 | 示例形式 |
| --- | --- | --- |
| `<protocol>` | 服务当前启用的传输协议。 | `http` 或 `https` |
| `<device-ip>` | BMC 管理地址或可解析的主机名。 | `192.0.2.10` |
| `<port>` | Redfish 服务监听端口。 | `443`、`8443` |
| `<resource-path>` | 以 `/redfish` 开头的接口路径。 | `/redfish/v1/Systems` |

HTTP 与 HTTPS 可按设备配置切换。端口号不能单独决定协议；如果出现 TLS 握手错误或返回非预期代理页面，应先核对当前端口实际使用的是 HTTP 还是 HTTPS。

### 1.2 路径参数

路径中的花括号表示需要替换的动态参数，例如：

```text
/redfish/v1/Systems/{system-id}
```

调用时应将 `{system-id}` 替换为集合响应中实际返回的资源标识。路径和字段名称区分大小写，不要保留花括号。

## 2 请求约定

### 2.1 HTTP 方法

| 方法 | 用途 |
| --- | --- |
| `GET` | 查询资源、集合、状态或 ActionInfo。 |
| `POST` | 创建资源或执行 Redfish Action。 |
| `PUT` | 整体更新实现指定的配置资源。 |
| `PATCH` | 修改资源的部分属性。 |
| `DELETE` | 删除资源、配置或会话。 |

具体接口是否支持某种方法，以资源页面的“请求”表格为准。

### 2.2 通用请求头

| 请求头 | 适用场景 | 说明 |
| --- | --- | --- |
| `Accept: application/json` | 建议用于所有 JSON 查询 | 声明客户端期望接收 JSON。 |
| `Content-Type: application/json` | 提交 JSON 请求体时 | 声明请求体编码为 JSON。 |
| `Authorization: Basic <credentials>` | Basic Auth | `curl --user` 会自动生成该请求头。 |
| `X-Xsrf-Token: <token>` | Token 认证 | 携带登录接口返回的会话 Token。 |
| `Content-Length: 0` | 无请求体的 POST | 某些代理或网关要求显式声明空请求体，例如注销接口。 |

<Callout title="Token 不属于请求体" type="info">
  `X-Xsrf-Token` 是认证请求头。业务请求体只填写接口定义的参数，不要向 JSON 中额外加入 `token` 字段。
</Callout>

### 2.3 JSON 请求体

提交 JSON 时应遵守以下规则：

- 字段名、大小写和数据类型必须与接口定义一致。
- 字符串使用双引号，布尔值使用 `true` 或 `false`，不要写成字符串。
- 必填字段不能省略；可选字段应根据设备能力和 ActionInfo 填写。
- 不要提交文档未定义的字段，否则服务可能返回 `PropertyUnknown` 或其他参数错误。

## 3 认证与会话

### 3.1 HTTP Basic Auth

Basic Auth 适合命令行验证或受控环境中的短请求。用户名和密码随每次请求发送：

```bash title="Basic Auth"
curl --user '<username>:<password>' \
  --header 'Accept: application/json' \
  '<protocol>://<device-ip>:<port>/redfish/v1'
```

生产环境使用 Basic Auth 时应启用 HTTPS，避免凭据在明文链路上传输。

### 3.2 会话 Token

调用 `POST /redfish/v1/login` 获取 Token，随后在每个受保护请求中添加：

```http title="Token 请求头"
X-Xsrf-Token: <token>
```

Token 与当前会话关联。Token 无效、已注销或会话过期时，接口返回 `401 Unauthorized`。

### 3.3 注销会话

Token 使用结束后，调用 `POST /redfish/v1/logout` 主动注销。注销成功后，原 Token 立即失效。

```bash title="注销当前会话"
curl --request POST \
  --header 'Content-Length: 0' \
  --header 'X-Xsrf-Token: <token>' \
  '<protocol>://<device-ip>:<port>/redfish/v1/logout'
```

## 4 响应约定

### 4.1 HTTP 状态码

| 状态码 | 含义 | 处理建议 |
| --- | --- | --- |
| `200 OK` | 查询或操作请求已被服务处理。 | 继续检查响应体中的业务字段。 |
| `204 No Content` | 请求成功且没有响应体，常用于预检请求。 | 不要尝试解析 JSON。 |
| `400 Bad Request` | JSON 格式错误或请求参数不符合定义。 | 检查请求体、字段类型和必填字段。 |
| `401 Unauthorized` | 未认证、凭据无效或 Token 已失效。 | 重新认证并检查请求头。 |
| `404 Not Found` | 路径或目标资源不存在。 | 从集合响应重新确认 `@odata.id`。 |
| `405 Method Not Allowed` | 方法不支持，或后端以该状态表示操作失败。 | 查看 Redfish 错误体中的详细信息。 |

<Callout title="同时检查 HTTP 状态和响应体" type="warn">
  当前登录接口在部分认证失败场景中可能返回 HTTP `200`，但响应体中的 `code` 为 `422`。客户端不能只判断 HTTP 状态码，还应检查接口定义的业务字段或 Redfish Message。
</Callout>

### 4.2 OData 通用字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | 当前资源或关联资源的访问路径。 |
| `@odata.type` | string | 资源采用的 Redfish Schema 类型和版本。 |
| `Id` | string | 资源标识。 |
| `Name` | string | 资源显示名称。 |
| `Description` | string | 资源用途说明。 |
| `Oem` | object/null | Firefly 或其他厂商扩展字段。 |
| `Actions` | object | 当前资源支持的操作及目标路径。 |

字段是否出现取决于资源类型和设备能力。

### 4.3 集合与 ActionInfo

集合资源通常使用以下字段：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `Members` | array | 集合成员，每个成员通常包含 `@odata.id`。 |
| `Members@odata.count` | integer | 当前响应中的成员数量。 |

ActionInfo 用于描述操作参数。常见字段包括参数名称、是否必填、数据类型和允许值。执行写操作前，应先读取接口对应的 ActionInfo，避免使用当前设备不支持的参数。

### 4.4 通用操作成功响应

除登录、注销及接口明确说明的自定义响应外，Redfish 写操作成功时通常返回统一 Message：

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

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.type` | string | Message 资源的 OData 类型。 |
| `Timestamp` | string | 服务生成响应时的 Unix 时间戳，使用字符串表示。 |
| `MessageId` | string | 消息注册表标识；成功时为 `Base.1.11.0.Success`。 |
| `Message` | string | 可读的操作结果说明。 |
| `MessageArgs` | array | 用于格式化消息的参数；无参数时为空数组。 |
| `MessageSeverity` | string | Redfish 消息严重程度；成功时为 `OK`。 |
| `Severity` | string | 兼容字段；当前成功响应为空字符串。 |
| `Oem` | object/null | 厂商扩展消息；无扩展时为 `null`。 |
| `RelatedProperties` | array/null | 与消息关联的请求属性；无关联属性时为 `null`。 |
| `Resolution` | string | 建议的处理方式；成功时为 `None`。 |

`Timestamp` 每次请求都会变化，客户端不应与示例值进行固定比较。

### 4.5 认证错误响应

未提供有效认证信息时，受保护接口返回：

```json title="401 Unauthorized"
{
  "code": 401,
  "msg": "unauthorized"
}
```

业务参数错误通常使用 Redfish `error` 和 `@Message.ExtendedInfo` 描述。客户端应优先读取其中的 `code`、`message`、`MessageId`、`MessageArgs`、`MessageSeverity` 和 `Resolution`。

## 5 其他通信方式

### 5.1 WebSocket

远程终端、KVM、UID 状态订阅等接口可能使用 WebSocket。此类接口需要完成 HTTP `Upgrade: websocket` 握手，并由客户端按对应章节传递认证信息和业务参数，不返回普通 REST JSON 响应。

### 5.2 HTTPS 证书

测试设备可能使用自签名证书。仅在受控测试环境中可使用 `curl --insecure` 临时跳过证书校验；生产环境应部署可信证书并保持证书验证开启。
