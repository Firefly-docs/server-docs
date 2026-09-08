# Upgrade Management Resources

Upgrade management resources are used to query firmware and upgrade tasks, upload local firmware, and perform firmware upgrades on the BMC or sub-boards.

<Callout title="Authentication and Privileges" type="info">
  The APIs on this page support HTTP Basic Auth and session tokens. The token is carried in the `X-Xsrf-Token` request header. Operations such as upgrade tasks, BMC self-upgrade, and chunked upload require the `OemUpgrade` privilege.
</Callout>

## 2 Upgrade Operations

### 2.1 Query the Upgrade Queue ActionInfo

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/UpdateFwService/Actions/UpdateFwServiceActionInfo` |
| Success Status Code | `200 OK` |

```bash title="Query upgrade queue parameters using a token"
curl --header 'X-Xsrf-Token: <token>' \
  --header 'Accept: application/json' \
  '<protocol>://<device-ip>:<port>/redfish/v1/UpdateFwService/Actions/UpdateFwServiceActionInfo'
```

#### Response Example

```json title="200 OK·Actual Device Response"
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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | ActionInfo resource path. |
| `@odata.type` | string | ActionInfo resource type. |
| `Id` | string | ActionInfo resource identifier. |
| `Name` | string | ActionInfo resource name. |
| `Parameters` | array | List of parameters for the upgrade queue operation. |
| `Parameters[].DisallowedInput` | boolean | Whether input for this parameter is disallowed. |
| `Parameters[].AllowablePattern` | string | Allowed matching pattern for the parameter. |
| `Parameters[].AllowableValues` | array | Allowed values for the parameter; returned only for some parameters. |
| `Parameters[].DataType` | string | Parameter data type. |
| `Parameters[].Name` | string | Parameter name. |
| `Parameters[].Required` | boolean | Whether the parameter is required. |

### 2.2 Add an Upgrade Queue Task

| Item | Content |
| --- | --- |
| Method | `POST` |
| Path | `/redfish/v1/UpdateFwService/Actions/UpdateFwService.SimpleUpdate` |
| Required Privilege | `OemUpgrade` |
| Success Status Code | `200 OK` |

<CodeBlockTabs defaultValue="local">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="local">Local Firmware</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="network">Network Firmware</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="local">
    ```bash title="Add a local upgrade task using a token"
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
    ```bash title="Add an NFS upgrade task using a token"
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

#### Request Fields

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `ImageURI` | string | Yes | Firmware file name or network URI. The exact form is determined by `TransferProtocol`. |
| `Targets` | array | Yes | List of upgrade target IDs, for example `bmc` or `sub01`. |
| `TransferProtocol` | string | No | Transfer protocol: `CIFS`, `FTP`, `SFTP`, `HTTP`, `HTTPS`, `SCP`, `TFTP`, `NFS`, or `LOCAL`. |
| `Platform` | string | Yes | Target platform. Must be selected from the `AllowableValues` currently returned by 2.1 on the actual device. |
| `RewriteMac` | boolean | No | Requested value for whether to rewrite the MAC address. The backend also decides the actual behavior based on whether the target device has an assigned MAC address. |

#### Response Example

The following is a V1 success response returned by an actual device. An empty `Targets` array was used here to verify the response format; no actual upgrade task was created.

```json title="200 OK·Actual Device Response"
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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `error` | object | V1-compatible response container. Although the field is named `error`, the inner `MessageId` indicates success. |
| `error.@Message.ExtendedInfo` | array | Redfish Message details. |
| `error.@Message.ExtendedInfo[].@odata.type` | string | Redfish Message type. |
| `error.@Message.ExtendedInfo[].Message` | string | Upgrade queue operation message. |
| `error.@Message.ExtendedInfo[].MessageArgs` | array | Message arguments. |
| `error.@Message.ExtendedInfo[].MessageId` | string | Message identifier; `Base.1.11.0.Success` on success. |
| `error.@Message.ExtendedInfo[].MessageSeverity` | string | Message severity. |
| `error.@Message.ExtendedInfo[].Resolution` | string | Suggested follow-up action. |
| `error.code` | string | V1-compatible code; currently always `code`. |
| `error.message` | string | V1-compatible message type. |

<Callout title="Targets Must Not Be Empty" type="warn">
  The current backend only checks whether the `Targets` field is present. An empty array also returns success, but no upgrade task is added for any target. Clients must validate that the array is non-empty before sending the request.
</Callout>

### 2.3 Query the BMC Self-Upgrade ActionInfo

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/UpdateFwService/Actions/UpdateFwServiceSelfUpdateActionInfo` |
| Success Status Code | `200 OK` |

```bash title="Query BMC self-upgrade parameters using a token"
curl --header 'X-Xsrf-Token: <token>' \
  --header 'Accept: application/json' \
  '<protocol>://<device-ip>:<port>/redfish/v1/UpdateFwService/Actions/UpdateFwServiceSelfUpdateActionInfo'
```

#### Response Example

```json title="200 OK·Actual Device Response"
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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | ActionInfo resource path. |
| `@odata.type` | string | ActionInfo resource type. |
| `Id` | string | ActionInfo resource identifier. |
| `Name` | string | ActionInfo resource name. |
| `Parameters` | array | List of BMC self-upgrade parameters. |
| `Parameters[].DisallowedInput` | boolean | Whether input for this parameter is disallowed. |
| `Parameters[].AllowablePattern` | string | Allowed matching pattern for the parameter. |
| `Parameters[].DataType` | string | Parameter data type. |
| `Parameters[].Name` | string | Parameter name; currently `ImageURI`. |
| `Parameters[].Required` | boolean | Whether the parameter is required. |

### 2.4 Perform a BMC Self-Upgrade

| Item | Content |
| --- | --- |
| Method | `POST` |
| Path | `/redfish/v1/UpdateFwService/Actions/UpdateFwService.SelfUpdate` |
| Required Privilege | `OemUpgrade` |
| Success Status Code | `200 OK` |

```bash title="Perform a BMC self-upgrade using a token"
curl --request POST \
  --header 'X-Xsrf-Token: <token>' \
  --header 'Content-Type: application/json' \
  --data '{
    "ImageURI": "<locally-visible-firmware-path>"
  }' \
  '<protocol>://<device-ip>:<port>/redfish/v1/UpdateFwService/Actions/UpdateFwService.SelfUpdate'
```

#### Request Fields

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `ImageURI` | string | Yes | Path to the firmware file directly accessible on the BMC itself. The backend will look up the block device, UUID, and mount point of the file. |

#### Success Response Structure

On success, the same V1 nested Message structure as in 2.2 is returned. Because this operation writes the boot configuration and triggers a BMC upgrade, the success path was not exercised this time.

```json title="200 OK·Backend Success Response Structure"
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

#### Response Fields

The response fields are identical to the "Response Fields" in 2.2.

<Callout title="Failure Path Verified on Actual Device" type="info">
  When a non-existent `ImageURI` is passed, the actual device returns `400 Bad Request`, with `error.@Message.ExtendedInfo` set to `the file path is not natively visible`.
</Callout>

<Callout title="High-Risk Operation" type="warn">
  Performing an upgrade may reboot the target device or interrupt the current service. Before submitting, always query the ActionInfo first and confirm that the image, platform, transfer protocol, and target device match.
</Callout>

## 1 Upgrade Status and Firmware

### 1.1 Query the Upgrade Task List

Query the current upgrade task status and overall progress of the BMC and each sub-board.

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/UpdateFwService/UpdateFwServiceTasksLists` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Required Privilege | `OemUpgrade` |
| Success Status Code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="Query the upgrade task list"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/UpdateFwService/UpdateFwServiceTasksLists'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="Query the upgrade task list using a token"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/UpdateFwService/UpdateFwServiceTasksLists'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

The following is an excerpt of the core fields returned by an actual device. The device also contains `sub02` through `sub10`, whose structure is identical to `sub01`.

```json title="200 OK·Excerpt of Actual Response"
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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `TasksLists` | object | Upgrade task map keyed by target ID. |
| `TasksLists.{target}` | object | Upgrade task information for the specified target, for example `bmc` or `sub01`. |
| `TasksLists.{target}.Target` | string | Task target ID; an empty string when there is no task. |
| `TasksLists.{target}.TaskUID` | integer | Unique identifier of the upgrade task; `0` when there is no task. |
| `TasksLists.{target}.TaskState` | object | Multilingual task state. |
| `TasksLists.{target}.TaskState.En` | string | Task state in English. |
| `TasksLists.{target}.TaskState.Zh_CN` | string | Task state in Chinese. |
| `TasksLists.{target}.StartTime` | string | Task start time. |
| `TasksLists.{target}.TaskPercentage` | integer | Task progress for the specified target, ranging from `0` to `100`. |
| `TasksLists.{target}.Message` | array \| null | List of upgrade log messages; may be `null` when there is no task. |
| `TaskPercentage` | integer | Average progress of all currently computable tasks. |

### 1.2 Query the Upgrade Status of a Specified Target

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/UpdateFwService/{target-id}/Actions/Oem/Firefly/UpdateFwService.ServiceInfo` |
| Path Parameter | `target-id`, for example `bmc` or `sub01` |
| Required Privilege | `OemUpgrade` |
| Success Status Code | `200 OK` |

```bash title="Query BMC upgrade status using a token"
curl --header 'X-Xsrf-Token: <token>' \
  --header 'Accept: application/json' \
  '<protocol>://<device-ip>:<port>/redfish/v1/UpdateFwService/bmc/Actions/Oem/Firefly/UpdateFwService.ServiceInfo'
```

#### Response Example

```json title="200 OK·Actual Device Response"
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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `Target` | string | Upgrade target ID. |
| `TaskUID` | integer | Upgrade task identifier. |
| `TaskState` | object | Multilingual task state. |
| `TaskState.En` | string | Task state in English. |
| `TaskState.Zh_CN` | string | Task state in Chinese. |
| `StartTime` | string | Task start time. |
| `TaskPercentage` | integer | Task progress, ranging from `0` to `100`. |
| `Message` | array \| null | List of upgrade log messages. |

### 1.3 Query the Local Firmware List

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/UpdateFwService/LocalFirmwareLists` |
| Success Status Code | `200 OK` |

```bash title="Query the local firmware list using a token"
curl --header 'X-Xsrf-Token: <token>' \
  --header 'Accept: application/json' \
  '<protocol>://<device-ip>:<port>/redfish/v1/UpdateFwService/LocalFirmwareLists'
```

#### Response Example

```json title="200 OK·Actual Device Response"
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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `FirmwareLists` | array | List of files in the BMC local firmware directory. |
| `FirmwareLists[].name` | string | Firmware file name; can be used for local upgrade or deletion operations. |
| `Types` | array \| null | Firmware type list; the current implementation returns `null`. |

### 1.4 Query Available Upgrade Firmware Paths

Query `.img` files in the upgrade upload directory. This API reads a different firmware directory from 1.3, and the two responses are not necessarily identical.

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/UpdateFwService/Actions/UpdateFwService.FirmwarePath` |
| Required Privilege | `OemUpgrade` |
| Success Status Code | `200 OK` |

```bash title="Query available upgrade firmware paths using a token"
curl --header 'X-Xsrf-Token: <token>' \
  --header 'Accept: application/json' \
  '<protocol>://<device-ip>:<port>/redfish/v1/UpdateFwService/Actions/UpdateFwService.FirmwarePath'
```

#### Response Example

```json title="200 OK·Actual Device Response"
{
  "Id": "UpdateFwServiceActionInfo",
  "Name": "UpdateFwService Action Info",
  "Parameters": []
}
```

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `Id` | string | Resource identifier. |
| `Name` | string | Resource name. |
| `Parameters` | array | Available firmware list. Each element contains `Path` and `Name`; the directory on the current device is empty. |
| `Parameters[].Path` | string | Absolute path of the firmware file. |
| `Parameters[].Name` | string | Firmware file name. |

### 1.5 Delete Local Firmware

| Item | Content |
| --- | --- |
| Method | `DELETE` |
| Path | `/redfish/v1/UpdateFwService/LocalFirmwareLists/{firmware-id}` |
| Path Parameter | `firmware-id`, obtained from `FirmwareLists[].name` returned by 1.3 |
| Request Body | None |
| Success Status Code | `200 OK` |

```bash title="Delete local firmware using a token"
curl --request DELETE \
  --header 'X-Xsrf-Token: <token>' \
  '<protocol>://<device-ip>:<port>/redfish/v1/UpdateFwService/LocalFirmwareLists/<firmware-id>'
```

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
| `@odata.type` | string | Redfish Message type. |
| `Timestamp` | string | Timestamp when the server generated the message. |
| `MessageId` | string | Success message identifier. |
| `Message` | string | Description of the operation result. |
| `MessageArgs` | array | Message arguments. |
| `MessageSeverity` | string | Message severity. |
| `Severity` | string | Compatibility severity field. |
| `Oem` | object \| null | OEM extension information. |
| `RelatedProperties` | array \| null | Related resource properties. |
| `Resolution` | string | Suggested follow-up action. |

<Callout title="Deletion Is Irreversible" type="warn">
  This API directly deletes the firmware file on the BMC and attempts to clean up the associated unpack cache. In this session, only a non-existent file name was used to verify the route and error response; no existing firmware was deleted.
</Callout>
