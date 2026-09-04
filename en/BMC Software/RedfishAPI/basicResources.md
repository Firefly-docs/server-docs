# Basic Resources

Basic resources are used to discover the Redfish service version and the collections of resources exposed by the device. A client can first query the version resource, then access the Service Root of the corresponding version, and follow the `@odata.id` values in the responses to continue accessing resources such as Systems, Managers, and Accounts.

<Callout title="Authentication Requirements" type="info">
  The APIs on this page support HTTP Basic Auth and session tokens. The token is not placed in the request body; instead, it is carried in the `X-Xsrf-Token` request header. Do not write real passwords or tokens into scripts, logs, or version control repositories.
</Callout>

<Callout title="Access Protocol" type="info">
  The Redfish service can switch between HTTP and HTTPS. In the examples, `<protocol>` is set to `http` or `https` according to the service configuration, and `<port>` is set to the actual listening port. The port number alone does not determine the protocol; for example, the service can also provide HTTP on port `443`.
</Callout>

**Obtaining a Session Token**

Submit the username and password to the login API. Authentication information only needs to be carried in the request body during login.

```bash title="Log in and obtain a token"
curl --request POST \
  --header 'Content-Type: application/json' \
  --data '{
    "username": "<username>",
    "password": "<password>"
  }' \
  '<protocol>://<device-ip>:<port>/redfish/v1/login'
```

After a successful login, obtain the token from `data.token` in the response:

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

Subsequent requests put the returned token into the `X-Xsrf-Token` request header:

```http title="Token request header"
X-Xsrf-Token: <token>
```

## 1 Version Resource

Query the Redfish major versions supported by the service and their entry paths. A client can use the returned paths to continue discovering the Service Root of the corresponding version.

### Request

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Request body | None |
| Success status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query the Redfish version entry"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query the Redfish version entry using a token"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

### Response Example

The following is a real response returned by a test device, with HTTP status code `200 OK`.

```json title="200 OK"
{
  "v1": "/redfish/v1/"
}
```

### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `v1` | string | Access path of the Redfish v1 Service Root. |

<Callout title="Difference Between Version Fields" type="info">
  `/redfish` returns the major version entry, for example `v1`; the `RedfishVersion` in the `/redfish/v1` response returns the detailed protocol version followed by the implementation, for example `1.9.0`. The two have different meanings.
</Callout>

### Error Response

When no valid authentication information is provided, the service returns `401 Unauthorized`, with the same response format as the Service Root API.

## 2 Service Root Collection

Query the Redfish Service Root. The response contains the service's own identifiers, protocol version, device UUID, and entry points to other resources provided by the current device.

### Request

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Request body | None |
| Success status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query the Service Root collection"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query the Service Root collection using a token"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

<Callout title="Use the Canonical Path" type="info">
  It is recommended to use `/redfish/v1` without a trailing slash. With the current service, accessing `/redfish/v1/` returns `301 Moved Permanently` and redirects to `/redfish/v1`.
</Callout>

### Response Example

The following is a real response obtained by requesting the current test environment `http://172.16.100.172:443` with a valid account, with HTTP status code `200 OK`. Switching to HTTPS does not change the response structure; device-specific fields such as `UUID` will differ on other devices.

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

### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Unique access path of the Service Root; the current implementation returns `/redfish/v1/`. |
| `@odata.type` | string | OData type of the Service Root; the current implementation is `#ServiceRoot.v1.15.0.ServiceRoot`. |
| `Id` | string | Service Root identifier; the current implementation is `RootService`. |
| `Name` | string | Service Root name; the current implementation is `Root Service`. |
| `RedfishVersion` | string | Redfish protocol version supported by the current implementation. |
| `BmcVersion` | string | Current BMC service version. |
| `UUID` | string | Globally unique identifier of the current device. |
| `AccountService` | object | Entry point to the account service resource. |
| `Managers` | object | Entry point to the BMC management resource. |
| `Oem` | object | Entry point to the Firefly OEM extension resources. |
| `Switch` | object | Entry point to the switch management resource. |
| `Systems` | object | Entry point to the Systems resource. |
| `UpdateFwService` | object | Entry point to the firmware upgrade service. |
| `UpdateFwService_V2` | object | Entry point to the second version of the firmware upgrade service. |

<Callout title="Resource Entry Points Follow the Actual Response" type="info">
  Resource entry points in the Service Root are registered by the backend at runtime. The response from the current test device does not include the `Chassis` field, so clients should not assume that every documented category appears in the Service Root response; rely on the fields actually returned.
</Callout>

### Error Response

When no valid authentication information is provided, the service returns:

```json title="401 Unauthorized"
{
  "code": 401,
  "msg": "unauthorized"
}
```

<Callout title="Log Out Promptly" type="info">
  After finishing using a token, you should send `POST /redfish/v1/logout` with that token. Once the logout succeeds, the original token becomes invalid immediately.
</Callout>

```bash title="Log out the current session"
curl --request POST \
  --header 'X-Xsrf-Token: <token>' \
  '<protocol>://<device-ip>:<port>/redfish/v1/logout'
```
