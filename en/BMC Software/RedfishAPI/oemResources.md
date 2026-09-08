# OEM Resources

OEM resources provide Firefly extended KVM/ADB, VNC, WebTTY, and alert notification capabilities.

<Callout title="Authentication and Access Protocol" type="info">
  Plain HTTP requests on this page support HTTP Basic Auth and session tokens. The token is carried in the `X-Xsrf-Token` request header. WebSocket connections also use the `X-Xsrf-Token` request header. In the examples, replace `<protocol>` with `http` or `https` according to the service configuration.
</Callout>

## 1 Remote Management Resources

### 1.1 Query the ADB Device List

Queries the devices currently connected via USB or network ADB.

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Oem/KvmServices` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Request body | None |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="Query the ADB Device List"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      --max-time 20 \
      '<protocol>://<device-ip>:<port>/redfish/v1/Oem/KvmServices'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="Query the ADB Device List with a Token"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      --max-time 20 \
      '<protocol>://<device-ip>:<port>/redfish/v1/Oem/KvmServices'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Success Response Structure

The following structure is returned when the backend successfully retrieves ADB devices:

```json title="200 OK · Backend Response Structure"
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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.type` | string | OEM type of the ADB device list. |
| `Oem` | object | OEM extension information. |
| `Oem.Firefly` | object | Firefly KVM extension information. |
| `Oem.Firefly.DeviceList` | array | ADB device list. The specific device fields are returned by the current ADB implementation. |
| `Name` | string | Resource name. |
| `Id` | string | Resource identifier. |
| `@odata.id` | string | Path of the current resource. |

<Callout title="Query Timeout in Current Deployment" type="warn">
  On-device requests did not return any response within 20 seconds and curl reported a timeout. This interface synchronously waits for the ADB device scan; callers should set a client timeout and handle "no devices found" and "ADB service not responding" as separate cases.
</Callout>


### 1.2 Remote Management WebSocket

Establishes an Android device screen-casting session via WebSocket. After the connection succeeds, the server first sends the device display and video encoding capabilities, the client then submits screen-casting parameters, and afterwards video data is continuously received as binary WebSocket messages.

| Item | Content |
| --- | --- |
| Method | `GET` + WebSocket Upgrade |
| Path | `/redfish/v1/Oem/KvmServices/ws` |
| Authentication | `X-Xsrf-Token` |
| Query parameter | `udid`, the unique identifier of the Android device |
| Success status code | `101 Switching Protocols` |
| Transferred content | JSON negotiation messages, binary video frames, and control messages |

<Callout title="Protocol Mapping" type="info">
  When the BMC uses HTTP, connect via `ws://`; when it uses HTTPS, connect via `wss://`. This interface does not return an ordinary `200 OK` JSON response.
</Callout>

#### Connection Example

<CodeBlockTabs defaultValue="ws">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="ws">HTTP / WS</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="wss">HTTPS / WSS</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="ws">
    ```bash title="Establish an Android Screen-Casting Connection"
    websocat \
      --header 'X-Xsrf-Token: <token>' \
      'ws://<device-ip>:<port>/redfish/v1/Oem/KvmServices/ws?udid=<device-udid>'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="wss">
    ```bash title="Establish an Encrypted Android Screen-Casting Connection"
    websocat \
      --header 'X-Xsrf-Token: <token>' \
      'wss://<device-ip>:<port>/redfish/v1/Oem/KvmServices/ws?udid=<device-udid>'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

The `udid` should be a valid device identifier returned by "[1.1 Query the ADB Device List](#11-query-the-adb-device-list)". If `udid` is not passed in the URL, the server remains compatible with legacy clients: after the WebSocket connection is established, send the following JSON as the first text message.

```json title="Legacy Client Device Selection Message"
{
  "udid": "<device-udid>"
}
```

#### Connection Flow

1. The client initiates a WebSocket Upgrade request with a valid `udid`.
2. The server looks up the Android device and reads the device name, resolution, display, and encoder information.
3. The server sends a JSON text message informing the client of the available video capabilities.
4. The new client sends a `setting` message to select the bitrate, frame rate, resolution, display, and encoder.
5. The server starts the scrcpy session and continuously sends binary video data; the client may simultaneously send control messages such as touch events.

#### Video Capability Message

The following is an example structure of the server's first text message. The device name, resolution, and encoder list depend on the actual Android device.

```json title="WebSocket Text Message · Structure Example"
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

| Field | Type | Description |
| --- | --- | --- |
| `deviceName` | string | Android device product name. |
| `displayInfo` | object | Current display information. |
| `displayInfo.displayId` | integer | Android display number. |
| `displayInfo.width` | integer | Display width in pixels. |
| `displayInfo.height` | integer | Display height in pixels. |
| `displayInfo.rotation` | integer | Display rotation angle. |
| `displayInfo.layerStack` | integer | Android display layer stack identifier. |
| `connectionCount` | integer | Current number of connections. |
| `screenInfo` | object | Current screen region and rotation information. |
| `screenInfo.left` | integer | Left boundary of the screen region. |
| `screenInfo.top` | integer | Top boundary of the screen region. |
| `screenInfo.right` | integer | Right boundary of the screen region. |
| `screenInfo.bottom` | integer | Bottom boundary of the screen region. |
| `screenInfo.width` | integer | Screen width in pixels. |
| `screenInfo.height` | integer | Screen height in pixels. |
| `screenInfo.deviceRotation` | integer | Device screen rotation angle. |
| `videoSettings` | object | Initial video configuration suggested by the server. |
| `videoSettings.video` | boolean | Whether video transmission is enabled. |
| `videoSettings.bitrate` | integer | Video bitrate in bit/s; may be `0` in the initial capability message. |
| `videoSettings.maxFps` | integer | Maximum video frame rate; may be `0` in the initial capability message. |
| `videoSettings.width` | integer | Target video width in pixels. |
| `videoSettings.height` | integer | Target video height in pixels. |
| `videoSettings.left` | integer | Left boundary of the video crop region. |
| `videoSettings.top` | integer | Top boundary of the video crop region. |
| `videoSettings.right` | integer | Right boundary of the video crop region. |
| `videoSettings.bottom` | integer | Bottom boundary of the video crop region. |
| `videoSettings.displayId` | integer | Android display number. |
| `videoSettings.encoderName` | string | Name of the selected video encoder. |
| `videoSettings.codecOptions` | string | Additional options passed to the video encoder. |
| `videoSettings.iFrameInterval` | integer | Keyframe interval. |
| `videoSettings.sendFrameMeta` | boolean | Whether video frame metadata is transmitted. |
| `videoSettings.lockedVideoOrientation` | integer | Locked video orientation. |
| `videoSettings.videoBuffer` | integer | Video buffer configuration. |
| `videoSettings.control` | boolean | Whether remote control is enabled. |
| `videoSettings.audio` | boolean | Whether audio transmission is enabled. |
| `encoders` | array | List of video encoder names supported by the current device. |
| `clientId` | integer | Identifier of the current WebSocket client. |

#### Client Setting Message

After receiving the video capability message, the new client should send a `setting` JSON text message.

```json title="WebSocket Text Message"
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

| Field | Type | Description |
| --- | --- | --- |
| `type` | string | Message type, fixed as `setting`. |
| `data.bitrate` | integer | Video bitrate in bit/s. |
| `data.maxFps` | integer | Maximum video frame rate. |
| `data.bounds.width` | integer | Output video width in pixels. |
| `data.bounds.height` | integer | Output video height in pixels. |
| `data.displayId` | integer | Android display number to cast. |
| `data.control` | boolean | Whether remote control is enabled. |
| `data.audio` | boolean | Whether audio transmission is enabled. |
| `data.video` | boolean | Whether video transmission is enabled. |
| `data.encoderName` | string | Video encoder name, which should be selected from the `encoders` returned by the server. |
| `data.codecOptions` | string | Additional options passed to the scrcpy video encoder; pass an empty string when there are no additional options. |

#### Video and Control Messages

- The server continuously sends video data as binary WebSocket messages; the client must handle it as a scrcpy video stream and must not parse it as JSON.
- The client may send scrcpy binary control packets, or compatible JSON text touch messages.
- The `pong` text message is used for client keep-alive; the server does not forward it to the Android device after receiving it.

```json title="Touch Control Message"
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

| Field | Type | Description |
| --- | --- | --- |
| `messageType` | string | Message type. `touch` for touch events, `pong` for keep-alive messages. |
| `data.actionType` | integer | Touch action: `0` means press down, `1` means release, `2` means move. |
| `data.x` | integer | X coordinate of the touch point. |
| `data.y` | integer | Y coordinate of the touch point. |
| `data.width` | integer | Width of the coordinate system; must be greater than `0` and no more than `65535`. |
| `data.height` | integer | Height of the coordinate system; must be greater than `0` and no more than `65535`. |

#### Error Messages

When the WebSocket Upgrade has succeeded but a later initialization step fails, the server sends a JSON string and then closes the connection.

| Message | Description |
| --- | --- |
| `"udid is empty"` | The legacy handshake message did not provide a valid `udid`. |
| `"device not found"` | No Android device matching the `udid` was found. |
| `"scrcpy option list failed"` | Failed to read the device's scrcpy encoder or display information. |
| `"scrcpy client create failed"` | Failed to create the scrcpy session. |
| `"scrcpy client start failed"` | Failed to start the scrcpy session. |

## 2 Alert Management Resources

### 2.1 Query SMTP Configuration

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Oem/Alert/GetSMTPServer` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Required privilege | `OemDebug` |
| Success status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="Query SMTP Configuration"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Oem/Alert/GetSMTPServer'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="Query SMTP Configuration with a Token"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Oem/Alert/GetSMTPServer'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Oem/Alert/GetSMTPServer",
  "enable": false,
  "mail_addr": "",
  "smtp_addr": "",
  "smtp_port": 0
}
```

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Path of the SMTP configuration resource. |
| `enable` | boolean | Whether email sending is enabled. |
| `mail_addr` | string | Sender email account. |
| `smtp_addr` | string | SMTP server address. |
| `smtp_port` | integer | SMTP server port. |

<Callout title="Authorization Code Is Not Returned" type="info">
  The query interface does not return `mail_auth`, so the current SMTP configuration cannot be fully reconstructed from the GET response alone. The authorization code must be confirmed separately before writing the original values back.
</Callout>

### 2.2 Set SMTP Configuration

| Item | Content |
| --- | --- |
| Method | `POST` |
| Path | `/redfish/v1/Oem/Alert/SetSMTPServer` |
| Required privilege | `OemDebug` |
| Success status code | `200 OK` |

| Request Fields | Type | Required | Description |
| --- | --- | --- | --- |
| `mail_addr` | string | Yes | Sender email account. |
| `mail_auth` | string | Yes | SMTP authorization code or application password. |
| `smtp_addr` | string | Yes | SMTP server domain name or address. |
| `smtp_port` | integer | Yes | SMTP server port. |
| `enable` | boolean | Yes | Whether email alerts are enabled. |

```bash title="Set SMTP Configuration with a Token"
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

This interface replaces the sender configuration as a whole and writes it to the database; it was not executed this time.

#### Response Example

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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.type` | string | OData type of the Redfish message resource. |
| `Timestamp` | string | Server response timestamp. |
| `MessageId` | string | Success message identifier. |
| `Message` | string | Request processing result. |
| `MessageArgs` | array | Message formatting arguments. |
| `MessageSeverity` | string | Message severity level. |
| `Severity` | string | Severity field retained for compatibility. |
| `Oem` | object \| null | OEM extension message. |
| `RelatedProperties` | array \| null | Related property paths. |
| `Resolution` | string | Suggested resolution. |

### 2.3 Send a Test Email

| Item | Content |
| --- | --- |
| Method | `POST` |
| Path | `/redfish/v1/Oem/Alert/SendMsg` |
| Required privilege | `OemDebug` |
| Prerequisites | Valid SMTP configuration and network access to the SMTP server |

| Request Fields | Type | Required | Description |
| --- | --- | --- | --- |
| `to` | string[] | Yes | Recipient list. |
| `cc` | string[] | No | CC list. |
| `bcc` | string[] | No | BCC list. |
| `subject` | string | Yes | Email subject. |
| `msg` | string | Yes | Email body. |
| `msg_format` | string | Yes | MIME type of the body, for example `text/plain` or `text/html`. |

```json title="Request Body Example"
{
  "to": ["receiver@example.com"],
  "subject": "BMC alert test",
  "msg": "This is a test message.",
  "msg_format": "text/plain"
}
```

This operation actually sends an email to an external mailbox; it was not executed this time.

#### Response Example

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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.type` | string | OData type of the Redfish message resource. |
| `Timestamp` | string | Server response timestamp. |
| `MessageId` | string | Success message identifier. |
| `Message` | string | Request processing result. |
| `MessageArgs` | array | Message formatting arguments. |
| `MessageSeverity` | string | Message severity level. |
| `Severity` | string | Severity field retained for compatibility. |
| `Oem` | object \| null | OEM extension message. |
| `RelatedProperties` | array \| null | Related property paths. |
| `Resolution` | string | Suggested resolution. |

### 2.4 Query Alert Receivers

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Oem/Alert/GetReceivers` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Required privilege | `OemDebug` |
| Success status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="Query Alert Receivers"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Oem/Alert/GetReceivers'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="Query Alert Receivers with a Token"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Oem/Alert/GetReceivers'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Oem/Alert/GetReceivers",
  "Receivers": {}
}
```

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Path of the alert receiver resource. |
| `Receivers` | object | Receiver map keyed by receiver name. |
| `Receivers.{name}.Name` | string | Receiver name. |
| `Receivers.{name}.Way` | integer | Notification channel combination code, ranging from `1` to `8`. |
| `Receivers.{name}.Severity` | string | Severity level of alerts received. |
| `Receivers.{name}.Mail` | string | Email address. |
| `Receivers.{name}.EnterpriseWechat` | string | WeCom (Enterprise WeChat) robot webhook. |
| `Receivers.{name}.DingTalk` | string | DingTalk robot webhook. |

### 2.5 Add or Modify an Alert Receiver

| Operation | Method | Path |
| --- | --- | --- |
| Add a receiver | `POST` | `/redfish/v1/Oem/Alert/AddReceivers` |
| Modify a receiver | `POST` | `/redfish/v1/Oem/Alert/SetReceivers` |

Both interfaces use the same request structure.

| Request Fields | Type | Required | Description |
| --- | --- | --- | --- |
| `Name` | string | Yes | Unique receiver name. The modify interface uses this field to locate or create the record. |
| `Way` | integer | Yes | Channel combination: `1` no notification, `2` email, `3` WeCom, `4` DingTalk, `5` email + WeCom, `6` email + DingTalk, `7` WeCom + DingTalk, `8` all. |
| `Severity` | string | Yes | Severity level of alerts received. |
| `Mail` | string | Conditionally required | Fill in when the email channel is used. |
| `EnterpriseWechat` | string | Conditionally required | Fill in the webhook when the WeCom channel is used. |
| `DingTalk` | string | Conditionally required | Fill in the webhook when the DingTalk channel is used. |

```json title="Request Body Example"
{
  "Name": "operations-team",
  "Way": 2,
  "Severity": "Critical",
  "Mail": "operations@example.com",
  "EnterpriseWechat": "",
  "DingTalk": ""
}
```

The backend rejects duplicate names, as well as email addresses or robot webhooks that duplicate those of other receivers. No receivers were modified this time.

#### Response Example

Both add and modify return the following on success:

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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.type` | string | OData type of the Redfish message resource. |
| `Timestamp` | string | Server response timestamp. |
| `MessageId` | string | Success message identifier. |
| `Message` | string | Request processing result. |
| `MessageArgs` | array | Message formatting arguments. |
| `MessageSeverity` | string | Message severity level. |
| `Severity` | string | Severity field retained for compatibility. |
| `Oem` | object \| null | OEM extension message. |
| `RelatedProperties` | array \| null | Related property paths. |
| `Resolution` | string | Suggested resolution. |

### 2.6 Delete an Alert Receiver

| Item | Content |
| --- | --- |
| Method | `POST` |
| Path | `/redfish/v1/Oem/Alert/DelReceivers` |
| Request fields | `Name`, string, required |
| Success status code | `200 OK` |

```json title="Request Body Example"
{
  "Name": "operations-team"
}
```

If the receiver does not exist, a generic Redfish error is returned. This operation permanently deletes the receiver configuration; it was not executed this time.

#### Response Example

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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.type` | string | OData type of the Redfish message resource. |
| `Timestamp` | string | Server response timestamp. |
| `MessageId` | string | Success message identifier. |
| `Message` | string | Request processing result. |
| `MessageArgs` | array | Message formatting arguments. |
| `MessageSeverity` | string | Message severity level. |
| `Severity` | string | Severity field retained for compatibility. |
| `Oem` | object \| null | OEM extension message. |
| `RelatedProperties` | array \| null | Related property paths. |
| `Resolution` | string | Suggested resolution. |

### 2.7 Test an Alert Receiver

Triggers a test notification for one or more existing receivers.

| Item | Content |
| --- | --- |
| Method | `POST` |
| Path | `/redfish/v1/Oem/Alert/Test` |
| Request body | Array of receiver name objects |
| Success status code | `200 OK` |

```json title="Request Body Example"
[
  { "Name": "operations-team" }
]
```

The interface asynchronously invokes the email, WeCom, or DingTalk channels configured for the receivers, which may generate real external notifications; it was not executed this time.

#### Response Example

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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.type` | string | OData type of the Redfish message resource. |
| `Timestamp` | string | Server response timestamp. |
| `MessageId` | string | Success message identifier. |
| `Message` | string | Request processing result. |
| `MessageArgs` | array | Message formatting arguments. |
| `MessageSeverity` | string | Message severity level. |
| `Severity` | string | Severity field retained for compatibility. |
| `Oem` | object \| null | OEM extension message. |
| `RelatedProperties` | array \| null | Related property paths. |
| `Resolution` | string | Suggested resolution. |
