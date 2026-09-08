# OEM 资源

OEM 资源提供 Firefly 扩展的 KVM/ADB、VNC、WebTTY 和告警通知能力。

<Callout title="认证与访问协议" type="info">
  本页普通 HTTP 请求支持 HTTP Basic Auth 和会话 Token。Token 通过请求头 `X-Xsrf-Token` 携带。WebSocket 连接同样使用 `X-Xsrf-Token` 请求头。示例中的 `<protocol>` 根据服务配置填写 `http` 或 `https`。
</Callout>

## 1 远程管理资源

### 1.1 查询 ADB 设备列表

查询当前通过 USB 或网络 ADB 连接的设备。

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Oem/KvmServices` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 请求体 | 无 |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="查询 ADB 设备列表"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      --max-time 20 \
      '<protocol>://<device-ip>:<port>/redfish/v1/Oem/KvmServices'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询 ADB 设备列表"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      --max-time 20 \
      '<protocol>://<device-ip>:<port>/redfish/v1/Oem/KvmServices'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 成功响应结构

后端成功取得 ADB 设备时返回以下结构：

```json title="200 OK · 后端响应结构"
{
  "@odata.type": "#Oem.v1_0_2.FireflyKvmService.DeviceList",
  "Oem": {
    "Firefly": {
      "DeviceList": []
    }
  },
  "Name": "FireflyKvmService.DeviceList",
  "Id": "FireflyKvmService.DeviceList",
  "@odata.id": "/redfish/v1/Oem/KvmServices"
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.type` | string | ADB 设备列表的 OEM 类型。 |
| `Oem` | object | OEM 扩展信息。 |
| `Oem.Firefly` | object | Firefly KVM 扩展信息。 |
| `Oem.Firefly.DeviceList` | array | ADB 设备列表。具体设备字段由当前 ADB 实现返回。 |
| `Name` | string | 资源名称。 |
| `Id` | string | 资源标识。 |
| `@odata.id` | string | 当前资源路径。 |

<Callout title="当前部署查询超时" type="warn">
  实机请求在 20 秒内未返回任何响应，curl 状态为超时。该接口会同步等待 ADB 设备扫描；调用方应设置客户端超时，并将“未发现设备”和“ADB 服务未响应”区分处理。
</Callout>


### 1.2 远程管理 WebSocket

通过 WebSocket 建立 Android 设备投屏会话。连接成功后，服务端先发送设备显示和视频编码能力，客户端再提交投屏参数，随后通过二进制 WebSocket 消息持续接收视频数据。

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` + WebSocket Upgrade |
| 路径 | `/redfish/v1/Oem/KvmServices/ws` |
| 认证 | `X-Xsrf-Token` |
| 查询参数 | `udid`，Android 设备唯一标识 |
| 成功状态码 | `101 Switching Protocols` |
| 传输内容 | JSON 协商消息、二进制视频帧和控制消息 |

<Callout title="协议对应关系" type="info">
  BMC 使用 HTTP 时连接 `ws://`；使用 HTTPS 时连接 `wss://`。该接口不返回普通的 `200 OK` JSON 响应。
</Callout>

#### 连接示例

<CodeBlockTabs defaultValue="ws">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="ws">HTTP / WS</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="wss">HTTPS / WSS</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="ws">
    ```bash title="建立 Android 投屏连接"
    websocat \
      --header 'X-Xsrf-Token: <token>' \
      'ws://<device-ip>:<port>/redfish/v1/Oem/KvmServices/ws?udid=<device-udid>'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="wss">
    ```bash title="建立加密 Android 投屏连接"
    websocat \
      --header 'X-Xsrf-Token: <token>' \
      'wss://<device-ip>:<port>/redfish/v1/Oem/KvmServices/ws?udid=<device-udid>'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

`udid` 应使用“[1.1 查询 ADB 设备列表](#11-查询-adb-设备列表)”返回的有效设备标识。未在 URL 中传入 `udid` 时，服务端兼容旧版客户端：WebSocket 连接建立后，将下列 JSON 作为第一条文本消息发送。

```json title="旧版客户端设备选择消息"
{
  "udid": "<device-udid>"
}
```

#### 连接流程

1. 客户端使用有效 `udid` 发起 WebSocket Upgrade 请求。
2. 服务端查找 Android 设备，并读取设备名称、分辨率、显示器和编码器信息。
3. 服务端发送一条 JSON 文本消息，告知客户端可用的视频能力。
4. 新版客户端发送 `setting` 消息，选择码率、帧率、分辨率、显示器和编码器。
5. 服务端启动 scrcpy 会话，持续发送二进制视频数据；客户端可同时发送触摸等控制消息。

#### 视频能力消息

以下为服务端首条文本消息的结构示例。设备名称、分辨率和编码器列表以实际 Android 设备为准。

```json title="WebSocket 文本消息·结构示例"
{
  "deviceName": "<android-device-name>",
  "displayInfo": {
    "displayId": 0,
    "width": 1080,
    "height": 1920,
    "rotation": 0,
    "layerStack": 0
  },
  "connectionCount": 0,
  "screenInfo": {
    "left": 0,
    "top": 0,
    "right": 0,
    "bottom": 0,
    "width": 1080,
    "height": 1920,
    "deviceRotation": 0
  },
  "videoSettings": {
    "video": true,
    "bitrate": 0,
    "maxFps": 0,
    "width": 0,
    "height": 0,
    "left": 0,
    "top": 0,
    "right": 0,
    "bottom": 0,
    "displayId": 0,
    "encoderName": "",
    "codecOptions": "",
    "iFrameInterval": 0,
    "sendFrameMeta": false,
    "lockedVideoOrientation": 0,
    "videoBuffer": 0,
    "control": true,
    "audio": false
  },
  "encoders": [
    "<encoder-name>"
  ],
  "clientId": 1
}
```

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `deviceName` | string | Android 设备产品名称。 |
| `displayInfo` | object | 当前显示器信息。 |
| `displayInfo.displayId` | integer | Android 显示器编号。 |
| `displayInfo.width` | integer | 显示器宽度，单位为像素。 |
| `displayInfo.height` | integer | 显示器高度，单位为像素。 |
| `displayInfo.rotation` | integer | 显示器旋转角度。 |
| `displayInfo.layerStack` | integer | Android 显示层栈标识。 |
| `connectionCount` | integer | 当前连接数。 |
| `screenInfo` | object | 当前屏幕区域和旋转信息。 |
| `screenInfo.left` | integer | 屏幕区域左边界。 |
| `screenInfo.top` | integer | 屏幕区域上边界。 |
| `screenInfo.right` | integer | 屏幕区域右边界。 |
| `screenInfo.bottom` | integer | 屏幕区域下边界。 |
| `screenInfo.width` | integer | 屏幕宽度，单位为像素。 |
| `screenInfo.height` | integer | 屏幕高度，单位为像素。 |
| `screenInfo.deviceRotation` | integer | 设备屏幕旋转角度。 |
| `videoSettings` | object | 服务端建议的初始视频配置。 |
| `videoSettings.video` | boolean | 是否启用视频传输。 |
| `videoSettings.bitrate` | integer | 视频码率，单位为 bit/s；初始能力消息中可为 `0`。 |
| `videoSettings.maxFps` | integer | 最大视频帧率；初始能力消息中可为 `0`。 |
| `videoSettings.width` | integer | 目标视频宽度，单位为像素。 |
| `videoSettings.height` | integer | 目标视频高度，单位为像素。 |
| `videoSettings.left` | integer | 视频裁剪区域左边界。 |
| `videoSettings.top` | integer | 视频裁剪区域上边界。 |
| `videoSettings.right` | integer | 视频裁剪区域右边界。 |
| `videoSettings.bottom` | integer | 视频裁剪区域下边界。 |
| `videoSettings.displayId` | integer | Android 显示器编号。 |
| `videoSettings.encoderName` | string | 选用的视频编码器名称。 |
| `videoSettings.codecOptions` | string | 传递给视频编码器的附加选项。 |
| `videoSettings.iFrameInterval` | integer | 关键帧间隔。 |
| `videoSettings.sendFrameMeta` | boolean | 是否传输视频帧元数据。 |
| `videoSettings.lockedVideoOrientation` | integer | 锁定的视频方向。 |
| `videoSettings.videoBuffer` | integer | 视频缓冲配置。 |
| `videoSettings.control` | boolean | 是否启用远程控制。 |
| `videoSettings.audio` | boolean | 是否启用音频传输。 |
| `encoders` | array | 当前设备支持的视频编码器名称列表。 |
| `clientId` | integer | 当前 WebSocket 客户端标识。 |

#### 客户端设置消息

收到视频能力消息后，新版客户端应发送一条 `setting` JSON 文本消息。

```json title="WebSocket 文本消息"
{
  "type": "setting",
  "data": {
    "bitrate": 4000000,
    "maxFps": 24,
    "bounds": {
      "width": 720,
      "height": 1280
    },
    "displayId": 0,
    "control": true,
    "audio": false,
    "video": true,
    "encoderName": "<encoder-name>",
    "codecOptions": ""
  }
}
```

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `type` | string | 消息类型，固定为 `setting`。 |
| `data.bitrate` | integer | 视频码率，单位为 bit/s。 |
| `data.maxFps` | integer | 最大视频帧率。 |
| `data.bounds.width` | integer | 输出视频宽度，单位为像素。 |
| `data.bounds.height` | integer | 输出视频高度，单位为像素。 |
| `data.displayId` | integer | 要投屏的 Android 显示器编号。 |
| `data.control` | boolean | 是否启用远程控制。 |
| `data.audio` | boolean | 是否启用音频传输。 |
| `data.video` | boolean | 是否启用视频传输。 |
| `data.encoderName` | string | 视频编码器名称，应从服务端返回的 `encoders` 中选择。 |
| `data.codecOptions` | string | 传递给 scrcpy 视频编码器的附加选项；无额外选项时传空字符串。 |

#### 视频与控制消息

- 服务端以二进制 WebSocket 消息持续发送视频数据，客户端需按 scrcpy 视频流处理，不能将其当作 JSON 解析。
- 客户端可以发送 scrcpy 二进制控制包，也可发送兼容的 JSON 文本触摸消息。
- `pong` 文本消息用于客户端保活，服务端接收后不转发给 Android 设备。

```json title="触摸控制消息"
{
  "messageType": "touch",
  "data": {
    "actionType": 0,
    "x": 416,
    "y": 243,
    "width": 1080,
    "height": 1920
  }
}
```

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `messageType` | string | 消息类型。触摸事件为 `touch`，保活消息为 `pong`。 |
| `data.actionType` | integer | 触摸动作：`0` 表示按下，`1` 表示抬起，`2` 表示移动。 |
| `data.x` | integer | 触摸点 X 坐标。 |
| `data.y` | integer | 触摸点 Y 坐标。 |
| `data.width` | integer | 坐标系宽度，必须大于 `0` 且不超过 `65535`。 |
| `data.height` | integer | 坐标系高度，必须大于 `0` 且不超过 `65535`。 |

#### 错误消息

WebSocket Upgrade 已成功但后续初始化失败时，服务端会发送 JSON 字符串后结束连接。

| 消息 | 说明 |
| --- | --- |
| `"udid is empty"` | 旧版握手消息未提供有效 `udid`。 |
| `"device not found"` | 未找到 `udid` 对应的 Android 设备。 |
| `"scrcpy option list failed"` | 无法读取设备的 scrcpy 编码器或显示信息。 |
| `"scrcpy client create failed"` | 创建 scrcpy 会话失败。 |
| `"scrcpy client start failed"` | 启动 scrcpy 会话失败。 |

## 2 告警管理资源

### 2.1 查询 SMTP 配置

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Oem/Alert/GetSMTPServer` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 所需权限 | `OemDebug` |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="查询 SMTP 配置"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Oem/Alert/GetSMTPServer'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询 SMTP 配置"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Oem/Alert/GetSMTPServer'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Oem/Alert/GetSMTPServer",
  "enable": false,
  "mail_addr": "",
  "smtp_addr": "",
  "smtp_port": 0
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | SMTP 配置资源路径。 |
| `enable` | boolean | 是否启用邮件发送。 |
| `mail_addr` | string | 发件人邮箱账号。 |
| `smtp_addr` | string | SMTP 服务器地址。 |
| `smtp_port` | integer | SMTP 服务器端口。 |

<Callout title="授权码不会返回" type="info">
  查询接口不会返回 `mail_auth`，因此无法仅依靠 GET 响应完整还原当前 SMTP 配置。原值回写前必须另行确认授权码。
</Callout>

### 2.2 设置 SMTP 配置

| 项目 | 内容 |
| --- | --- |
| 方法 | `POST` |
| 路径 | `/redfish/v1/Oem/Alert/SetSMTPServer` |
| 所需权限 | `OemDebug` |
| 成功状态码 | `200 OK` |

| 请求字段 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `mail_addr` | string | 是 | 发件人邮箱账号。 |
| `mail_auth` | string | 是 | SMTP 授权码或应用密码。 |
| `smtp_addr` | string | 是 | SMTP 服务器域名或地址。 |
| `smtp_port` | integer | 是 | SMTP 服务器端口。 |
| `enable` | boolean | 是 | 是否启用邮件告警。 |

```bash title="使用 Token 设置 SMTP 配置"
curl --request POST \
  --header 'X-Xsrf-Token: <token>' \
  --header 'Content-Type: application/json' \
  --data '{
    "mail_addr": "sender@example.com",
    "mail_auth": "<smtp-authorization-code>",
    "smtp_addr": "smtp.example.com",
    "smtp_port": 465,
    "enable": true
  }' \
  '<protocol>://<device-ip>:<port>/redfish/v1/Oem/Alert/SetSMTPServer'
```

该接口整体替换发件人配置并写入数据库，本次未执行。

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
| `Timestamp` | string | 服务端响应时间戳。 |
| `MessageId` | string | 成功消息标识。 |
| `Message` | string | 请求处理结果。 |
| `MessageArgs` | array | 消息格式化参数。 |
| `MessageSeverity` | string | 消息严重级别。 |
| `Severity` | string | 兼容保留的严重级别字段。 |
| `Oem` | object \| null | OEM 扩展消息。 |
| `RelatedProperties` | array \| null | 相关属性路径。 |
| `Resolution` | string | 建议的处理方式。 |

### 2.3 发送测试邮件

| 项目 | 内容 |
| --- | --- |
| 方法 | `POST` |
| 路径 | `/redfish/v1/Oem/Alert/SendMsg` |
| 所需权限 | `OemDebug` |
| 前置条件 | SMTP 配置有效且网络可访问 SMTP 服务器 |

| 请求字段 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `to` | string[] | 是 | 收件人列表。 |
| `cc` | string[] | 否 | 抄送列表。 |
| `bcc` | string[] | 否 | 密送列表。 |
| `subject` | string | 是 | 邮件主题。 |
| `msg` | string | 是 | 邮件正文。 |
| `msg_format` | string | 是 | 正文 MIME 类型，例如 `text/plain` 或 `text/html`。 |

```json title="请求体示例"
{
  "to": ["receiver@example.com"],
  "subject": "BMC alert test",
  "msg": "This is a test message.",
  "msg_format": "text/plain"
}
```

该操作会向外部邮箱真实发送邮件，本次未执行。

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
| `Timestamp` | string | 服务端响应时间戳。 |
| `MessageId` | string | 成功消息标识。 |
| `Message` | string | 请求处理结果。 |
| `MessageArgs` | array | 消息格式化参数。 |
| `MessageSeverity` | string | 消息严重级别。 |
| `Severity` | string | 兼容保留的严重级别字段。 |
| `Oem` | object \| null | OEM 扩展消息。 |
| `RelatedProperties` | array \| null | 相关属性路径。 |
| `Resolution` | string | 建议的处理方式。 |

### 2.4 查询告警接收者

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Oem/Alert/GetReceivers` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 所需权限 | `OemDebug` |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="查询告警接收者"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Oem/Alert/GetReceivers'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询告警接收者"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Oem/Alert/GetReceivers'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Oem/Alert/GetReceivers",
  "Receivers": {}
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | 告警接收者资源路径。 |
| `Receivers` | object | 以接收者名称为键的接收者映射。 |
| `Receivers.{name}.Name` | string | 接收者名称。 |
| `Receivers.{name}.Way` | integer | 通知渠道组合编码，取值为 `1` 至 `8`。 |
| `Receivers.{name}.Severity` | string | 接收的告警严重级别。 |
| `Receivers.{name}.Mail` | string | 邮件地址。 |
| `Receivers.{name}.EnterpriseWechat` | string | 企业微信机器人 Webhook。 |
| `Receivers.{name}.DingTalk` | string | 钉钉机器人 Webhook。 |

### 2.5 新增或修改告警接收者

| 操作 | 方法 | 路径 |
| --- | --- | --- |
| 新增接收者 | `POST` | `/redfish/v1/Oem/Alert/AddReceivers` |
| 修改接收者 | `POST` | `/redfish/v1/Oem/Alert/SetReceivers` |

两个接口使用相同请求结构。

| 请求字段 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `Name` | string | 是 | 接收者唯一名称。修改接口以该字段定位或创建记录。 |
| `Way` | integer | 是 | 渠道组合：`1` 不通知、`2` 邮件、`3` 企业微信、`4` 钉钉、`5` 邮件+企业微信、`6` 邮件+钉钉、`7` 企业微信+钉钉、`8` 全部。 |
| `Severity` | string | 是 | 接收的告警严重级别。 |
| `Mail` | string | 条件必填 | 使用邮件渠道时填写。 |
| `EnterpriseWechat` | string | 条件必填 | 使用企业微信渠道时填写 Webhook。 |
| `DingTalk` | string | 条件必填 | 使用钉钉渠道时填写 Webhook。 |

```json title="请求体示例"
{
  "Name": "operations-team",
  "Way": 2,
  "Severity": "Critical",
  "Mail": "operations@example.com",
  "EnterpriseWechat": "",
  "DingTalk": ""
}
```

后端会拒绝重复名称，以及与其他接收者重复的邮箱或机器人 Webhook。本次未修改接收者。

#### 响应示例

新增和修改成功时均返回：

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
| `Timestamp` | string | 服务端响应时间戳。 |
| `MessageId` | string | 成功消息标识。 |
| `Message` | string | 请求处理结果。 |
| `MessageArgs` | array | 消息格式化参数。 |
| `MessageSeverity` | string | 消息严重级别。 |
| `Severity` | string | 兼容保留的严重级别字段。 |
| `Oem` | object \| null | OEM 扩展消息。 |
| `RelatedProperties` | array \| null | 相关属性路径。 |
| `Resolution` | string | 建议的处理方式。 |

### 2.6 删除告警接收者

| 项目 | 内容 |
| --- | --- |
| 方法 | `POST` |
| 路径 | `/redfish/v1/Oem/Alert/DelReceivers` |
| 请求字段 | `Name`，string，必填 |
| 成功状态码 | `200 OK` |

```json title="请求体示例"
{
  "Name": "operations-team"
}
```

接收者不存在时返回 Redfish 通用错误。该操作会永久删除接收者配置，本次未执行。

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
| `Timestamp` | string | 服务端响应时间戳。 |
| `MessageId` | string | 成功消息标识。 |
| `Message` | string | 请求处理结果。 |
| `MessageArgs` | array | 消息格式化参数。 |
| `MessageSeverity` | string | 消息严重级别。 |
| `Severity` | string | 兼容保留的严重级别字段。 |
| `Oem` | object \| null | OEM 扩展消息。 |
| `RelatedProperties` | array \| null | 相关属性路径。 |
| `Resolution` | string | 建议的处理方式。 |

### 2.7 测试告警接收者

对一个或多个已存在的接收者触发测试通知。

| 项目 | 内容 |
| --- | --- |
| 方法 | `POST` |
| 路径 | `/redfish/v1/Oem/Alert/Test` |
| 请求体 | 接收者名称对象数组 |
| 成功状态码 | `200 OK` |

```json title="请求体示例"
[
  { "Name": "operations-team" }
]
```

接口会异步调用接收者配置的邮件、企业微信或钉钉渠道，可能产生真实外部通知，本次未执行。

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
| `Timestamp` | string | 服务端响应时间戳。 |
| `MessageId` | string | 成功消息标识。 |
| `Message` | string | 请求处理结果。 |
| `MessageArgs` | array | 消息格式化参数。 |
| `MessageSeverity` | string | 消息严重级别。 |
| `Severity` | string | 兼容保留的严重级别字段。 |
| `Oem` | object \| null | OEM 扩展消息。 |
| `RelatedProperties` | array \| null | 相关属性路径。 |
| `Resolution` | string | 建议的处理方式。 |
