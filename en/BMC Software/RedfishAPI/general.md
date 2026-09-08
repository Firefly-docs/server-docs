# General API Conventions

This page describes the address, authentication, request, response, and error handling conventions of the Redfish API. Each resource page only supplements the paths, parameters, and business fields specific to that interface.

## 1 Address and Path

### 1.1 Base Address

Full request addresses in this document are uniformly written as:

```text
<protocol>://<device-ip>:<port><resource-path>
```

| Placeholder | Description | Example Form |
| --- | --- | --- |
| `<protocol>` | The transport protocol currently enabled on the service. | `http` or `https` |
| `<device-ip>` | The BMC management address or a resolvable hostname. | `192.0.2.10` |
| `<port>` | The port on which the Redfish service listens. | `443`, `8443` |
| `<resource-path>` | The interface path starting with `/redfish`. | `/redfish/v1/Systems` |

HTTP and HTTPS can be switched according to device configuration. The port number alone does not determine the protocol; if you see TLS handshake errors or an unexpected proxy page, first verify whether the current port actually uses HTTP or HTTPS.

### 1.2 Path Parameters

Curly braces in a path denote dynamic parameters to be substituted, for example:

```text
/redfish/v1/Systems/{system-id}
```

When calling the interface, replace `{system-id}` with the actual resource identifier returned in the collection response. Paths and field names are case-sensitive; do not keep the curly braces.

## 2 Request Conventions

### 2.1 HTTP Methods

| Method | Purpose |
| --- | --- |
| `GET` | Query resources, collections, status, or ActionInfo. |
| `POST` | Create resources or execute Redfish Actions. |
| `PUT` | Fully update configuration resources designated by the implementation. |
| `PATCH` | Modify part of a resource's properties. |
| `DELETE` | Delete resources, configuration, or sessions. |

Whether a specific interface supports a given method is subject to the "Request" table on the resource page.

### 2.2 Common Request Headers

| Request Header | Applicable Scenario | Description |
| --- | --- | --- |
| `Accept: application/json` | Recommended for all JSON queries | Declares that the client expects to receive JSON. |
| `Content-Type: application/json` | When submitting a JSON request body | Declares that the request body is JSON encoded. |
| `Authorization: Basic <credentials>` | Basic Auth | `curl --user` generates this header automatically. |
| `X-Xsrf-Token: <token>` | Token authentication | Carries the session Token returned by the login interface. |
| `Content-Length: 0` | POST without a request body | Some proxies or gateways require an explicit empty body, for example the logout interface. |

<Callout title="The Token Does Not Belong in the Request Body" type="info">
  `X-Xsrf-Token` is an authentication request header. The business request body should only contain the parameters defined by the interface; do not add an extra `token` field to the JSON.
</Callout>

### 2.3 JSON Request Body

Follow these rules when submitting JSON:

- Field names, casing, and data types must match the interface definition exactly.
- Use double quotes for strings and `true` or `false` for booleans; do not write them as strings.
- Required fields must not be omitted; optional fields should be filled in according to device capabilities and the ActionInfo.
- Do not submit fields not defined in the documentation, otherwise the service may return `PropertyUnknown` or another parameter error.

## 3 Authentication and Sessions

### 3.1 HTTP Basic Auth

Basic Auth is suitable for command-line verification or short requests in controlled environments. The username and password are sent with every request:

```bash title="Basic Auth"
curl --user '<username>:<password>' \
  --header 'Accept: application/json' \
  '<protocol>://<device-ip>:<port>/redfish/v1'
```

When using Basic Auth in production environments, enable HTTPS to prevent credentials from being transmitted over a plaintext link.

### 3.2 Session Token

Call `POST /redfish/v1/login` to obtain a Token, then add the following to each protected request:

```http title="Token Request Header"
X-Xsrf-Token: <token>
```

The Token is associated with the current session. If the Token is invalid, has been logged out, or the session has expired, the interface returns `401 Unauthorized`.

### 3.3 Logging Out a Session

After the Token is no longer needed, call `POST /redfish/v1/logout` to actively log out. After a successful logout, the original Token becomes invalid immediately.

```bash title="Log Out the Current Session"
curl --request POST \
  --header 'Content-Length: 0' \
  --header 'X-Xsrf-Token: <token>' \
  '<protocol>://<device-ip>:<port>/redfish/v1/logout'
```

## 4 Response Conventions

### 4.1 HTTP Status Codes

| Status Code | Meaning | Suggested Handling |
| --- | --- | --- |
| `200 OK` | The query or operation request has been processed by the service. | Continue to check the business fields in the response body. |
| `204 No Content` | The request succeeded with no response body; commonly used for preflight requests. | Do not attempt to parse JSON. |
| `400 Bad Request` | Malformed JSON or request parameters that do not match the definition. | Check the request body, field types, and required fields. |
| `401 Unauthorized` | Not authenticated, invalid credentials, or an expired Token. | Re-authenticate and check the request headers. |
| `404 Not Found` | The path or target resource does not exist. | Re-confirm the `@odata.id` from the collection response. |
| `405 Method Not Allowed` | The method is not supported, or the backend uses this status to indicate operation failure. | Check the details in the Redfish error body. |

<Callout title="Check Both the HTTP Status and the Response Body" type="warn">
  For the current login interface, in some authentication failure scenarios the HTTP status may be `200` while the `code` in the response body is `422`. Clients must not rely solely on the HTTP status code; they should also check the business fields or the Redfish Message defined by the interface.
</Callout>

### 4.2 Common OData Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | The access path of the current or a related resource. |
| `@odata.type` | string | The Redfish Schema type and version used by the resource. |
| `Id` | string | The resource identifier. |
| `Name` | string | The display name of the resource. |
| `Description` | string | A description of the resource's purpose. |
| `Oem` | object/null | Firefly or other vendor extension fields. |
| `Actions` | object | The operations supported by the current resource and their target paths. |

Whether a field appears depends on the resource type and device capabilities.

### 4.3 Collections and ActionInfo

Collection resources typically use the following fields:

| Field | Type | Description |
| --- | --- | --- |
| `Members` | array | The collection members; each member usually contains an `@odata.id`. |
| `Members@odata.count` | integer | The number of members in the current response. |

ActionInfo describes operation parameters. Common fields include the parameter name, whether it is required, its data type, and allowed values. Before performing a write operation, read the ActionInfo for the corresponding interface to avoid using parameters not supported by the current device.

### 4.4 General Successful Operation Response

Except for login, logout, and custom responses explicitly documented for specific interfaces, successful Redfish write operations typically return a unified Message:

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

| Field | Type | Description |
| --- | --- | --- |
| `@odata.type` | string | The OData type of the Message resource. |
| `Timestamp` | string | The Unix timestamp when the service generated the response, represented as a string. |
| `MessageId` | string | The message registry identifier; `Base.1.11.0.Success` on success. |
| `Message` | string | A human-readable description of the operation result. |
| `MessageArgs` | array | The parameters used to format the message; an empty array when there are none. |
| `MessageSeverity` | string | The Redfish message severity; `OK` on success. |
| `Severity` | string | Compatibility field; an empty string in the current success response. |
| `Oem` | object/null | Vendor extension message; `null` when there is no extension. |
| `RelatedProperties` | array/null | The request properties related to the message; `null` when there are none. |
| `Resolution` | string | The suggested handling; `None` on success. |

`Timestamp` changes on every request; clients should not perform a fixed comparison against the example value.

### 4.5 Authentication Error Response

When no valid authentication information is provided, protected interfaces return:

```json title="401 Unauthorized"
{
  "code": 401,
  "msg": "unauthorized"
}
```

Business parameter errors are usually described using the Redfish `error` and `@Message.ExtendedInfo`. Clients should read the `code`, `message`, `MessageId`, `MessageArgs`, `MessageSeverity`, and `Resolution` from them first.

## 5 Other Communication Methods

### 5.1 WebSocket

Interfaces such as remote terminal, KVM, and UID status subscription may use WebSocket. Such interfaces require an HTTP `Upgrade: websocket` handshake, and the client must pass authentication information and business parameters as described in the corresponding chapters; they do not return ordinary REST JSON responses.

### 5.2 HTTPS Certificates

Test devices may use self-signed certificates. `curl --insecure` may be used to temporarily skip certificate verification only in controlled test environments; production environments should deploy trusted certificates and keep certificate verification enabled.
