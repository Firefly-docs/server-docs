# Account Resources

Account resources are used to manage BMC local accounts, role privileges, and password policies.

<Callout title="Authentication and Privileges" type="info">
  The APIs on this page support HTTP Basic Auth and session tokens. The token is carried in the `X-Xsrf-Token` request header. To query or manage other accounts, the current user typically needs the `ConfigureUsers` privilege.
</Callout>

## 1 Role Management Resources

The current implementation uses the account's `Role` field to distinguish roles, and queries or configures specific privileges through the `/Privileges` resource.

<Callout title="Roles Resource Currently Unavailable" type="warn">
  The `/redfish/v1/AccountService` response contains links to `/redfish/v1/AccountService/Roles` and `/redfish/v1/AccountService/PrivilegeMap`, but the backend currently does not register these two sets of routes. Requests to a live device return `404 page not found`; clients should not follow these two links.
</Callout>

### 1.1 Querying Privileges Available for Roles

Query the configurable privilege parameters for the `Administrator` and `CommonUser` roles. The administrator role returns functional privileges, and the common user role returns the accessible board core IDs.

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/AccountService/Privileges/Help` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Request body | None |
| Success status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="Query privileges available for roles"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/AccountService/Privileges/Help'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="Query privileges available for roles using a token"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/AccountService/Privileges/Help'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

```json title="200 OK·Structure example"
{
  "Parameters": {
    "Administrator": [
      "Login",
      "ConfigureComponents",
      "ConfigureSelf",
      "ConfigureUsers",
      "OemConfigureRemote",
      "OemConfigureNetwork",
      "OemConfigureFirewall",
      "OemPowerControl",
      "OemUpgrade",
      "OemDebug",
      "OemConfigureNtp"
    ],
    "CommonUser": [
      "<core-id>"
    ]
  }
}
```

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `Parameters` | object | Available privileges grouped by role. |
| `Parameters.Administrator` | array | List of functional privilege strings configurable for the administrator. |
| `Parameters.CommonUser` | array | List of board core IDs on the current device that can be assigned to common users. |

### 1.2 Querying the Account Privilege Collection

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/AccountService/Privileges` |
| Success status code | `200 OK` |

```bash title="Query the account privilege collection using a token"
curl --header 'X-Xsrf-Token: <token>' \
  --header 'Accept: application/json' \
  '<protocol>://<device-ip>:<port>/redfish/v1/AccountService/Privileges'
```

#### Response Example

```json title="200 OK"
{
  "@odata.context": "/redfish/v1/$metadata#ManagerPrivilegeCollection.ManagerPrivilegeCollection",
  "@odata.id": "/redfish/v1/AccountService/Privileges",
  "@odata.type": "#ManagerPrivilegeCollection.ManagerPrivilegeCollection",
  "Members": [
    {
      "@odata.id": "/redfish/v1/AccountService/Privileges/admin"
    }
  ],
  "Members@odata.count": 1,
  "Name": "Privilege Collection"
}
```

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.context` | string | OData metadata context of the privilege collection. |
| `@odata.id` | string | Path of the privilege collection. |
| `@odata.type` | string | Type of the privilege collection. |
| `Members` | array | List of account privilege resource links. |
| `Members[].@odata.id` | string | Privilege resource path of the specified account. |
| `Members@odata.count` | integer | Number of privilege resources. |
| `Name` | string | Name of the privilege collection. |

### 1.3 Querying Privileges of a Specified Account

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/AccountService/Privileges/{account-id}` |
| Path parameter | `account-id`, the account name |
| Success status code | `200 OK` |

```bash title="Query privileges of a specified account using a token"
curl --header 'X-Xsrf-Token: <token>' \
  --header 'Accept: application/json' \
  '<protocol>://<device-ip>:<port>/redfish/v1/AccountService/Privileges/<account-id>'
```

#### Response Example

```json title="200 OK·Administrator"
{
  "@odata.context": "/redfish/v1/$metadata#ManagerPrivilege.ManagerPrivilege",
  "@odata.id": "/redfish/v1/AccountService/Privileges/<account-id>",
  "@odata.type": "#ManagerPrivilege.v1_0_0.ManagerPrivilege",
  "AssignedPrivileges": [
    "Login",
    "ConfigureSelf",
    "ConfigureUsers"
  ],
  "Description": "Privilege",
  "Name": "Privilege",
  "Oem": {
    "Firefly": {
      "Role": "Administrator",
      "UserName": "<account-id>"
    }
  }
}
```

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.context` | string | OData context of the account privilege resource. |
| `@odata.id` | string | Path of the current account privilege resource. |
| `@odata.type` | string | Type of the account privilege resource. |
| `AssignedPrivileges` | array \| null | Assigned privileges. `Administrator` returns functional privileges, `CommonUser` returns board core IDs, and it can be `null` when nothing is assigned. |
| `Description` | string | Resource description. |
| `Name` | string | Resource name. |
| `Oem` | object | OEM extension information. |
| `Oem.Firefly` | object | Firefly account privilege information. |
| `Oem.Firefly.Role` | string | Account role. |
| `Oem.Firefly.UserName` | string | Account name. |

### 1.4 Configuring Privileges of a Specified Account

For an `Administrator` account, pass a list of functional privileges; for a `CommonUser` account, pass a list of board core IDs. Call 1.1 first to obtain the currently available values.

| Item | Content |
| --- | --- |
| Method | `PATCH` |
| Path | `/redfish/v1/AccountService/Privileges/{account-id}` |
| Request body | JSON array of strings |
| Success status code | `200 OK` |

```bash title="Configure boards accessible to a common user using a token"
curl --request PATCH \
  --header 'X-Xsrf-Token: <token>' \
  --header 'Content-Type: application/json' \
  --data '["<core-id>"]' \
  '<protocol>://<device-ip>:<port>/redfish/v1/AccountService/Privileges/<account-id>'
```

#### Request Fields

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| Request body | array | Yes | List of privilege strings. Array elements must come from the corresponding role list returned by 1.1. |

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
| `Timestamp` | string | Timestamp string generated by the server when the message was produced. |
| `MessageId` | string | Message identifier; `Base.1.11.0.Success` on success. |
| `Message` | string | Description of the operation result. |
| `MessageArgs` | array | Message formatting arguments; an empty array on success. |
| `MessageSeverity` | string | Message severity; `OK` on success. |
| `Severity` | string | Compatibility severity field. |
| `Oem` | object \| null | OEM extension information. |
| `RelatedProperties` | array \| null | Resource properties related to the message. |
| `Resolution` | string | Suggested follow-up action. |

## 2 Account Management Resources

### 2.1 Querying the Account Collection

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/AccountService/Accounts` |
| Success status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="Query the account collection"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/AccountService/Accounts'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="Query the account collection using a token"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/AccountService/Accounts'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

```json title="200 OK"
{
  "@odata.context": "/redfish/v1/$metadata#ManagerAccountCollection.ManagerAccountCollection",
  "@odata.id": "/redfish/v1/AccountService/Accounts",
  "@odata.type": "#ManagerAccountCollection.ManagerAccountCollection",
  "Members": [
    {
      "@odata.id": "/redfish/v1/AccountService/Accounts/admin"
    }
  ],
  "Members@odata.count": 1,
  "Name": "Accounts Collection"
}
```

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.context` | string | OData context of the account collection. |
| `@odata.id` | string | Path of the account collection. |
| `@odata.type` | string | Type of the account collection. |
| `Members` | array | List of account resource links. |
| `Members[].@odata.id` | string | Resource path of the specified account. |
| `Members@odata.count` | integer | Number of accounts. |
| `Name` | string | Name of the account collection. |

### 2.2 Querying a Specified Account

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/AccountService/Accounts/{account-id}` |
| Success status code | `200 OK` |

```bash title="Query a specified account using a token"
curl --header 'X-Xsrf-Token: <token>' \
  --header 'Accept: application/json' \
  '<protocol>://<device-ip>:<port>/redfish/v1/AccountService/Accounts/<account-id>'
```

#### Response Example

```json title="200 OK·Live device structure"
{
  "@odata.context": "/redfish/v1/$metadata#ManagerAccount.ManagerAccount",
  "@odata.id": "/redfish/v1/AccountService/Accounts/<account-id>",
  "@odata.type": "#ManagerAccount.v1_10_0.ManagerAccount",
  "CreateTime": "2026-08-05T13:20:59.197+08:00",
  "Description": "User Account",
  "Email": "user@example.com",
  "Enabled": true,
  "Locked": false,
  "Name": "User Account",
  "Password": "",
  "RoleId": "CommonUser",
  "UpdateTime": "2026-08-05T13:20:59.197+08:00",
  "UserName": "<account-id>"
}
```

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.context` | string | OData context of the account resource. |
| `@odata.id` | string | Path of the current account resource. |
| `@odata.type` | string | Type of the account resource. |
| `CreateTime` | string | Account creation time. |
| `Description` | string | Account resource description. |
| `Email` | string | Account email address. |
| `Enabled` | boolean | Whether the account is enabled. |
| `Locked` | boolean | Whether the account is locked. |
| `Name` | string | Account resource name. |
| `Password` | string | Password field. Queries always return an empty string; the password or its hash is never returned. |
| `RoleId` | string | Account role, for example `Administrator` or `CommonUser`. |
| `UpdateTime` | string | Last update time of the account. |
| `UserName` | string | Account name. |

### 2.3 Creating an Account

| Item | Content |
| --- | --- |
| Method | `POST` |
| Path | `/redfish/v1/AccountService/Accounts` |
| Success status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="Create an account"
    curl --request POST \
      --user '<username>:<password>' \
      --header 'Content-Type: application/json' \
      --data '{
        "UserName": "api-user",
        "Password": "<new-password>",
        "Role": "CommonUser",
        "Email": "user@example.com",
        "Locked": false,
        "Enabled": true
      }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/AccountService/Accounts'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="Create an account using a token"
    curl --request POST \
      --header 'X-Xsrf-Token: <token>' \
      --header 'Content-Type: application/json' \
      --data '{
        "UserName": "api-user",
        "Password": "<new-password>",
        "Role": "CommonUser",
        "Email": "user@example.com",
        "Locked": false,
        "Enabled": true
      }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/AccountService/Accounts'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Request Fields

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `UserName` | string | Yes | New account name; must not duplicate an existing account. |
| `Password` | string | Yes | New account password; must satisfy the current account service length and complexity policies. |
| `Role` | string | Yes | Account role. The current creation parameters support `Administrator` and `CommonUser`. |
| `Email` | string | Yes | Account email address. |
| `Locked` | boolean | Yes | Whether the account is initially locked. |
| `Enabled` | boolean | Yes | Whether the account is initially enabled. |

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
| `Timestamp` | string | Timestamp string generated by the server when the message was produced. |
| `MessageId` | string | Message identifier; `Base.1.11.0.Success` on success. |
| `Message` | string | Description of the operation result. |
| `MessageArgs` | array | Message formatting arguments; an empty array on success. |
| `MessageSeverity` | string | Message severity; `OK` on success. |
| `Severity` | string | Compatibility severity field. |
| `Oem` | object \| null | OEM extension information. |
| `RelatedProperties` | array \| null | Resource properties related to the message. |
| `Resolution` | string | Suggested follow-up action. |

### 2.4 Modifying a Specified Account

The account's password, role, email address, enabled state, and locked state can be modified. The API does not allow modifying the username.

| Item | Content |
| --- | --- |
| Method | `PATCH` |
| Path | `/redfish/v1/AccountService/Accounts/{account-id}` |
| Success status code | `200 OK` |

```bash title="Modify an account using a token"
curl --request PATCH \
  --header 'X-Xsrf-Token: <token>' \
  --header 'Content-Type: application/json' \
  --data '{
    "Email": "updated@example.com",
    "Enabled": true,
    "Locked": false
  }' \
  '<protocol>://<device-ip>:<port>/redfish/v1/AccountService/Accounts/<account-id>'
```

#### Request Fields

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `Password` | string | No | New password. After modification, the account's existing sessions are revoked. |
| `Role` | string | No | New role. When the role changes, old privileges that do not apply to the new role are cleaned up. |
| `Email` | string | No | New email address. The current implementation does not clear the email address with an empty string. |
| `Locked` | boolean | No | Whether to lock the account. |
| `Enabled` | boolean | No | Whether to enable the account. |
| `UserName` | string | Forbidden | The username is determined by `account-id` in the URL; a non-empty `UserName` must not be passed in the request body. |

<Callout title="Limitations When Modifying Your Own Account" type="warn">
  When the current account modifies itself, only the password or email address can be changed; modifying its own role, `Enabled`, or `Locked` is not allowed.
</Callout>

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
| `Timestamp` | string | Timestamp string generated by the server when the message was produced. |
| `MessageId` | string | Message identifier; `Base.1.11.0.Success` on success. |
| `Message` | string | Description of the operation result. |
| `MessageArgs` | array | Message formatting arguments; an empty array on success. |
| `MessageSeverity` | string | Message severity; `OK` on success. |
| `Severity` | string | Compatibility severity field. |
| `Oem` | object \| null | OEM extension information. |
| `RelatedProperties` | array \| null | Resource properties related to the message. |
| `Resolution` | string | Suggested follow-up action. |

### 2.5 Deleting a Specified Account

| Item | Content |
| --- | --- |
| Method | `DELETE` |
| Path | `/redfish/v1/AccountService/Accounts/{account-id}` |
| Request body | None |
| Success status code | `200 OK` |

```bash title="Delete an account using a token"
curl --request DELETE \
  --header 'X-Xsrf-Token: <token>' \
  '<protocol>://<device-ip>:<port>/redfish/v1/AccountService/Accounts/<account-id>'
```

<Callout title="Built-in Accounts Cannot Be Deleted" type="warn">
  The backend rejects deletion of reserved built-in accounts. After an account is deleted, its existing sessions also become invalid.
</Callout>

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
| `Timestamp` | string | Timestamp string generated by the server when the message was produced. |
| `MessageId` | string | Message identifier; `Base.1.11.0.Success` on success. |
| `Message` | string | Description of the operation result. |
| `MessageArgs` | array | Message formatting arguments; an empty array on success. |
| `MessageSeverity` | string | Message severity; `OK` on success. |
| `Severity` | string | Compatibility severity field. |
| `Oem` | object \| null | OEM extension information. |
| `RelatedProperties` | array \| null | Resource properties related to the message. |
| `Resolution` | string | Suggested follow-up action. |

### 2.6 Querying Account Request Parameters

Query the account parameter help information generated by the backend.

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/AccountService/Accounts/Help` |
| Success status code | `200 OK` |

```bash title="Query account request parameters using a token"
curl --header 'X-Xsrf-Token: <token>' \
  --header 'Accept: application/json' \
  '<protocol>://<device-ip>:<port>/redfish/v1/AccountService/Accounts/Help'
```

#### Response Example

```json title="200 OK·Current implementation"
{
  "Parameters": [
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "DataType": "String",
      "Name": "Enabled",
      "Required": true
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "DataType": "String",
      "Name": "Enabled",
      "Required": true
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "AllowableValues": [
        "Administrator",
        "CommonUser"
      ],
      "DataType": "String",
      "Name": "Role",
      "Required": true
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "DataType": "String",
      "Name": "Email",
      "Required": true
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "DataType": "Boolean",
      "Name": "Locked",
      "Required": true
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "DataType": "Boolean",
      "Name": "Enabled",
      "Required": true
    }
  ]
}
```

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `Parameters` | array | List of account request parameter metadata. |
| `Parameters[].DisallowedInput` | boolean | Whether input of this parameter is disallowed. |
| `Parameters[].AllowablePattern` | string | Matching pattern for allowed values. |
| `Parameters[].AllowableValues` | array | List of selectable values; returned only for some parameters. |
| `Parameters[].DataType` | string | Data type of the parameter. |
| `Parameters[].Name` | string | Parameter name. |
| `Parameters[].Required` | boolean | Whether the parameter is required. |

<Callout title="Known Issue in the Help Response" type="warn">
  In the current `Help` response, the first two string parameters are incorrectly labeled as `Enabled`; the actual account creation request fields are `UserName` and `Password`. When calling the API, refer to the request fields table in 2.3.
</Callout>

## 3 Account Service Resources

### 3.1 Querying the Account Service Configuration

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/AccountService` |
| Success status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="Query the account service configuration"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/AccountService'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="Query the account service configuration using a token"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/AccountService'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

```json title="200 OK·Live device response"
{
  "@odata.context": "/redfish/v1/$metadata#AccountService",
  "@odata.id": "/redfish/v1/AccountService",
  "@odata.type": "#AccountService.v1_12_0.AccountService",
  "AccountLockoutDuration": 5,
  "AccountLockoutThreshold": 5,
  "Accounts": {
    "@odata.id": "/redfish/v1/AccountService/Accounts"
  },
  "Id": "AccountService",
  "MaxPasswordLength": 20,
  "MinPasswordLength": 5,
  "Name": "Account Service",
  "Oem": {
    "PasswordComplexityCheckEnabled": false,
    "PasswordComplexitynumber": false,
    "PasswordHistoryRecords": true,
    "PasswordLowercaseletters": false,
    "PasswordSpecialcharacters": false,
    "PasswordUppercaseletters": false
  },
  "PrivilegeMap": {
    "@odata.id": "/redfish/v1/AccountService/PrivilegeMap"
  },
  "Roles": {
    "@odata.id": "/redfish/v1/AccountService/Roles"
  }
}
```

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.context` | string | OData context of AccountService. |
| `@odata.id` | string | Account service resource path. |
| `@odata.type` | string | AccountService resource type. |
| `AccountLockoutDuration` | integer | Lockout duration after the lockout threshold is reached. |
| `AccountLockoutThreshold` | integer | Number of failed attempts that triggers an account lockout. |
| `Accounts` | object | Account collection resource link. |
| `Accounts.@odata.id` | string | Account collection path. |
| `Id` | string | Resource identifier. |
| `MaxPasswordLength` | integer | Maximum password length. |
| `MinPasswordLength` | integer | Minimum password length. |
| `Name` | string | Resource name. |
| `Oem` | object | Firefly password policy extensions. |
| `Oem.PasswordComplexityCheckEnabled` | boolean | Whether password complexity checking is enabled. |
| `Oem.PasswordComplexitynumber` | boolean | Whether the password must contain a digit. |
| `Oem.PasswordHistoryRecords` | boolean | Whether the password history policy is enabled. |
| `Oem.PasswordLowercaseletters` | boolean | Whether the password must contain a lowercase letter. |
| `Oem.PasswordSpecialcharacters` | boolean | Whether the password must contain a special character. |
| `Oem.PasswordUppercaseletters` | boolean | Whether the password must contain an uppercase letter. |
| `PrivilegeMap` | object | Privilege mapping resource link; the link target is currently not registered. |
| `PrivilegeMap.@odata.id` | string | Privilege mapping resource path. |
| `Roles` | object | Role collection resource link; the link target is currently not registered. |
| `Roles.@odata.id` | string | Role collection resource path. |

### 3.2 Configuring the Account Service

Modify password length, account lockout, and password complexity policies. This API supports partial updates; only the fields to be modified need to be passed.

| Item | Content |
| --- | --- |
| Method | `PATCH` |
| Path | `/redfish/v1/AccountService` |
| Success status code | `200 OK` |

```bash title="Configure the account service using a token"
curl --request PATCH \
  --header 'X-Xsrf-Token: <token>' \
  --header 'Content-Type: application/json' \
  --data '{
    "MinPasswordLength": 8,
    "MaxPasswordLength": 20,
    "AccountLockoutThreshold": 5,
    "AccountLockoutDuration": 5,
    "Oem": {
      "PasswordHistoryRecords": true,
      "PasswordComplexityCheckEnabled": true,
      "PasswordUppercaseletters": true,
      "PasswordLowercaseletters": true,
      "PasswordComplexitynumber": true,
      "PasswordSpecialcharacters": true
    }
  }' \
  '<protocol>://<device-ip>:<port>/redfish/v1/AccountService'
```

#### Request Fields

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `MinPasswordLength` | integer | No | Minimum password length. |
| `MaxPasswordLength` | integer | No | Maximum password length. |
| `AccountLockoutThreshold` | integer | No | Number of failed attempts that triggers an account lockout. |
| `AccountLockoutDuration` | integer | No | Account lockout duration. |
| `Oem` | object | No | Firefly password policy extensions. |
| `Oem.PasswordHistoryRecords` | boolean | No | Whether to enable password history records. |
| `Oem.PasswordComplexityCheckEnabled` | boolean | No | Whether to enable password complexity checking. |
| `Oem.PasswordUppercaseletters` | boolean | No | Whether uppercase letters are required. |
| `Oem.PasswordLowercaseletters` | boolean | No | Whether lowercase letters are required. |
| `Oem.PasswordComplexitynumber` | boolean | No | Whether digits are required. |
| `Oem.PasswordSpecialcharacters` | boolean | No | Whether special characters are required. |

#### Response Example

On successful configuration, a generic Message is not returned; instead, the complete updated AccountService resource is returned.

```json title="200 OK·Live device response"
{
  "@odata.context": "/redfish/v1/$metadata#AccountService",
  "@odata.id": "/redfish/v1/AccountService",
  "@odata.type": "#AccountService.v1_12_0.AccountService",
  "AccountLockoutDuration": 5,
  "AccountLockoutThreshold": 5,
  "Accounts": {
    "@odata.id": "/redfish/v1/AccountService/Accounts"
  },
  "Id": "AccountService",
  "MaxPasswordLength": 20,
  "MinPasswordLength": 5,
  "Name": "Account Service",
  "Oem": {
    "PasswordComplexityCheckEnabled": false,
    "PasswordComplexitynumber": false,
    "PasswordHistoryRecords": true,
    "PasswordLowercaseletters": false,
    "PasswordSpecialcharacters": false,
    "PasswordUppercaseletters": false
  },
  "PrivilegeMap": {
    "@odata.id": "/redfish/v1/AccountService/PrivilegeMap"
  },
  "Roles": {
    "@odata.id": "/redfish/v1/AccountService/Roles"
  }
}
```

The response fields are the same as the "Response Fields" in 3.1, with each policy field showing the updated value.

<Callout title="How Password Complexity Takes Effect" type="info">
  Only when `PasswordComplexityCheckEnabled` is `true` does the backend check new passwords against the other password policy fields. Before creating an account or changing a password, it is recommended to query the current account service configuration first.
</Callout>
