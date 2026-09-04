# Redfish API Usage

This page walks through the complete integration flow in the order "verify the service, log in, access resources, submit requests, log out". All examples use placeholders and contain no real device addresses, passwords, or tokens.

## 1 Prepare Connection Information

### 1.1 Confirm the Base Address

First confirm the BMC management address, listening port, and current protocol:

```text
<protocol>://<device-ip>:<port>
```

`<protocol>` can be `http` or `https`. Whether HTTP or HTTPS is used is determined by the service configuration; do not assume HTTPS just because the port is `443`.

### 1.2 Confirm the Service Is Reachable

Use `/redfish` to query the version entry point. This endpoint requires Basic Auth or a token; the first access typically uses Basic Auth:

```bash title="Query the Redfish version entry point"
curl --user '<username>:<password>' \
  --header 'Accept: application/json' \
  '<protocol>://<device-ip>:<port>/redfish'
```

Successful response:

```json title="200 OK"
{
  "v1": "/redfish/v1/"
}
```

If the HTTPS test device uses a self-signed certificate, you may temporarily add `--insecure` after confirming the network environment is trusted. Certificate verification should not be disabled in production calls.

## 2 Obtain a Session Token

### 2.1 Login Request

Submit the username and password to the login endpoint:

| Item | Value |
| --- | --- |
| Method | `POST` |
| Path | `/redfish/v1/login` |
| Authentication | No prior authentication required |
| Request body | JSON |
| Success status code | `200 OK` |

```bash title="Login"
curl --request POST \
  --header 'Content-Type: application/json' \
  --data '{
    "username": "<username>",
    "password": "<password>"
  }' \
  '<protocol>://<device-ip>:<port>/redfish/v1/login'
```

#### Request Fields

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `username` | string | Yes | Login account. |
| `password` | string | Yes | Account password. |

### 2.2 Login Response

After a successful login, read the session token from `data.token`:

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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `code` | integer | Business result code; `200` on successful login. |
| `data` | object | Session data returned after a successful login. |
| `data.name` | string | Name of the authenticated account. |
| `data.token` | string | Session token used for subsequent requests. |
| `msg` | string | Login result description; `login success` on success. |

<Callout title="Store the Token Securely" type="warn">
  The token is equivalent to the current session credential. Do not write it into URLs, business request bodies, logs, or version control; log out immediately once you are done using it.
</Callout>

### 2.3 Handling Login Failures

The business result of the login endpoint is indicated by `code` in the response body. For example, when the credentials are invalid, the HTTP status may still be `200` while `code` is `422`:

```json title="Login failure example"
{
  "code": 422,
  "msg": "<login-error-message>"
}
```

On a JSON syntax error, the service returns HTTP `400`, and `code` in the response body is likewise `422`. Callers should check both the HTTP status and `code`, not just one of them.

## 3 Accessing Redfish Resources

### 3.1 Choosing an Authentication Method

When querying the same resource, you can switch between Basic Auth and a token:

<CodeBlockTabs defaultValue="token">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="token">
    ```bash title="Query the root resource using a token"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query the root resource using Basic Auth"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

<Callout title="Where to Carry the Token" type="info">
  The token is always placed in the `X-Xsrf-Token` request header. Even if the endpoint includes a JSON request body, do not add a `token` field to the request body.
</Callout>

### 3.2 Following Resource Links

The root resource response returns the resource entry points provided by the current device. For example:

```json title="Root resource excerpt"
{
  "Systems": {
    "@odata.id": "/redfish/v1/Systems"
  },
  "Managers": {
    "@odata.id": "/redfish/v1/Managers"
  },
  "AccountService": {
    "@odata.id": "/redfish/v1/AccountService"
  }
}
```

To continue, concatenate the base address with the `@odata.id`:

```bash title="Follow a resource entry point"
curl --header 'X-Xsrf-Token: <token>' \
  --header 'Accept: application/json' \
  '<protocol>://<device-ip>:<port>/redfish/v1/Systems'
```

The `Members` in a collection resource continue to provide individual resource paths. Clients should follow the returned `@odata.id` values instead of guessing resource numbers.

## 4 Submitting Configuration or Operations

### 4.1 Query the ActionInfo

If a resource's `Actions` contains `@Redfish.ActionInfo`, query that address first to confirm the operation parameters, required fields, data types, and allowed values:

```json title="Actions structure example"
{
  "Actions": {
    "#Resource.Action": {
      "target": "/redfish/v1/example/Actions/Resource.Action",
      "@Redfish.ActionInfo": "/redfish/v1/example/ActionInfo/Resource.Action"
    }
  }
}
```

### 4.2 Sending a JSON Request

Write operations require both the authentication header and the business request body defined by the endpoint:

<CodeBlockTabs defaultValue="token">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="token">
    ```bash title="Submit an operation using a token"
    curl --request POST \
      --header 'X-Xsrf-Token: <token>' \
      --header 'Content-Type: application/json' \
      --data '{
        "<parameter>": "<value>"
      }' \
      '<protocol>://<device-ip>:<port><action-target>'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="basic-auth">
    ```bash title="Submit an operation using Basic Auth"
    curl --request POST \
      --user '<username>:<password>' \
      --header 'Content-Type: application/json' \
      --data '{
        "<parameter>": "<value>"
      }' \
      '<protocol>://<device-ip>:<port><action-target>'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

On success, a `Base.1.11.0.Success` message is usually returned. Follow the real request bodies, response examples, and field tables provided in the specific resource chapters; do not call the device directly with the placeholder parameters above.

## 5 Logging Out the Session

### 5.1 Logout Request

After finishing operations with the token, call `logout` to delete the current session:

| Item | Value |
| --- | --- |
| Method | `POST` |
| Path | `/redfish/v1/logout` |
| Authentication | `X-Xsrf-Token` |
| Request body | None |
| Success status code | `200 OK` |

```bash title="Logout"
curl --request POST \
  --header 'Content-Length: 0' \
  --header 'X-Xsrf-Token: <token>' \
  '<protocol>://<device-ip>:<port>/redfish/v1/logout'
```

Explicitly adding `Content-Length: 0` prevents some proxies from rejecting an empty POST request that carries no length information.

### 5.2 Logout Response

```json title="200 OK"
{
  "code": 200,
  "data": "",
  "msg": ""
}
```

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `code` | integer | Business result code; `200` on successful logout. |
| `data` | string | Logout response data; an empty string on success. |
| `msg` | string | Logout result description; an empty string on success. |

After a successful logout, accessing a protected resource with the original token returns:

```json title="401 Unauthorized"
{
  "code": 401,
  "msg": "unauthorized"
}
```

## 6 FAQ

### 6.1 Cannot Establish a Connection

- Confirm that the device address and port are reachable from the client.
- Confirm that `<protocol>` matches the service's current configuration.
- If HTTPS reports a certificate error, check the certificate trust chain and hostname rather than keeping `--insecure` in production long term.

### 6.2 401 Returned

- Basic Auth: check the account, password, and account privileges.
- Token: check that the request header name is `X-Xsrf-Token`, and confirm the token has not expired or been invalidated by logout.
- Do not send both invalid Basic Auth and a valid token at the same time; choose one authentication method per request.

### 6.3 Write Operation Failed

- Confirm the request method and target path match the resource's `Actions.target`.
- Query the corresponding ActionInfo and check required fields, types, and allowed values.
- Check `Content-Type: application/json` and the JSON syntax.
- Read the HTTP status code, the Redfish `error`, `@Message.ExtendedInfo`, or the business `code` together.
