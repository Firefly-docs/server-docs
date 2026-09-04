# 机箱资源

机箱资源用于查询设备的物理机箱、主板、风扇、温度和电源状态，以及设置风扇模式、UID 指示灯和总电源状态。

<Callout title="认证与访问协议" type="info">
  本页普通 HTTP 请求支持 HTTP Basic Auth 和会话 Token。Token 通过请求头 `X-Xsrf-Token` 携带，不放在请求体中。示例中的 `<protocol>` 根据服务配置填写 `http` 或 `https`。
</Callout>


<Callout title="机箱标识" type="info">
  当前后端只注册一个机箱，`ChassisId` 为 `bmc`。客户端也应先查询机箱资源合集，再从 `Members[].@odata.id` 获取实际路径。
</Callout>

## 1 机箱资源合集

查询当前设备中的机箱资源列表。

### 请求

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Chassis` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 请求体 | 无 |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询机箱资源合集"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Chassis'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询机箱资源合集"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Chassis'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

### 响应示例

以下是测试设备返回的真实响应，HTTP 状态码为 `200 OK`。`DateTime` 为请求时间，每次查询会变化。

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

### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | 机箱资源合集的访问路径。 |
| `@odata.type` | string | 机箱资源合集的 OData 类型。 |
| `DateTime` | string | 服务器生成响应时的时间。 |
| `Description` | string | 机箱资源合集的描述。 |
| `Members` | array | 机箱资源成员列表。 |
| `Members[].@odata.id` | string | 指定机箱资源的访问路径。 |
| `Members@odata.count` | number | 机箱资源数量。 |
| `Name` | string | 机箱资源合集的名称。 |

## 2 机箱状态资源

### 2.1 查询机箱状态

查询机箱的型号、尺寸、健康状态、电源状态、UID 灯状态和 OEM 主板信息。

#### 请求

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Chassis/{ChassisId}` |
| 路径参数 | `ChassisId`：机箱标识，当前为 `bmc` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询机箱状态"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Chassis/bmc'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询机箱状态"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Chassis/bmc'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

以下为真实 `200 OK` 响应的核心字段节选。设备序列号、版本、电源和 UID 状态以实际设备为准。

```json title="200 OK · 真实响应节选"
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

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | 机箱资源的访问路径。 |
| `@odata.type` | string | 机箱资源的 OData 类型。 |
| `ChassisType` | string | 机箱类型，当前响应为 `Drawer`。 |
| `DepthMm` | number | 机箱深度，单位为毫米。 |
| `EnvironmentalClass` | string | 机箱适用的环境等级。 |
| `HeightMm` | number | 机箱高度，单位为毫米。 |
| `HotPluggable` | boolean | 机箱是否支持热插拔。 |
| `Id` | string | 机箱资源标识。 |
| `IndicatorLED` | string | UID 灯当前状态，如 `Lit`、`Blinking` 或 `Off`。 |
| `Manufacturer` | string | 机箱制造商。 |
| `MaxPowerWatts` | number | 机箱最大功率，单位为瓦。 |
| `MinPowerWatts` | number | 机箱最小功率，单位为瓦。 |
| `Name` | string | 机箱资源名称。 |
| `Oem` | object | 机箱的 OEM 扩展信息。 |
| `Oem.Firefly` | object | Firefly OEM 扩展信息。 |
| `Oem.Firefly.Mainboard` | object | Firefly 主板信息。 |
| `Oem.Firefly.Mainboard.Manufacturer` | string | 主板制造商。 |
| `Oem.Firefly.Mainboard.ProductName` | string | 主板产品名称。 |
| `Oem.Firefly.Mainboard.SerialNumber` | string | 主板序列号。 |
| `Oem.Firefly.Mainboard.ChassisShell` | string | 机箱外形规格。 |
| `Oem.Firefly.Mainboard.BoardName` | string | 主板名称。 |
| `Oem.Firefly.Mainboard.Version` | string | 主板版本。 |
| `Oem.Firefly.Mainboard.Fullname` | string | 由机箱规格、主板名称和版本组成的完整名称。 |
| `PowerState` | string | 总电源当前状态。 |
| `SerialNumber` | string | 机箱或设备序列号。 |
| `Status` | object | 机箱状态信息。 |
| `Status.Health` | string | 机箱健康状态。 |
| `Status.HealthRollup` | string | 机箱汇总健康状态。 |
| `Status.State` | string | 机箱运行状态。 |
| `ThermalDirection` | string | 机箱风道方向。 |
| `UUID` | string | 机箱的全局唯一标识。 |
| `Version` | string | 机箱硬件版本。 |
| `WeightKg` | number | 机箱重量，单位为千克。 |
| `WidthMm` | number | 机箱宽度，单位为毫米。 |

### 2.2 查询主板信息

查询 Firefly OEM 扩展的主板型号、序列号、版本和环境规格。

#### 请求

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Chassis/{ChassisId}/Oem/Firefly/Board` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询主板信息"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Chassis/bmc/Oem/Firefly/Board'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询主板信息"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Chassis/bmc/Oem/Firefly/Board'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

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

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | OEM 主板资源的访问路径。 |
| `@odata.type` | string | OEM 主板资源的 OData 类型。 |
| `Id` | string | 主板信息资源标识。 |
| `Name` | string | 主板信息资源名称。 |
| `info` | object | 主板详细信息。 |
| `info.Manufacturer` | string | 制造商信息。 |
| `info.ProductName` | string | 主板产品名称。 |
| `info.SerialNumber` | string | 主板序列号。 |
| `info.EnvironmentalClassOpt` | object | 环境等级选项。 |
| `info.EnvironmentalClassOpt.Degrees` | string | 允许的环境温度范围。 |
| `info.EnvironmentalClassOpt.RelativeHumidity` | string | 允许的相对湿度范围。 |
| `info.EnvironmentalClassOpt.Specification` | string | 适用的环境规范。 |
| `info.Oem` | object | 主板 OEM 扩展信息。 |
| `info.Oem.ChassisShell` | string | 机箱外形规格。 |
| `info.Oem.BoardName` | string | 主板名称。 |
| `info.Oem.ProductName` | string | OEM 产品名称。 |
| `info.Oem.Version` | string | 主板版本。 |
| `info.Oem.Fullname` | string | 主板完整名称。 |

## 3 风扇管理资源

### 3.1 查询散热与风扇状态

查询风扇转速、调速模式、风扇健康状态和温度传感器读数。

#### 请求

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Chassis/{ChassisId}/Thermal` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询散热与风扇状态"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Chassis/bmc/Thermal'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询散热与风扇状态"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Chassis/bmc/Thermal'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

以下是真实 `200 OK` 响应的核心字段节选。实际响应包含 6 个风扇成员，此处展示其中一个成员和一个温度传感器。转速和温度会随设备运行状态变化。

```json title="200 OK · 真实响应节选"
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

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | 散热资源的访问路径。 |
| `@odata.type` | string | 散热资源的 OData 类型。 |
| `Fans` | array | 风扇成员列表。 |
| `Fans[].MemberId` | string | 风扇成员标识。 |
| `Fans[].Model` | string | 风扇型号。 |
| `Fans[].Name` | string | 风扇名称。 |
| `Fans[].PhysicalContext` | string | 传感器所属的物理部件。 |
| `Fans[].ReadingUnits` | string | 风扇读数单位，当前为 `RPM`。 |
| `Fans[].SensorNumber` | number | 风扇传感器编号。 |
| `Fans[].Reading` | number | 风扇当前转速。 |
| `Fans[].Oem` | object | 风扇 OEM 扩展信息。 |
| `Fans[].Oem.Firefly` | object | Firefly 风扇扩展信息。 |
| `Fans[].Oem.Firefly.FanMode` | object | 风扇调速模式信息。 |
| `Fans[].Oem.Firefly.FanMode.Mode` | string | 当前风扇模式。 |
| `Fans[].Oem.Firefly.SpeedRatio` | number | 风扇转速比例。 |
| `Fans[].Oem.Firefly.Position` | string | 风扇位置描述。 |
| `Fans[].Oem.Firefly.SlotNumber` | number | 风扇插槽编号。 |
| `Fans[].Status` | object | 风扇状态信息。 |
| `Fans[].Status.Health` | string | 风扇健康状态。 |
| `Fans[].Status.HealthRollup` | string | 风扇汇总健康状态。 |
| `Fans[].Status.State` | string | 风扇运行状态。 |
| `Fans@odata.count` | number | 风扇成员数量。 |
| `Id` | string | 散热资源标识。 |
| `Name` | string | 散热资源名称。 |
| `Temperatures` | array | 温度传感器列表。 |
| `Temperatures[].PhysicalContext` | string | 温度传感器所属的物理部件。 |
| `Temperatures[].Name` | string | 温度传感器名称。 |
| `Temperatures[].ReadingCelsius` | number | 摄氏温度读数。 |
| `Temperatures[].SensorNumber` | number | 温度传感器编号。 |
| `Temperatures[].Status` | object | 温度传感器状态信息。 |
| `Temperatures[].Status.Health` | string | 温度传感器健康状态。 |
| `Temperatures[].Status.HealthRollup` | string | 温度传感器汇总健康状态。 |
| `Temperatures[].Status.State` | string | 温度传感器运行状态。 |
| `Temperatures@odata.count` | number | 温度传感器数量。 |

### 3.2 查询风扇模式参数

查询设置风扇模式时允许使用的参数、取值和数值范围。

#### 请求

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Chassis/{ChassisId}/FanModeActionInfo` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询风扇模式参数"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Chassis/bmc/FanModeActionInfo'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询风扇模式参数"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Chassis/bmc/FanModeActionInfo'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

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

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | 风扇模式 ActionInfo 资源的访问路径。 |
| `@odata.type` | string | ActionInfo 资源的 OData 类型。 |
| `Id` | string | ActionInfo 资源标识。 |
| `Name` | string | ActionInfo 资源名称。 |
| `Parameters` | array | 风扇模式设置接口的参数定义列表。 |
| `Parameters[].DisallowedInput` | boolean | 该参数是否禁止输入。 |
| `Parameters[].AllowablePattern` | string | 参数允许的字符串模式；空字符串表示未限定。 |
| `Parameters[].AllowableValues` | array | 字符串参数允许的取值列表。 |
| `Parameters[].DataType` | string | 参数数据类型。 |
| `Parameters[].Name` | string | 参数名称，如 `FanCustLevel` 或 `Mode`。 |
| `Parameters[].Required` | boolean | 参数是否必填。 |
| `Parameters[].MinimumValue` | number | 数值参数允许的最小值。 |
| `Parameters[].MaximumValue` | number | 数值参数允许的最大值。 |

### 3.3 设置风扇模式

<Callout title="会改变设备状态" type="warn">
  此操作会立即改变风扇调速策略。实机验证时设备当前模式为 `Balanced`，测试仅将 `Balanced` 原值写回，未切换风扇模式。
</Callout>

| 项目 | 内容 |
| --- | --- |
| 方法 | `POST` |
| 路径 | `/redfish/v1/Chassis/{ChassisId}/Actions/FanMode.Setting` |
| 权限 | `ConfigureComponents` |
| 请求头 | `Content-Type: application/json` |
| 成功状态码 | `200 OK` |

| 参数 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `Mode` | string | 否 | `Silent`、`Balanced`、`Powerful` 或 `Custom`。 |
| `FanCustLevel` | number | 否 | 自定义档位，范围 `1`–`20`；仅在 `Mode` 为 `Custom` 时使用。 |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="设置均衡风扇模式"
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
    ```bash title="使用 Token 设置均衡风扇模式"
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

#### 响应示例

以下是将当前风扇模式 `Balanced` 原值写回后的真实响应，HTTP 状态码为 `200 OK`。请求后重新查询 Thermal 资源，风扇模式仍为 `Balanced`。

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

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.type` | string | Redfish 消息的 OData 类型。 |
| `Timestamp` | string | 服务器生成消息时的 Unix 时间戳字符串。 |
| `MessageId` | string | Redfish 消息标识，成功时为 `Base.1.11.0.Success`。 |
| `Message` | string | 操作结果描述。 |
| `MessageArgs` | array | 用于格式化消息的参数列表。 |
| `MessageSeverity` | string | 消息严重级别，成功时为 `OK`。 |
| `Severity` | string | 兼容性严重级别字段；当前响应为空字符串。 |
| `Oem` | object \| null | OEM 扩展消息；当前响应为 `null`。 |
| `RelatedProperties` | array \| null | 与消息相关的属性路径；当前响应为 `null`。 |
| `Resolution` | string | 后续处理建议；成功时为 `None`。 |

<Callout title="旧版风扇历史路径" type="info">
  旧参考文档中的 `/redfish/v1/Chassis/bmc/Thermal/FanHistorySpeed` 在当前后端未注册，实机请求返回 `404 Not Found`，因此不作为可用 API。
</Callout>

## 4 UID管理资源

### 4.1 查询 UID 设置参数

查询 UID 指示灯支持的状态值。

#### 请求

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Chassis/{ChassisId}/Actions/IndicatorLEDActionInfo` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询 UID 设置参数"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Chassis/bmc/Actions/IndicatorLEDActionInfo'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询 UID 设置参数"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Chassis/bmc/Actions/IndicatorLEDActionInfo'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

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

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | UID ActionInfo 资源的访问路径。 |
| `@odata.type` | string | ActionInfo 资源的 OData 类型。 |
| `Id` | string | ActionInfo 资源标识。 |
| `Name` | string | ActionInfo 资源名称。 |
| `Parameters` | array | UID 指示灯设置接口的参数定义列表。 |
| `Parameters[].DisallowedInput` | boolean | 该参数是否禁止输入。 |
| `Parameters[].AllowablePattern` | string | 参数允许的字符串模式；空字符串表示未限定。 |
| `Parameters[].AllowableValues` | array | UID 指示灯允许的状态值列表。 |
| `Parameters[].DataType` | string | 参数数据类型。 |
| `Parameters[].Name` | string | 参数名称，当前为 `IndicatorLED`。 |
| `Parameters[].Required` | boolean | 参数是否必填。 |

#### `IndicatorLED` 取值

| 取值 | 说明 |
| --- | --- |
| `Lit` | UID 灯常亮。 |
| `Blinking` | UID 灯闪烁。 |
| `Off` | UID 灯关闭。 |
| `Unknown` | 未知状态；ActionInfo 会返回该值，不建议用于设置操作。 |

### 4.2 设置 UID 指示灯

<Callout title="会改变设备状态" type="warn">
  此操作会改变机箱 UID 灯状态。实机验证时 UID 灯当前状态为 `Off`，测试仅将 `Off` 原值写回，未切换指示灯状态。
</Callout>

| 项目 | 内容 |
| --- | --- |
| 方法 | `POST` |
| 路径 | `/redfish/v1/Chassis/{ChassisId}/Actions/IndicatorLED.Setting` |
| 权限 | `ConfigureComponents` |
| 请求头 | `Content-Type: application/json` |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="关闭 UID 灯"
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
    ```bash title="使用 Token 关闭 UID 灯"
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

#### 响应示例

以下是将 UID 灯当前状态 `Off` 原值写回后的真实响应，HTTP 状态码为 `200 OK`。请求后重新查询机箱资源，`IndicatorLED` 仍为 `Off`。

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

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.type` | string | Redfish 消息的 OData 类型。 |
| `Timestamp` | string | 服务器生成消息时的 Unix 时间戳字符串。 |
| `MessageId` | string | Redfish 消息标识，成功时为 `Base.1.11.0.Success`。 |
| `Message` | string | 操作结果描述。 |
| `MessageArgs` | array | 用于格式化消息的参数列表。 |
| `MessageSeverity` | string | 消息严重级别，成功时为 `OK`。 |
| `Severity` | string | 兼容性严重级别字段；当前响应为空字符串。 |
| `Oem` | object \| null | OEM 扩展消息；当前响应为 `null`。 |
| `RelatedProperties` | array \| null | 与消息相关的属性路径；当前响应为 `null`。 |
| `Resolution` | string | 后续处理建议；成功时为 `None`。 |

## 5 总电源管理资源

### 5.1 查询机箱电源信息

查询机箱功率控制信息和电源模块状态。

#### 请求

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Chassis/{ChassisId}/Power` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询机箱电源信息"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Chassis/bmc/Power'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询机箱电源信息"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Chassis/bmc/Power'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

以下是真实 `200 OK` 响应的核心字段节选。当前测试设备的 PSU 状态为 `Absent`，因此多个电气读数为 `0`。

```json title="200 OK · 真实响应节选"
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

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | 机箱电源资源的访问路径。 |
| `@odata.type` | string | 机箱电源资源的 OData 类型。 |
| `Id` | string | 机箱电源资源标识。 |
| `Name` | string | 机箱电源资源名称。 |
| `PowerControl` | object | 机箱的功率控制信息。 |
| `PowerControl.PowerAllocatedWatts` | number | 已分配功率，单位为瓦。 |
| `PowerControl.PowerAvailableWatts` | number | 可用功率，单位为瓦。 |
| `PowerControl.PowerCapacityWatts` | number | 功率容量，单位为瓦。 |
| `PowerControl.PowerConsumedWatts` | number | 当前消耗功率，单位为瓦。 |
| `PowerControl.PowerRequestedWatts` | number | 请求的功率，单位为瓦。 |
| `PowerSupplies` | array | 电源模块列表。 |
| `PowerSupplies[].@odata.id` | string | 指定电源模块在当前资源中的路径。 |
| `PowerSupplies[].EfficiencyPercent` | number | 电源转换效率百分比。 |
| `PowerSupplies[].LineInputVoltage` | number | 输入电压。 |
| `PowerSupplies[].MemberId` | string | 电源模块成员标识。 |
| `PowerSupplies[].Name` | string | 电源模块名称。 |
| `PowerSupplies[].Oem` | object | 电源模块 OEM 扩展信息。 |
| `PowerSupplies[].Oem.Firefly` | object | Firefly 电源模块扩展信息。 |
| `PowerSupplies[].Oem.Firefly.InputAmperage` | number | 输入电流。 |
| `PowerSupplies[].Oem.Firefly.LineInputCurrent` | number | 线路输入电流。 |
| `PowerSupplies[].Oem.Firefly.SlotNumber` | number | 电源模块插槽编号。 |
| `PowerSupplies[].Oem.Firefly.SlotName` | string | 电源模块插槽名称。 |
| `PowerSupplies[].PowerCapacityWatts` | number | 电源模块额定功率，单位为瓦。 |
| `PowerSupplies[].PowerInputWatts` | number | 电源模块输入功率，单位为瓦。 |
| `PowerSupplies[].PowerOutputWatts` | number | 电源模块输出功率，单位为瓦。 |
| `PowerSupplies[].Status` | object | 电源模块状态信息。 |
| `PowerSupplies[].Status.Health` | string | 电源模块健康状态。 |
| `PowerSupplies[].Status.HealthRollup` | string | 电源模块汇总健康状态。 |
| `PowerSupplies[].Status.State` | string | 电源模块运行或在位状态。 |

### 5.2 查询总电源设置参数

查询总电源操作支持的状态值。

#### 请求

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Chassis/{ChassisId}/Actions/PowerStateActionInfo` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询总电源设置参数"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Chassis/bmc/Actions/PowerStateActionInfo'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询总电源设置参数"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Chassis/bmc/Actions/PowerStateActionInfo'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

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

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | 总电源 ActionInfo 资源的访问路径。 |
| `@odata.type` | string | ActionInfo 资源的 OData 类型。 |
| `Id` | string | ActionInfo 资源标识。 |
| `Name` | string | ActionInfo 资源名称。 |
| `Parameters` | array | 总电源设置接口的参数定义列表。 |
| `Parameters[].DisallowedInput` | boolean | 该参数是否禁止输入。 |
| `Parameters[].AllowablePattern` | string | 参数允许的字符串模式；空字符串表示未限定。 |
| `Parameters[].AllowableValues` | array | 总电源允许的状态值列表，当前为 `On` 和 `Off`。 |
| `Parameters[].DataType` | string | 参数数据类型。 |
| `Parameters[].Name` | string | 参数名称，当前为 `PowerState`。 |
| `Parameters[].Required` | boolean | 参数是否必填。 |

### 5.3 设置总电源状态

<Callout title="高风险操作" type="warn">
  此操作会直接打开或关闭设备总电源，可导致业务中断或数据丢失。实机验证时设备当前状态为 `On`，测试仅将 `On` 原值写回，未执行 `Off` 下电操作。
</Callout>

| 项目 | 内容 |
| --- | --- |
| 方法 | `POST` |
| 路径 | `/redfish/v1/Chassis/{ChassisId}/Actions/PowerState.Setting` |
| 权限 | `OemPowerControl` |
| 请求头 | `Content-Type: application/json` |
| 成功状态码 | `200 OK` |

| 参数 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `PowerState` | string | 是 | `On` 表示上电，`Off` 表示下电。 |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="设置总电源上电"
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
    ```bash title="使用 Token 设置总电源上电"
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

#### 响应示例

以下是将总电源当前状态 `On` 原值写回后的真实响应，HTTP 状态码为 `200 OK`。请求后重新查询机箱资源，`PowerState` 仍为 `On`。

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

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.type` | string | Redfish 消息的 OData 类型。 |
| `Timestamp` | string | 服务器生成消息时的 Unix 时间戳字符串。 |
| `MessageId` | string | Redfish 消息标识，成功时为 `Base.1.11.0.Success`。 |
| `Message` | string | 操作结果描述。 |
| `MessageArgs` | array | 用于格式化消息的参数列表。 |
| `MessageSeverity` | string | 消息严重级别，成功时为 `OK`。 |
| `Severity` | string | 兼容性严重级别字段；当前响应为空字符串。 |
| `Oem` | object \| null | OEM 扩展消息；当前响应为 `null`。 |
| `RelatedProperties` | array \| null | 与消息相关的属性路径；当前响应为 `null`。 |
| `Resolution` | string | 后续处理建议；成功时为 `None`。 |
