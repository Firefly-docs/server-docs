# Manager Resources

Manager resources are used to discover device managers, and to query, clear, or export BMC management logs.

<Callout title="Authentication and Access Protocol" type="info">
  The APIs on this page support HTTP Basic Auth and session tokens. The token is carried in the `X-Xsrf-Token` request header, not in the request body. In the examples, `<protocol>` is `http` or `https` depending on the service configuration.
</Callout>

## 1 Manager Resource Collection

### 1.1 Querying the Manager Resource Collection

Query the manager resources accessible to the current account.

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Managers` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Request body | None |
| Success status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query the Manager Resource Collection"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Managers'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query the Manager Resource Collection with a Token"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Managers'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

The following is a real response returned by a test device.

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Managers",
  "@odata.type": "#ManagersCollection.ManagersCollection",
  "Name": "Managers Collection",
  "Members@odata.count": 11,
  "Members": [
    { "@odata.id": "/redfish/v1/Managers/bmc" },
    { "@odata.id": "/redfish/v1/Managers/sub01" },
    { "@odata.id": "/redfish/v1/Managers/sub02" },
    { "@odata.id": "/redfish/v1/Managers/sub03" },
    { "@odata.id": "/redfish/v1/Managers/sub04" },
    { "@odata.id": "/redfish/v1/Managers/sub05" },
    { "@odata.id": "/redfish/v1/Managers/sub06" },
    { "@odata.id": "/redfish/v1/Managers/sub07" },
    { "@odata.id": "/redfish/v1/Managers/sub08" },
    { "@odata.id": "/redfish/v1/Managers/sub09" },
    { "@odata.id": "/redfish/v1/Managers/sub10" }
  ]
}
```

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Path of the manager resource collection. |
| `@odata.type` | string | OData type of the manager resource collection. |
| `Name` | string | Name of the manager resource collection. |
| `Members@odata.count` | number | Number of managers accessible to the current account. |
| `Members` | array | List of manager members. |
| `Members[].@odata.id` | string | Resource path of a specific manager. |

### 1.2 Querying a Specific Manager Resource

Query the status, time, linked resources, and console capabilities of a specific manager. The following example uses the manager identifier `bmc`.

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Managers/{ManagerId}` |
| Path parameter | `ManagerId`: manager identifier, for example `bmc` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Request body | None |
| Success status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query the bmc Manager Resource"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Managers/bmc'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query the bmc Manager Resource with a Token"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Managers/bmc'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

The following is an excerpt of the key fields from a real response.

```json title="200 OK · Real Response Excerpt"
{
  "@odata.id": "/redfish/v1/Managers/bmc",
  "@odata.type": "#Managers.v1_20_0.Managers",
  "Actions": null,
  "DateTime": "2026-08-05T02:06:31+00:00",
  "DateTimeLocalOffset": "UTC+08:00",
  "Description": "Managers",
  "Id": "bmc",
  "Links": {
    "AccountService": [{ "Id": "/redfish/v1/AccountService" }],
    "Chassis": [{ "Id": "/redfish/v1/Chassis" }],
    "Oem": [{ "Id": "/redfish/v1/Oem" }],
    "Switch": [{ "Id": "/redfish/v1/Switch" }],
    "Systems": [{ "Id": "/redfish/v1/Systems" }],
    "UpdateFwService": [{ "Id": "/redfish/v1/UpdateFwService" }],
    "UpdateFwService_V2": [{ "Id": "/redfish/v1/UpdateFwService_V2" }]
  },
  "Manufacturer": "Firefly",
  "Name": "Computer System Managers",
  "PowerState": "On",
  "SerialConsole": {
    "ConnectTypesSupported": ["Shell"],
    "MaxConcurrentSessions": 1,
    "ServiceEnabled": true
  },
  "Status": {
    "Health": "Critical",
    "HealthRollup": "",
    "State": ""
  },
  "TimeZoneName": "UTC"
}
```

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Resource path of the specific manager. |
| `@odata.type` | string | OData type of the manager resource. |
| `Actions` | object \| null | Set of actions currently exposed by the manager. |
| `DateTime` | string | Current date and time of the manager. |
| `DateTimeLocalOffset` | string | Local time zone offset reported by the manager. |
| `Description` | string | Description of the manager resource. |
| `Id` | string | Manager identifier. |
| `Links` | object | Entries to resources linked to the manager. |
| `Links.AccountService` | array | List of user account service entries. |
| `Links.AccountService[].Id` | string | User account service path. |
| `Links.Chassis` | array | List of chassis resource entries. |
| `Links.Chassis[].Id` | string | Chassis resource path. |
| `Links.Oem` | array | List of OEM resource entries. |
| `Links.Oem[].Id` | string | OEM resource path. |
| `Links.Switch` | array | List of switch resource entries. |
| `Links.Switch[].Id` | string | Switch resource path. |
| `Links.Systems` | array | List of system resource entries. |
| `Links.Systems[].Id` | string | System resource path. |
| `Links.UpdateFwService` | array | List of firmware upgrade service entries. |
| `Links.UpdateFwService[].Id` | string | Firmware upgrade service path. |
| `Links.UpdateFwService_V2` | array | List of entries to the second version of the firmware upgrade service. |
| `Links.UpdateFwService_V2[].Id` | string | Path of the second version of the firmware upgrade service. |
| `Manufacturer` | string | Manager manufacturer. |
| `Name` | string | Manager name. |
| `PowerState` | string | Manager power state. |
| `SerialConsole` | object | Serial console capabilities. |
| `SerialConsole.ConnectTypesSupported` | array | Supported console connection types. |
| `SerialConsole.MaxConcurrentSessions` | number | Maximum number of concurrent console sessions. |
| `SerialConsole.ServiceEnabled` | boolean | Whether the serial console service is enabled. |
| `Status` | object | Manager status. |
| `Status.Health` | string | Manager health status. |
| `Status.HealthRollup` | string | Manager rollup health status. |
| `Status.State` | string | Manager operating state. |
| `TimeZoneName` | string | Time zone name reported by the manager. |

## 2 Log Resources

Management logs are divided into four types.

| `LogServiceId` | Description |
| --- | --- |
| `OperateLog` | Management operation logs. |
| `RunLog` | Management service run logs. |
| `SecurityLog` | Security-related logs such as login and authentication. |
| `UpgradeLog` | Firmware or software upgrade logs. |

### 2.1 Querying the Log Service Collection

Query all management log types supported by a specific manager.

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Managers/{ManagerId}/LogServices` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Required privilege | `OemDebug` |
| Request body | None |
| Success status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="Query the Management Log Service Collection"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Managers/bmc/LogServices'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="Query the Management Log Service Collection with a Token"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Managers/bmc/LogServices'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

```json title="200 OK"
{
  "@odata.context": "/redfish/v1/$metadata#Managers/Members/bmc/LogServices/$entity",
  "@odata.id": "/redfish/v1/Managers/bmc/LogServices",
  "@odata.type": "#LogServiceCollection.LogServiceCollection",
  "Members": [
    { "@odata.id": "/redfish/v1/Managers/bmc/LogServices/OperateLog" },
    { "@odata.id": "/redfish/v1/Managers/bmc/LogServices/RunLog" },
    { "@odata.id": "/redfish/v1/Managers/bmc/LogServices/SecurityLog" },
    { "@odata.id": "/redfish/v1/Managers/bmc/LogServices/UpgradeLog" }
  ],
  "Members@odata.count": 4,
  "Name": "LogService Collection"
}
```

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.context` | string | OData metadata context of the log service collection. |
| `@odata.id` | string | Path of the log service collection. |
| `@odata.type` | string | OData type of the log service collection. |
| `Members` | array | List of log service members. |
| `Members[].@odata.id` | string | Access path of a specific log service. |
| `Members@odata.count` | number | Number of log services. |
| `Name` | string | Name of the log service collection. |

### 2.2 Querying a Specific Log Service

Query the status, entry point, and clear and export action entries of a specific log type.

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Managers/{ManagerId}/LogServices/{LogServiceId}` |
| Path parameter | `LogServiceId`: `OperateLog`, `RunLog`, `SecurityLog`, or `UpgradeLog` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Success status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="Query the Operation Log Service"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Managers/bmc/LogServices/OperateLog'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="Query the Operation Log Service with a Token"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Managers/bmc/LogServices/OperateLog'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

```json title="200 OK"
{
  "@odata.context": "/redfish/v1/$metadata#Managers/Members/bmc/LogServices/Members/$entity",
  "@odata.id": "/redfish/v1/Managers/bmc/LogServices/OperateLog",
  "@odata.type": "#LogService.v1_4_0.LogService",
  "Actions": {
    "#LogService.ClearLog": {
      "ClearType@Redfish.AllowableValue": [""],
      "target": "/redfish/v1/Managers/bmc/LogServices/OperateLog/Actions/LogService.ClearLog"
    },
    "Oem": {
      "Firefly": {
        "#LogService.ExportLog": {
          "@Redfish.ActionInfo": "/redfish/v1/Managers/bmc/LogServices/OperateLog/ExportLogActionInfo",
          "target": "/redfish/v1/Managers/bmc/LogServices/OperateLog/Actions/Oem/Firefly/LogService.ExportLog"
        }
      }
    }
  },
  "DateTime": "2026-08-05T10:06:08.292+08:00",
  "DateTimeLocalOffset": "+08:00",
  "Description": "Collection of log services for this manager",
  "Entries": {
    "@odata.id": "/redfish/v1/Managers/bmc/LogServices/OperateLog/Entries"
  },
  "Id": "OperateLog",
  "Name": "System Service Log",
  "OverWritePolicy": "NeverOverWrites",
  "OverWritePolicy@Redfish.AllowableValues": [
    "Unknown",
    "WrapsWhenFull",
    "NeverOverWrites"
  ],
  "ServiceEnabled": true
}
```

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.context` | string | OData metadata context of the specific log service. |
| `@odata.id` | string | Path of the specific log service. |
| `@odata.type` | string | OData type of the log service. |
| `Actions` | object | Set of actions supported by the log service. |
| `Actions.#LogService.ClearLog` | object | Clear log action. |
| `Actions.#LogService.ClearLog.ClearType@Redfish.AllowableValue` | array | Allowable values for the clear type; currently returns an empty string. |
| `Actions.#LogService.ClearLog.target` | string | POST path for clearing logs. |
| `Actions.Oem` | object | Set of OEM actions. |
| `Actions.Oem.Firefly` | object | Set of Firefly log actions. |
| `Actions.Oem.Firefly.#LogService.ExportLog` | object | Export log action. |
| `Actions.Oem.Firefly.#LogService.ExportLog.@Redfish.ActionInfo` | string | ActionInfo path of the export action. |
| `Actions.Oem.Firefly.#LogService.ExportLog.target` | string | POST path for exporting logs. |
| `DateTime` | string | Local date and time when the server generated the response. |
| `DateTimeLocalOffset` | string | Offset of local time from UTC. |
| `Description` | string | Description of the log service. |
| `Entries` | object | Entry point of the log entry collection. |
| `Entries.@odata.id` | string | Path of the log entry collection. |
| `Id` | string | Log service identifier. |
| `Name` | string | Log service name. |
| `OverWritePolicy` | string | Current log overwrite policy. |
| `OverWritePolicy@Redfish.AllowableValues` | array | Supported log overwrite policies. |
| `ServiceEnabled` | boolean | Whether the log service is enabled. |

### 2.3 Querying the Log Entry Collection

Query the entry paths of a specific log service with pagination.

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Managers/{ManagerId}/LogServices/{LogServiceId}/Entries` |
| Query parameter | `skip`: number of entries to skip, starting from zero; default `0` |
| Query parameter | `top`: end ordinal of the returned range; defaults to the current total number of logs when omitted |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Success status code | `200 OK` |

<Callout title="top Is Not a Return Count" type="warn">
  The current backend generates members from the ordinal range `skip + 1` to `top`. For example, `skip=0&top=3` returns entries 1 through 3; `skip=1&top=3` returns only entries 2 through 3. `top` is currently not the "return count" found in common pagination semantics.
</Callout>

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="Query the First Three Operation Log Entries"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Managers/bmc/LogServices/OperateLog/Entries?skip=0&top=3'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="Query the First Three Operation Log Entries with a Token"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Managers/bmc/LogServices/OperateLog/Entries?skip=0&top=3'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

```json title="200 OK"
{
  "@odata.context": "/redfish/v1/$metadata#LogEntryCollection.LogEntryCollection",
  "@odata.id": "/redfish/v1/Managers/bmc/LogServices/OperateLog/Entries",
  "@odata.type": "#LogEntryCollection.LogEntryCollection",
  "Description": "Collection of entries for this log service",
  "Members": [
    { "@odata.id": "/redfish/v1/Managers/bmc/LogServices/OperateLog/Entries/1" },
    { "@odata.id": "/redfish/v1/Managers/bmc/LogServices/OperateLog/Entries/2" },
    { "@odata.id": "/redfish/v1/Managers/bmc/LogServices/OperateLog/Entries/3" }
  ],
  "Members@odata.count": 3,
  "Name": "Log Service Entries Collection",
  "Oem": {
    "Firefly": {
      "MaxCount": 30
    }
  }
}
```

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.context` | string | OData metadata context of the log entry collection. |
| `@odata.id` | string | Path of the log entry collection; does not include the query string. |
| `@odata.type` | string | OData type of the log entry collection. |
| `Description` | string | Description of the log entry collection. |
| `Members` | array | List of log entries within the current query range. |
| `Members[].@odata.id` | string | Access path of a specific log entry. |
| `Members@odata.count` | number | Number of members actually returned in the current response. |
| `Name` | string | Name of the log entry collection. |
| `Oem` | object | OEM extension information. |
| `Oem.Firefly` | object | Firefly log extension information. |
| `Oem.Firefly.MaxCount` | number | Total number of logs currently stored for the specific log service. |

### 2.4 Querying a Specific Log Entry

Query the content of one management log entry by its ordinal number.

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Managers/{ManagerId}/LogServices/{LogServiceId}/Entries/{EntryId}` |
| Path parameter | `EntryId`: obtained from `Members[].@odata.id` of the log entry collection |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Success status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="Query Operation Log Entry 1"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Managers/bmc/LogServices/OperateLog/Entries/1'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="Query Operation Log Entry 1 with a Token"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Managers/bmc/LogServices/OperateLog/Entries/1'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

The following response comes from a real log entry, with the client address replaced by a placeholder.

```json title="200 OK · Client Address Masked"
{
  "@odata.context": "/redfish/v1/$metadata#Managers/Members/bmc/LogServices/OperateLog/Entries/Members/$entity",
  "@odata.id": "/redfish/v1/Managers/bmc/LogServices/OperateLog/Entries/1",
  "@odata.type": "#LogEntry.v1_1_4.LogEntry",
  "Created": "2026-05-14T10:22:16.795+08:00",
  "Description": "Manager OperateLog Log",
  "EventTimestamp": "2026-05-14T10:22:16.795+08:00",
  "Id": "1",
  "Message": "Access: /redfish/v1/UpdateFwService/Actions/UpdateFwService.SimpleUpdate; operation: control SimpleUpdate_Queue fail; reason: The property 'ImageURI' is a required property and must be included in the request.",
  "Name": "Log Entry 1",
  "Oem": {
    "Firefly": {
      "Client": "<client-ip>",
      "Interface": "LAN",
      "User": ""
    }
  },
  "Severity": "Critical"
}
```

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.context` | string | OData metadata context of the log entry. |
| `@odata.id` | string | Path of the specific log entry. |
| `@odata.type` | string | OData type of the log entry. |
| `Created` | string | Time when the log record was created. |
| `Description` | string | Description of the log entry. |
| `EventTimestamp` | string | Time when the logged event occurred. |
| `Id` | string | Ordinal number of the log entry in the current query. |
| `Message` | string | Log message body. |
| `Name` | string | Log entry name. |
| `Oem` | object | OEM extension information. |
| `Oem.Firefly` | object | Firefly log extension information. |
| `Oem.Firefly.Client` | string | Client address that initiated the related request. |
| `Oem.Firefly.Interface` | string | Interface the request came from; currently always returns `LAN`. |
| `Oem.Firefly.User` | string | Username that initiated the related request. |
| `Severity` | string | Log severity level; can be `OK`, `Warning`, or `Critical`. |

### 2.5 Clearing Logs

Permanently deletes all records under a specific manager and log type.

| Item | Content |
| --- | --- |
| Method | `POST` |
| Path | `/redfish/v1/Managers/{ManagerId}/LogServices/{LogServiceId}/Actions/LogService.ClearLog` |
| Request body | Empty JSON object `{}` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Required privilege | `OemDebug` |
| Success status code | `200 OK` |

<Callout title="Irreversible Operation" type="warn">
  This API permanently deletes all logs of the specified type from the database and compacts the log database space. Back up the logs via the export API before executing it. The clear operation was not performed during this on-device verification.
</Callout>

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="Clear Operation Logs"
    curl --request POST \
      --user '<username>:<password>' \
      --header 'Content-Type: application/json' \
      --data '{}' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Managers/bmc/LogServices/OperateLog/Actions/LogService.ClearLog'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="Clear Operation Logs with a Token"
    curl --request POST \
      --header 'X-Xsrf-Token: <token>' \
      --header 'Content-Type: application/json' \
      --data '{}' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Managers/bmc/LogServices/OperateLog/Actions/LogService.ClearLog'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

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
| `Timestamp` | string | Timestamp recorded when the server generated the response. |
| `MessageId` | string | Message identifier; `Base.1.11.0.Success` on success. |
| `Message` | string | Description of the request processing result. |
| `MessageArgs` | array | List of message formatting arguments; an empty array in this response. |
| `MessageSeverity` | string | Message severity level; `OK` on success. |
| `Severity` | string | Severity field retained for compatibility; an empty string in this response. |
| `Oem` | object \| null | OEM extension message; `null` in this response. |
| `RelatedProperties` | array \| null | Property paths related to the message; `null` in this response. |
| `Resolution` | string | Recommended resolution; `None` on success. |

### 2.7 Exporting Logs

Export a specific log type as a `.xlsx` file in Microsoft Excel 2007+ format.

| Item | Content |
| --- | --- |
| Method | `POST` |
| Path | `/redfish/v1/Managers/{ManagerId}/LogServices/{LogServiceId}/Actions/Oem/Firefly/LogService.ExportLog` |
| ActionInfo | `/redfish/v1/Managers/{ManagerId}/LogServices/{LogServiceId}/ExportLogActionInfo` |
| Request body | Empty request body |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Required privilege | `OemDebug` |
| Success status code | `200 OK` |
| Response type | XLSX binary file |

<Callout title="Content-Length Is Required" type="warn">
  The current proxy rejects POST requests without `Content-Length` and returns `502 POST or PUT without Content-Length`. The curl example uses `--data ''` to send a zero-length request body, ensuring the request includes `Content-Length: 0`.
</Callout>

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="Export Operation Logs"
    curl --request POST \
      --user '<username>:<password>' \
      --header 'Content-Type: application/json' \
      --data '' \
      --output 'bmc_OperateLog.xlsx' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Managers/bmc/LogServices/OperateLog/Actions/Oem/Firefly/LogService.ExportLog'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="Export Operation Logs with a Token"
    curl --request POST \
      --header 'X-Xsrf-Token: <token>' \
      --header 'Content-Type: application/json' \
      --data '' \
      --output 'bmc_OperateLog.xlsx' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Managers/bmc/LogServices/OperateLog/Actions/Oem/Firefly/LogService.ExportLog'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

On the real device, both Basic Auth and Token returned `200 OK`; the downloaded file passed ZIP structure inspection and was identified as a Microsoft Excel 2007+ file. The following are the real response headers from the Basic Auth verification:

```http title="200 OK"
Content-Disposition: attachment;filename=bmc_OperateLog.xlsx
Content-Type: application/vnd.openxmlformats-officedocument.spreadsheetml.sheet
Content-Length: 7561
```

#### Response Fields

| Response Header | Type | Description |
| --- | --- | --- |
| `Content-Disposition` | string | Download filename, in the format `{ManagerId}_{LogServiceId}.xlsx`. |
| `Content-Type` | string | Media type of the XLSX file. |
| `Content-Length` | number | Size of the exported file in bytes; varies with log content. |

<Callout title="Exported File Fields" type="info">
  The first row of the XLSX contains eight columns: `ID`, `CreatedAt`, `Level`, `caller`, `msg`, `type`, `log_id`, and `corename`. The export operation adds a new operation log entry, so querying the total log count again may show an increase compared to before the export.
</Callout>
