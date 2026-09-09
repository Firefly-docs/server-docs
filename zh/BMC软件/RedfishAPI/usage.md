# Redfish API 鉴权方式

Redfish API 支持以下两种鉴权方式：

- **Basic Auth**：每次请求都携带用户名和密码，适合快速验证接口。
- **Token**：先登录获取会话 Token，后续请求通过 `X-Xsrf-Token` 请求头鉴权，适合连续调用多个接口。

## 1 准备连接信息

Redfish API 的基础地址格式如下：

```text
<protocol>://<device-ip>:<port>
```

默认值如下，如需连接其他设备，再根据实际环境修改：

| 参数 | 默认值 | 说明 |
| --- | --- | --- |
| `protocol` | `https` | 默认使用 HTTPS。 |
| `device-ip` | `127.0.0.1` | BMC 默认示例地址。 |
| `port` | `443` | 默认 Redfish 服务端口。 |

### 1.1 默认账号密码

默认账号为 `admin`，默认密码为 `admin`：

| 项目 | 默认值 |
| --- | --- |
| 用户名 | `admin` |
| 密码 | `admin` |

首次登录后应立即修改默认密码。生产环境建议使用 HTTPS，避免账号密码在传输过程中泄露。

## 2 鉴权方式

以下 Tabs 分别展示两种完整的鉴权流程。默认显示 Basic Auth。

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    Basic Auth 使用 HTTP `Authorization` 请求头发送用户名和密码。使用 `curl --user` 时，`curl` 会自动生成该请求头。

    使用默认账号密码查询 Redfish 入口：

    ```bash title="Basic Auth"
    curl --user 'admin:admin' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish'
    ```

    查询 Redfish 根资源：

    ```bash title="查询根资源"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1'
    ```

    后续资源路径应以接口响应中的 `@odata.id` 为准，例如：

    ```bash title="查询 Systems 资源"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    Token 鉴权需要先登录获取 Token，后续请求通过 `X-Xsrf-Token` 请求头携带 Token。Token 不要放入 URL 或 JSON 请求体。

    获取 Token：

    ```bash title="登录获取 Token"
    curl --request POST \
      --header 'Content-Type: application/json' \
      --data '{
        "username": "admin",
        "password": "admin"
      }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/login'
    ```

    如果已经修改过默认密码，请将请求中的 `password` 替换为实际密码。登录成功后，从 `data.token` 中读取 Token：

    ```json title="登录成功响应"
    {
      "code": 200,
      "data": {
        "name": "admin",
        "token": "<token>"
      },
      "msg": "login success"
    }
    ```

    使用 Token 查询 Redfish 资源：

    ```bash title="使用 Token 查询根资源"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1'
    ```

    使用 Token 提交 JSON 请求：

    ```bash title="使用 Token 执行操作"
    curl --request POST \
      --header 'X-Xsrf-Token: <token>' \
      --header 'Content-Type: application/json' \
      --data '{
        "<parameter>": "<value>"
      }' \
      '<protocol>://<device-ip>:<port><action-target>'
    ```

    使用结束后注销 Token：

    ```bash title="注销 Token 会话"
    curl --request POST \
      --header 'Content-Length: 0' \
      --header 'X-Xsrf-Token: <token>' \
      '<protocol>://<device-ip>:<port>/redfish/v1/logout'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

成功查询响应示例：

```json title="200 OK"
{
  "v1": "/redfish/v1/"
}
```

同一个请求只选择一种鉴权方式即可，不要同时发送 Basic Auth 和 Token。生产环境建议使用 HTTPS，避免账号密码在传输过程中泄露。测试设备使用自签名证书时，可以临时在 `curl` 命令中加入 `--insecure`，生产环境不应长期关闭证书校验。

## 3 常见问题

### 返回 401 Unauthorized

- Basic Auth：确认用户名、密码正确，并检查账号是否具备目标资源权限。
- Token：确认请求头名称为 `X-Xsrf-Token`，并检查 Token 是否过期或已经注销。
- 检查请求使用的协议、地址和端口是否正确。

### 登录返回 HTTP 200，但登录失败

部分登录失败场景仍可能返回 HTTP `200`，需要同时检查响应体中的 `code`。凭据无效时，`code` 可能为 `422`。
