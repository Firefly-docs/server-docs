# Chassis Resources

Chassis resources are used to query the physical chassis, mainboard, fans, temperature, and power status of the device, as well as to set the fan mode, UID indicator LED, and overall power state.

<Callout title="Authentication and Access Protocol" type="info">
  Plain HTTP requests on this page support HTTP Basic Auth and session tokens. The token is carried in the `X-Xsrf-Token` request header, not in the request body. In the examples, replace `<protocol>` with `http` or `https` according to the service configuration.
</Callout>


<Callout title="Chassis Identifier" type="info">
  The current backend registers only one chassis, with `ChassisId` set to `bmc`. Clients should also query the chassis resource collection first, then obtain the actual path from `Members[].@odata.id`.
</Callout>

## 1 Chassis Resource Collection

Query the list of chassis resources on the current device.

### Request

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Chassis` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Request body | None |
| Success status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query the Chassis Resource Collection"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Chassis'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query the Chassis Resource Collection Using a Token"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Chassis'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

### Response Example

The following is a real response returned by a test device, with HTTP status code `200 OK`. `DateTime` is the request time and changes with each query.

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Chassis",
  "@odata.type": "#ComputerSystemCollection.ComputerSystemCollection",
  "DateTime": "2026-08-04T16:39:32.575+08:00",
  "Description": "The collection of Chassis resource instances",
  "Members": [
    {
      "@odata.id": "/redfish/v1/Chassis/bmc"
    }
  ],
  "Members@odata.count": 1,
  "Name": "Chassis Collection"
}
```

### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Access path of the chassis resource collection. |
| `@odata.type` | string | OData type of the chassis resource collection. |
| `DateTime` | string | Time at which the server generated the response. |
| `Description` | string | Description of the chassis resource collection. |
| `Members` | array | List of chassis resource members. |
| `Members[].@odata.id` | string | Access path of a specific chassis resource. |
| `Members@odata.count` | number | Number of chassis resources. |
| `Name` | string | Name of the chassis resource collection. |

## 2 Chassis Status Resource

### 2.1 Query Chassis Status

Query the chassis model, dimensions, health status, power state, UID LED status, and OEM mainboard information.

#### Request

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Chassis/{ChassisId}` |
| Path parameters | `ChassisId`: chassis identifier, currently `bmc` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Success status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query Chassis Status"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Chassis/bmc'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query Chassis Status Using a Token"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Chassis/bmc'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

The following is an excerpt of the core fields from a real `200 OK` response. The device serial number, version, power, and UID status depend on the actual device.

```json title="200 OK · Excerpt of a Real Response"
{
  "@odata.id": "/redfish/v1/Chassis/bmc",
  "@odata.type": "#Chassis.v1_22_0.Chassis",
  "ChassisType": "Drawer",
  "DepthMm": 421.3,
  "EnvironmentalClass": "A2",
  "HeightMm": 44.4,
  "HotPluggable": false,
  "Id": "bmc",
  "IndicatorLED": "Off",
  "Manufacturer": "Firefly",
  "MaxPowerWatts": 450,
  "MinPowerWatts": 150,
  "Name": "Computer System Chassis",
  "Oem": {
    "Firefly": {
      "Mainboard": {
        "Manufacturer": "Firefly",
        "ProductName": "CS-B1",
        "SerialNumber": "21",
        "ChassisShell": "1U",
        "BoardName": "CS-B1",
        "Version": "v1.0",
        "Fullname": "1U:CS-B1:v1.0"
      }
    }
  },
  "PowerState": "On",
  "SerialNumber": "21",
  "Status": {
    "Health": "OK",
    "HealthRollup": "OK",
    "State": "Enabled"
  },
  "ThermalDirection": "FrontToBack",
  "UUID": "a29d0b37-1414-5d2b-99fa-e6804dd73440",
  "Version": "v1.0",
  "WeightKg": 8,
  "WidthMm": 420
}
```

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Access path of the chassis resource. |
| `@odata.type` | string | OData type of the chassis resource. |
| `ChassisType` | string | Chassis type; the current response is `Drawer`. |
| `DepthMm` | number | Chassis depth in millimeters. |
| `EnvironmentalClass` | string | Environmental class applicable to the chassis. |
| `HeightMm` | number | Chassis height in millimeters. |
| `HotPluggable` | boolean | Whether the chassis supports hot-plugging. |
| `Id` | string | Chassis resource identifier. |
| `IndicatorLED` | string | Current UID LED status, such as `Lit`, `Blinking`, or `Off`. |
| `Manufacturer` | string | Chassis manufacturer. |
| `MaxPowerWatts` | number | Maximum chassis power in watts. |
| `MinPowerWatts` | number | Minimum chassis power in watts. |
| `Name` | string | Chassis resource name. |
| `Oem` | object | OEM extension information for the chassis. |
| `Oem.Firefly` | object | Firefly OEM extension information. |
| `Oem.Firefly.Mainboard` | object | Firefly mainboard information. |
| `Oem.Firefly.Mainboard.Manufacturer` | string | Mainboard manufacturer. |
| `Oem.Firefly.Mainboard.ProductName` | string | Mainboard product name. |
| `Oem.Firefly.Mainboard.SerialNumber` | string | Mainboard serial number. |
| `Oem.Firefly.Mainboard.ChassisShell` | string | Chassis form factor. |
| `Oem.Firefly.Mainboard.BoardName` | string | Mainboard name. |
| `Oem.Firefly.Mainboard.Version` | string | Mainboard version. |
| `Oem.Firefly.Mainboard.Fullname` | string | Full name composed of the chassis form factor, mainboard name, and version. |
| `PowerState` | string | Current overall power state. |
| `SerialNumber` | string | Chassis or device serial number. |
| `Status` | object | Chassis status information. |
| `Status.Health` | string | Chassis health status. |
| `Status.HealthRollup` | string | Chassis rollup health status. |
| `Status.State` | string | Chassis operational state. |
| `ThermalDirection` | string | Chassis airflow direction. |
| `UUID` | string | Universally unique identifier of the chassis. |
| `Version` | string | Chassis hardware version. |
| `WeightKg` | number | Chassis weight in kilograms. |
| `WidthMm` | number | Chassis width in millimeters. |

### 2.2 Query Mainboard Information

Query the model, serial number, version, and environmental specifications of the Firefly OEM extended mainboard.

#### Request

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Chassis/{ChassisId}/Oem/Firefly/Board` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Success status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query Mainboard Information"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Chassis/bmc/Oem/Firefly/Board'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query Mainboard Information Using a Token"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Chassis/bmc/Oem/Firefly/Board'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Chassis/bmc/Oem/Firefly/Board",
  "@odata.type": "#BoardInfo.v1_0_0.BoardInfo",
  "Id": "BoardInfo",
  "Name": "Oem Board Info",
  "info": {
    "Manufacturer": "Manufacturer",
    "ProductName": "CS-B1",
    "SerialNumber": "21",
    "EnvironmentalClassOpt": {
      "Degrees": "10°C-35°C",
      "RelativeHumidity": "20%RH-80%RH",
      "Specification": "SHRAE_A2"
    },
    "Oem": {
      "ChassisShell": "1U",
      "BoardName": "CS-B1",
      "ProductName": "CS-B1",
      "Version": "v1.0",
      "Fullname": "1U:CS-B1:v1.0"
    }
  }
}
```

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Access path of the OEM mainboard resource. |
| `@odata.type` | string | OData type of the OEM mainboard resource. |
| `Id` | string | Mainboard information resource identifier. |
| `Name` | string | Mainboard information resource name. |
| `info` | object | Detailed mainboard information. |
| `info.Manufacturer` | string | Manufacturer information. |
| `info.ProductName` | string | Mainboard product name. |
| `info.SerialNumber` | string | Mainboard serial number. |
| `info.EnvironmentalClassOpt` | object | Environmental class options. |
| `info.EnvironmentalClassOpt.Degrees` | string | Allowed ambient temperature range. |
| `info.EnvironmentalClassOpt.RelativeHumidity` | string | Allowed relative humidity range. |
| `info.EnvironmentalClassOpt.Specification` | string | Applicable environmental specification. |
| `info.Oem` | object | Mainboard OEM extension information. |
| `info.Oem.ChassisShell` | string | Chassis form factor. |
| `info.Oem.BoardName` | string | Mainboard name. |
| `info.Oem.ProductName` | string | OEM product name. |
| `info.Oem.Version` | string | Mainboard version. |
| `info.Oem.Fullname` | string | Full mainboard name. |

## 3 Fan Management Resources

### 3.1 Query Thermal and Fan Status

Query fan speed, speed-control mode, fan health status, and temperature sensor readings.

#### Request

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Chassis/{ChassisId}/Thermal` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Success status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query Thermal and Fan Status"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Chassis/bmc/Thermal'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query Thermal and Fan Status Using a Token"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Chassis/bmc/Thermal'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

The following is an excerpt of the core fields from a real `200 OK` response. The actual response contains 6 fan members; one member and one temperature sensor are shown here. Speeds and temperatures vary with the operating state of the device.

```json title="200 OK · Excerpt of a Real Response"
{
  "@odata.id": "/redfish/v1/Chassis/bmc/Thermal",
  "@odata.type": "#Thermal.v1_7_1.Thermal",
  "Fans": [
    {
      "MemberId": "5",
      "Model": "server-fan",
      "Name": "5",
      "PhysicalContext": "Fan",
      "ReadingUnits": "RPM",
      "SensorNumber": 5,
      "Reading": 6329,
      "Oem": {
        "Firefly": {
          "FanMode": {
            "Mode": "Balanced"
          },
          "SpeedRatio": 30,
          "Position": "",
          "SlotNumber": 5
        }
      },
      "Status": {
        "Health": "OK",
        "HealthRollup": "OK",
        "State": "Enabled"
      }
    }
  ],
  "Fans@odata.count": 6,
  "Id": "Thermal",
  "Name": "Thermal",
  "Temperatures": [
    {
      "PhysicalContext": "CPU",
      "Name": "Weighted mean temperature",
      "ReadingCelsius": 35,
      "SensorNumber": 0,
      "Status": {
        "Health": "OK",
        "HealthRollup": "OK",
        "State": "Enabled"
      }
    }
  ],
  "Temperatures@odata.count": 1
}
```

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Access path of the thermal resource. |
| `@odata.type` | string | OData type of the thermal resource. |
| `Fans` | array | List of fan members. |
| `Fans[].MemberId` | string | Fan member identifier. |
| `Fans[].Model` | string | Fan model. |
| `Fans[].Name` | string | Fan name. |
| `Fans[].PhysicalContext` | string | Physical component the sensor belongs to. |
| `Fans[].ReadingUnits` | string | Fan reading unit; currently `RPM`. |
| `Fans[].SensorNumber` | number | Fan sensor number. |
| `Fans[].Reading` | number | Current fan speed. |
| `Fans[].Oem` | object | Fan OEM extension information. |
| `Fans[].Oem.Firefly` | object | Firefly fan extension information. |
| `Fans[].Oem.Firefly.FanMode` | object | Fan speed-control mode information. |
| `Fans[].Oem.Firefly.FanMode.Mode` | string | Current fan mode. |
| `Fans[].Oem.Firefly.SpeedRatio` | number | Fan speed ratio. |
| `Fans[].Oem.Firefly.Position` | string | Fan position description. |
| `Fans[].Oem.Firefly.SlotNumber` | number | Fan slot number. |
| `Fans[].Status` | object | Fan status information. |
| `Fans[].Status.Health` | string | Fan health status. |
| `Fans[].Status.HealthRollup` | string | Fan rollup health status. |
| `Fans[].Status.State` | string | Fan operational state. |
| `Fans@odata.count` | number | Number of fan members. |
| `Id` | string | Thermal resource identifier. |
| `Name` | string | Thermal resource name. |
| `Temperatures` | array | List of temperature sensors. |
| `Temperatures[].PhysicalContext` | string | Physical component the temperature sensor belongs to. |
| `Temperatures[].Name` | string | Temperature sensor name. |
| `Temperatures[].ReadingCelsius` | number | Temperature reading in degrees Celsius. |
| `Temperatures[].SensorNumber` | number | Temperature sensor number. |
| `Temperatures[].Status` | object | Temperature sensor status information. |
| `Temperatures[].Status.Health` | string | Temperature sensor health status. |
| `Temperatures[].Status.HealthRollup` | string | Temperature sensor rollup health status. |
| `Temperatures[].Status.State` | string | Temperature sensor operational state. |
| `Temperatures@odata.count` | number | Number of temperature sensors. |

### 3.2 Query Fan Mode Parameters

Query the parameters, values, and numeric ranges allowed when setting the fan mode.

#### Request

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Chassis/{ChassisId}/FanModeActionInfo` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Success status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query Fan Mode Parameters"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Chassis/bmc/FanModeActionInfo'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query Fan Mode Parameters Using a Token"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Chassis/bmc/FanModeActionInfo'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Chassis/bmc/FanModeActionInfo",
  "@odata.type": "#ActionInfo.v1_1_2.ActionInfo",
  "Id": "ActionInfo",
  "Name": "Action Info",
  "Parameters": [
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "DataType": "Number",
      "Name": "FanCustLevel",
      "Required": false,
      "MinimumValue": 1,
      "MaximumValue": 20
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "AllowableValues": [
        "Silent",
        "Balanced",
        "Powerful",
        "Custom"
      ],
      "DataType": "String",
      "Name": "Mode",
      "Required": false
    }
  ]
}
```

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Access path of the fan mode ActionInfo resource. |
| `@odata.type` | string | OData type of the ActionInfo resource. |
| `Id` | string | ActionInfo resource identifier. |
| `Name` | string | ActionInfo resource name. |
| `Parameters` | array | List of parameter definitions for the fan mode setting interface. |
| `Parameters[].DisallowedInput` | boolean | Whether input is disallowed for this parameter. |
| `Parameters[].AllowablePattern` | string | String pattern allowed for the parameter; an empty string means unrestricted. |
| `Parameters[].AllowableValues` | array | List of values allowed for string parameters. |
| `Parameters[].DataType` | string | Parameter data type. |
| `Parameters[].Name` | string | Parameter name, such as `FanCustLevel` or `Mode`. |
| `Parameters[].Required` | boolean | Whether the parameter is required. |
| `Parameters[].MinimumValue` | number | Minimum value allowed for numeric parameters. |
| `Parameters[].MaximumValue` | number | Maximum value allowed for numeric parameters. |

### 3.3 Set Fan Mode

<Callout title="Changes Device State" type="warn">
  This operation immediately changes the fan speed-control policy. During on-device verification the current mode was `Balanced`; the test only wrote the `Balanced` value back as-is and did not switch the fan mode.
</Callout>

| Item | Content |
| --- | --- |
| Method | `POST` |
| Path | `/redfish/v1/Chassis/{ChassisId}/Actions/FanMode.Setting` |
| Privilege | `ConfigureComponents` |
| Request headers | `Content-Type: application/json` |
| Success status code | `200 OK` |

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `Mode` | string | No | `Silent`, `Balanced`, `Powerful`, or `Custom`. |
| `FanCustLevel` | number | No | Custom level in the range `1`–`20`; only used when `Mode` is `Custom`. |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Set the Balanced Fan Mode"
    curl --request POST \
      --user '<username>:<password>' \
      --header 'Content-Type: application/json' \
      --data '{
        "Mode": "Balanced"
      }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Chassis/bmc/Actions/FanMode.Setting'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Set the Balanced Fan Mode Using a Token"
    curl --request POST \
      --header 'X-Xsrf-Token: <token>' \
      --header 'Content-Type: application/json' \
      --data '{
        "Mode": "Balanced"
      }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Chassis/bmc/Actions/FanMode.Setting'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

The following is the real response after writing the current fan mode `Balanced` back as-is, with HTTP status code `200 OK`. Re-querying the Thermal resource after the request showed the fan mode was still `Balanced`.

```json title="200 OK"
{
  "@odata.type": "#Message.v1_1_1.Message",
  "Timestamp": "1785833173",
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
| `@odata.type` | string | OData type of the Redfish message. |
| `Timestamp` | string | Unix timestamp string of the time the server generated the message. |
| `MessageId` | string | Redfish message identifier; `Base.1.11.0.Success` on success. |
| `Message` | string | Description of the operation result. |
| `MessageArgs` | array | List of arguments used to format the message. |
| `MessageSeverity` | string | Message severity level; `OK` on success. |
| `Severity` | string | Compatibility severity field; an empty string in the current response. |
| `Oem` | object \| null | OEM extension message; `null` in the current response. |
| `RelatedProperties` | array \| null | Property paths related to the message; `null` in the current response. |
| `Resolution` | string | Suggested follow-up action; `None` on success. |

<Callout title="Legacy Fan History Path" type="info">
  The `/redfish/v1/Chassis/bmc/Thermal/FanHistorySpeed` path found in older reference documentation is not registered in the current backend; an on-device request returns `404 Not Found`, so it is not treated as a usable API.
</Callout>

## 4 UID Management Resources

### 4.1 Query UID Setting Parameters

Query the status values supported by the UID indicator LED.

#### Request

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Chassis/{ChassisId}/Actions/IndicatorLEDActionInfo` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Success status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query UID Setting Parameters"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Chassis/bmc/Actions/IndicatorLEDActionInfo'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query UID Setting Parameters Using a Token"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Chassis/bmc/Actions/IndicatorLEDActionInfo'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Chassis/bmc/Actions/IndicatorLEDActionInfo",
  "@odata.type": "#ActionInfo.v1_1_2.ActionInfo",
  "Id": "ActionInfo",
  "Name": "Action Info",
  "Parameters": [
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "AllowableValues": [
        "Unknown",
        "Lit",
        "Blinking",
        "Off"
      ],
      "DataType": "String",
      "Name": "IndicatorLED",
      "Required": true
    }
  ]
}
```

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Access path of the UID ActionInfo resource. |
| `@odata.type` | string | OData type of the ActionInfo resource. |
| `Id` | string | ActionInfo resource identifier. |
| `Name` | string | ActionInfo resource name. |
| `Parameters` | array | List of parameter definitions for the UID indicator LED setting interface. |
| `Parameters[].DisallowedInput` | boolean | Whether input is disallowed for this parameter. |
| `Parameters[].AllowablePattern` | string | String pattern allowed for the parameter; an empty string means unrestricted. |
| `Parameters[].AllowableValues` | array | List of status values allowed for the UID indicator LED. |
| `Parameters[].DataType` | string | Parameter data type. |
| `Parameters[].Name` | string | Parameter name; currently `IndicatorLED`. |
| `Parameters[].Required` | boolean | Whether the parameter is required. |

#### `IndicatorLED` Values

| Value | Description |
| --- | --- |
| `Lit` | UID LED is steady on. |
| `Blinking` | UID LED is blinking. |
| `Off` | UID LED is off. |
| `Unknown` | Unknown state; returned by ActionInfo, not recommended for set operations. |

### 4.2 Set the UID Indicator LED

<Callout title="Changes Device State" type="warn">
  This operation changes the chassis UID LED state. During on-device verification the UID LED was currently `Off`; the test only wrote the `Off` value back as-is and did not switch the indicator state.
</Callout>

| Item | Content |
| --- | --- |
| Method | `POST` |
| Path | `/redfish/v1/Chassis/{ChassisId}/Actions/IndicatorLED.Setting` |
| Privilege | `ConfigureComponents` |
| Request headers | `Content-Type: application/json` |
| Success status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Turn Off the UID LED"
    curl --request POST \
      --user '<username>:<password>' \
      --header 'Content-Type: application/json' \
      --data '{
        "IndicatorLED": "Off"
      }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Chassis/bmc/Actions/IndicatorLED.Setting'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Turn Off the UID LED Using a Token"
    curl --request POST \
      --header 'X-Xsrf-Token: <token>' \
      --header 'Content-Type: application/json' \
      --data '{
        "IndicatorLED": "Off"
      }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Chassis/bmc/Actions/IndicatorLED.Setting'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

The following is the real response after writing the UID LED's current state `Off` back as-is, with HTTP status code `200 OK`. Re-querying the chassis resource after the request showed `IndicatorLED` was still `Off`.

```json title="200 OK"
{
  "@odata.type": "#Message.v1_1_1.Message",
  "Timestamp": "1785833173",
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
| `@odata.type` | string | OData type of the Redfish message. |
| `Timestamp` | string | Unix timestamp string of the time the server generated the message. |
| `MessageId` | string | Redfish message identifier; `Base.1.11.0.Success` on success. |
| `Message` | string | Description of the operation result. |
| `MessageArgs` | array | List of arguments used to format the message. |
| `MessageSeverity` | string | Message severity level; `OK` on success. |
| `Severity` | string | Compatibility severity field; an empty string in the current response. |
| `Oem` | object \| null | OEM extension message; `null` in the current response. |
| `RelatedProperties` | array \| null | Property paths related to the message; `null` in the current response. |
| `Resolution` | string | Suggested follow-up action; `None` on success. |

## 5 Overall Power Management Resources

### 5.1 Query Chassis Power Information

Query chassis power control information and power supply unit status.

#### Request

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Chassis/{ChassisId}/Power` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Success status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query Chassis Power Information"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Chassis/bmc/Power'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query Chassis Power Information Using a Token"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Chassis/bmc/Power'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

The following is an excerpt of the core fields from a real `200 OK` response. The PSU status of the current test device is `Absent`, so several electrical readings are `0`.

```json title="200 OK · Excerpt of a Real Response"
{
  "@odata.id": "/redfish/v1/Chassis/bmc/Power",
  "@odata.type": "#Power_v1_7_1_Power",
  "Id": "Power",
  "Name": "Chassis Power",
  "PowerControl": {
    "PowerAllocatedWatts": 0,
    "PowerAvailableWatts": 0,
    "PowerCapacityWatts": 0,
    "PowerConsumedWatts": 0,
    "PowerRequestedWatts": 0
  },
  "PowerSupplies": [
    {
      "@odata.id": "/redfish/v1/Chassis/bmc/Power#/PowerSupplies/0",
      "EfficiencyPercent": 0,
      "LineInputVoltage": 0,
      "MemberId": "0",
      "Name": "PSU 1",
      "Oem": {
        "Firefly": {
          "InputAmperage": 0,
          "LineInputCurrent": 0,
          "SlotNumber": 1,
          "SlotName": "Top"
        }
      },
      "PowerCapacityWatts": 0,
      "PowerInputWatts": 0,
      "PowerOutputWatts": 0,
      "Status": {
        "Health": "Warning",
        "HealthRollup": "Warning",
        "State": "Absent"
      }
    }
  ]
}
```

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Access path of the chassis power resource. |
| `@odata.type` | string | OData type of the chassis power resource. |
| `Id` | string | Chassis power resource identifier. |
| `Name` | string | Chassis power resource name. |
| `PowerControl` | object | Chassis power control information. |
| `PowerControl.PowerAllocatedWatts` | number | Allocated power in watts. |
| `PowerControl.PowerAvailableWatts` | number | Available power in watts. |
| `PowerControl.PowerCapacityWatts` | number | Power capacity in watts. |
| `PowerControl.PowerConsumedWatts` | number | Currently consumed power in watts. |
| `PowerControl.PowerRequestedWatts` | number | Requested power in watts. |
| `PowerSupplies` | array | List of power supply units. |
| `PowerSupplies[].@odata.id` | string | Path of a specific power supply unit within this resource. |
| `PowerSupplies[].EfficiencyPercent` | number | Power conversion efficiency percentage. |
| `PowerSupplies[].LineInputVoltage` | number | Input voltage. |
| `PowerSupplies[].MemberId` | string | Power supply member identifier. |
| `PowerSupplies[].Name` | string | Power supply unit name. |
| `PowerSupplies[].Oem` | object | Power supply OEM extension information. |
| `PowerSupplies[].Oem.Firefly` | object | Firefly power supply extension information. |
| `PowerSupplies[].Oem.Firefly.InputAmperage` | number | Input amperage. |
| `PowerSupplies[].Oem.Firefly.LineInputCurrent` | number | Line input current. |
| `PowerSupplies[].Oem.Firefly.SlotNumber` | number | Power supply slot number. |
| `PowerSupplies[].Oem.Firefly.SlotName` | string | Power supply slot name. |
| `PowerSupplies[].PowerCapacityWatts` | number | Rated power of the power supply unit in watts. |
| `PowerSupplies[].PowerInputWatts` | number | Input power of the power supply unit in watts. |
| `PowerSupplies[].PowerOutputWatts` | number | Output power of the power supply unit in watts. |
| `PowerSupplies[].Status` | object | Power supply status information. |
| `PowerSupplies[].Status.Health` | string | Power supply health status. |
| `PowerSupplies[].Status.HealthRollup` | string | Power supply rollup health status. |
| `PowerSupplies[].Status.State` | string | Power supply operational or presence state. |

### 5.2 Query Overall Power Setting Parameters

Query the status values supported by overall power operations.

#### Request

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Chassis/{ChassisId}/Actions/PowerStateActionInfo` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Success status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query Overall Power Setting Parameters"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Chassis/bmc/Actions/PowerStateActionInfo'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query Overall Power Setting Parameters Using a Token"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Chassis/bmc/Actions/PowerStateActionInfo'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Chassis/bmc/Actions/PowerStateActionInfo",
  "@odata.type": "#ActionInfo.v1_1_2.ActionInfo",
  "Id": "ActionInfo",
  "Name": "Action Info",
  "Parameters": [
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "AllowableValues": [
        "On",
        "Off"
      ],
      "DataType": "String",
      "Name": "PowerState",
      "Required": true
    }
  ]
}
```

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Access path of the overall power ActionInfo resource. |
| `@odata.type` | string | OData type of the ActionInfo resource. |
| `Id` | string | ActionInfo resource identifier. |
| `Name` | string | ActionInfo resource name. |
| `Parameters` | array | List of parameter definitions for the overall power setting interface. |
| `Parameters[].DisallowedInput` | boolean | Whether input is disallowed for this parameter. |
| `Parameters[].AllowablePattern` | string | String pattern allowed for the parameter; an empty string means unrestricted. |
| `Parameters[].AllowableValues` | array | List of status values allowed for overall power; currently `On` and `Off`. |
| `Parameters[].DataType` | string | Parameter data type. |
| `Parameters[].Name` | string | Parameter name; currently `PowerState`. |
| `Parameters[].Required` | boolean | Whether the parameter is required. |

### 5.3 Set Overall Power State

<Callout title="High-Risk Operation" type="warn">
  This operation directly turns the device's overall power on or off and may cause service interruption or data loss. During on-device verification the device was currently `On`; the test only wrote the `On` value back as-is and did not perform an `Off` power-down operation.
</Callout>

| Item | Content |
| --- | --- |
| Method | `POST` |
| Path | `/redfish/v1/Chassis/{ChassisId}/Actions/PowerState.Setting` |
| Privilege | `OemPowerControl` |
| Request headers | `Content-Type: application/json` |
| Success status code | `200 OK` |

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `PowerState` | string | Yes | `On` means power on; `Off` means power off. |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Set Overall Power On"
    curl --request POST \
      --user '<username>:<password>' \
      --header 'Content-Type: application/json' \
      --data '{
        "PowerState": "On"
      }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Chassis/bmc/Actions/PowerState.Setting'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Set Overall Power On Using a Token"
    curl --request POST \
      --header 'X-Xsrf-Token: <token>' \
      --header 'Content-Type: application/json' \
      --data '{
        "PowerState": "On"
      }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Chassis/bmc/Actions/PowerState.Setting'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

The following is the real response after writing the overall power's current state `On` back as-is, with HTTP status code `200 OK`. Re-querying the chassis resource after the request showed `PowerState` was still `On`.

```json title="200 OK"
{
  "@odata.type": "#Message.v1_1_1.Message",
  "Timestamp": "1785833173",
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
| `@odata.type` | string | OData type of the Redfish message. |
| `Timestamp` | string | Unix timestamp string of the time the server generated the message. |
| `MessageId` | string | Redfish message identifier; `Base.1.11.0.Success` on success. |
| `Message` | string | Description of the operation result. |
| `MessageArgs` | array | List of arguments used to format the message. |
| `MessageSeverity` | string | Message severity level; `OK` on success. |
| `Severity` | string | Compatibility severity field; an empty string in the current response. |
| `Oem` | object \| null | OEM extension message; `null` in the current response. |
| `RelatedProperties` | array \| null | Property paths related to the message; `null` in the current response. |
| `Resolution` | string | Suggested follow-up action; `None` on success. |
