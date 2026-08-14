# 系统资源

系统资源用于查询设备中的系统节点、操作系统状态、网络、时间、存储、电源和防火墙信息。

<Callout title="认证与访问协议" type="info">
  本页普通 HTTP 请求支持 HTTP Basic Auth 和会话 Token。Token 通过请求头 `X-Xsrf-Token` 携带，不放在请求体中。示例中的 `<protocol>` 根据服务配置填写 `http` 或 `https`。
</Callout>

## 1 系统资源合集

查询当前设备中的系统资源成员。客户端应从 `Members[].@odata.id` 获取真实 `SystemId`，不应假定系统成员数量或名称固定。

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 请求体 | 无 |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询系统资源合集"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询系统资源合集"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

### 响应示例

以下是测试设备返回的真实响应，HTTP 状态码为 `200 OK`。

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Systems",
  "@odata.type": "#ComputerSystemCollection.ComputerSystemCollection",
  "Name": "Computer System Collection",
  "Members@odata.count": 11,
  "Members": [
    { "@odata.id": "/redfish/v1/Systems/bmc" },
    { "@odata.id": "/redfish/v1/Systems/sub01" },
    { "@odata.id": "/redfish/v1/Systems/sub02" },
    { "@odata.id": "/redfish/v1/Systems/sub03" },
    { "@odata.id": "/redfish/v1/Systems/sub04" },
    { "@odata.id": "/redfish/v1/Systems/sub05" },
    { "@odata.id": "/redfish/v1/Systems/sub06" },
    { "@odata.id": "/redfish/v1/Systems/sub07" },
    { "@odata.id": "/redfish/v1/Systems/sub08" },
    { "@odata.id": "/redfish/v1/Systems/sub09" },
    { "@odata.id": "/redfish/v1/Systems/sub10" }
  ]
}
```

### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | 系统资源合集的访问路径。 |
| `@odata.type` | string | 系统资源合集的 OData 类型。 |
| `Name` | string | 系统资源合集名称。 |
| `Members@odata.count` | number | 系统资源成员数量。 |
| `Members` | array | 系统资源成员列表。 |
| `Members[].@odata.id` | string | 指定系统资源的访问路径。 |

## 2 系统状态资源

### 2.1 查询指定系统

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/{SystemId}` |
| 路径参数 | `SystemId`：从系统资源合集获取，以下示例使用 `bmc` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询 bmc 系统状态"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询 bmc 系统状态"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

以下是真实 `200 OK` 响应的核心字段节选。

```json title="200 OK · 真实响应节选"
{
  "@odata.id": "/redfish/v1/Systems/bmc",
  "@odata.type": "#ComputerSystem.v1_20_0.ComputerSystem",
  "Id": "bmc",
  "Name": "Systems",
  "SystemType": "Physical",
  "Description": "System bmc",
  "Status": {
    "Health": "Critical",
    "HealthRollup": "",
    "State": ""
  },
  "SerialNumber": "5a674dc0e7ccb348",
  "ProcessorSummary": {
    "CoreCount": 8,
    "Count": 1,
    "LogicalProcessorCount": 8,
    "Model": "Cortex-A55",
    "ThreadingEnabled": false,
    "Status": {
      "Health": "OK",
      "HealthRollup": "OK",
      "State": "Enabled"
    }
  },
  "HostingRoles": "BareMetalServer",
  "HostName": "bmc",
  "Manufacturer": "Firefly",
  "ManufacturingMode": false,
  "UUID": "a1f1c73c-3fc0-5358-8ea8-b5c29ec52c48"
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | 系统资源的访问路径。 |
| `@odata.type` | string | 系统资源的 OData 类型。 |
| `Id` | string | 系统资源标识。 |
| `Name` | string | 系统资源名称。 |
| `SystemType` | string | 系统类型。 |
| `Description` | string | 系统资源描述。 |
| `Status` | object | 系统状态信息。 |
| `Status.Health` | string | 系统健康状态。 |
| `Status.HealthRollup` | string | 系统汇总健康状态。 |
| `Status.State` | string | 系统运行状态。 |
| `SerialNumber` | string | 系统序列号。 |
| `ProcessorSummary` | object | 处理器汇总信息。 |
| `ProcessorSummary.CoreCount` | number | 处理器核心总数。 |
| `ProcessorSummary.Count` | number | 物理处理器数量。 |
| `ProcessorSummary.LogicalProcessorCount` | number | 逻辑处理器数量。 |
| `ProcessorSummary.Model` | string | 处理器型号。 |
| `ProcessorSummary.ThreadingEnabled` | boolean | 是否启用硬件线程。 |
| `ProcessorSummary.Status` | object | 处理器状态信息。 |
| `ProcessorSummary.Status.Health` | string | 处理器健康状态。 |
| `ProcessorSummary.Status.HealthRollup` | string | 处理器汇总健康状态。 |
| `ProcessorSummary.Status.State` | string | 处理器运行状态。 |
| `HostingRoles` | string | 系统承担的主机角色。 |
| `HostName` | string | 系统主机名。 |
| `Manufacturer` | string | 系统制造商。 |
| `ManufacturingMode` | boolean | 是否处于制造模式。 |
| `UUID` | string | 系统全局唯一标识。 |

### 2.2 其他系统状态接口

| 方法 | 路径 | 说明 | 实机验证 |
| --- | --- | --- | --- |
| `GET` | `/redfish/v1/Systems/{SystemId}/Oem/Firefly/Status` | 查询操作系统、内核、SoC 和 BMC 软件状态。 | `200 OK` |
| `GET` | `/redfish/v1/Systems/{SystemId}/Oem/Firefly/ProductSpecifications` | 查询内存、存储和 SoC 产品规格。 | `200 OK` |

## 3 网络管理资源

### 3.1 查询网口资源合集

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/{SystemId}/EthernetInterfaces` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询网口资源合集"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/EthernetInterfaces'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询网口资源合集"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/EthernetInterfaces'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Systems/bmc/EthernetInterfaces",
  "@odata.type": "#EthernetInterfaceCollection.EthernetInterfaceCollection",
  "Name": "Ethernet Interface Collection",
  "Members@odata.count": 6,
  "Members": [
    { "@odata.id": "/redfish/v1/Systems/bmc/EthernetInterfaces/nic3" },
    { "@odata.id": "/redfish/v1/Systems/bmc/EthernetInterfaces/MGMT" },
    { "@odata.id": "/redfish/v1/Systems/bmc/EthernetInterfaces/lo" },
    { "@odata.id": "/redfish/v1/Systems/bmc/EthernetInterfaces/nic0" },
    { "@odata.id": "/redfish/v1/Systems/bmc/EthernetInterfaces/nic1" },
    { "@odata.id": "/redfish/v1/Systems/bmc/EthernetInterfaces/nic2" }
  ]
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | 网口资源合集的访问路径。 |
| `@odata.type` | string | 网口资源合集的 OData 类型。 |
| `Name` | string | 网口资源合集名称。 |
| `Members@odata.count` | number | 网口数量。 |
| `Members` | array | 网口资源成员列表。 |
| `Members[].@odata.id` | string | 指定网口的访问路径。 |

### 3.2 查询指定网口

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/{SystemId}/EthernetInterfaces/{EthernetInterfaceId}` |
| 路径参数 | `EthernetInterfaceId`：从网口资源合集获取，以下示例使用非管理网口 `nic0` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="查询非管理网口 nic0"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/EthernetInterfaces/nic0'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询非管理网口 nic0"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/EthernetInterfaces/nic0'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK · 真实响应节选"
{
  "@odata.id": "/redfish/v1/Systems/bmc/EthernetInterfaces/nic0",
  "@odata.type": "#EthernetInterface.v1_9_0.EthernetInterface",
  "AutoNeg": false,
  "DHCPv4": false,
  "EthernetInterfaceType": "Physical",
  "FQDN": "bmc",
  "FullDuplex": "full",
  "HostName": "bmc",
  "IPv4Addresses": [
    {
      "Address": "172.22.10.0",
      "SubnetMask": "255.255.0.0",
      "PrefixLen": 16,
      "AddressOrigin": "Static"
    }
  ],
  "Id": "nic0",
  "InterfaceEnabled": false,
  "LinkStatus": "1",
  "MACAddress": "42:07:6e:26:82:7b",
  "MTUSize": "1500",
  "Name": "nic0",
  "NameServers": ["114.114.114.114"],
  "Routes": [],
  "Speed": "1000"
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | 网口资源的访问路径。 |
| `@odata.type` | string | 网口资源的 OData 类型。 |
| `AutoNeg` | boolean | 是否启用自协商。 |
| `DHCPv4` | boolean | 是否通过 DHCPv4 获取地址。 |
| `EthernetInterfaceType` | string | 网口类型。 |
| `FQDN` | string | 完全限定域名。 |
| `FullDuplex` | string | 双工模式。 |
| `HostName` | string | 主机名。 |
| `IPv4Addresses` | array | IPv4 地址列表。 |
| `IPv4Addresses[].Address` | string | IPv4 地址。 |
| `IPv4Addresses[].SubnetMask` | string | IPv4 子网掩码。 |
| `IPv4Addresses[].PrefixLen` | number | IPv4 前缀长度。 |
| `IPv4Addresses[].AddressOrigin` | string | IPv4 地址来源。 |
| `Id` | string | 网口标识。 |
| `InterfaceEnabled` | boolean | 网口是否启用。 |
| `LinkStatus` | string | 网口链路状态。 |
| `MACAddress` | string | MAC 地址。 |
| `MTUSize` | string | 最大传输单元。 |
| `Name` | string | 网口名称。 |
| `NameServers` | array | DNS 服务器列表。 |
| `Routes` | array | 静态路由列表。 |
| `Speed` | string | 网口速率，单位为 Mbps。 |

### 3.3 查询 Bond 资源合集

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/{SystemId}/NetworkBondings` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="查询 Bond 资源合集"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkBondings'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询 Bond 资源合集"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkBondings'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Systems/bmc/NetworkBondings",
  "@odata.type": "#FFNetworkBondingCollection.FFNetworkBondingCollection",
  "Name": "Ethernet Bonding Collection",
  "Members@odata.count": 2,
  "Members": [
    { "@odata.id": "/redfish/v1/Systems/bmc/NetworkBondings/bond1" },
    { "@odata.id": "/redfish/v1/Systems/bmc/NetworkBondings/bond0" }
  ]
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | Bond 资源合集路径。 |
| `@odata.type` | string | Bond 资源合集的 OData 类型。 |
| `Name` | string | Bond 资源合集名称。 |
| `Members@odata.count` | number | Bond 数量。 |
| `Members` | array | Bond 成员列表。 |
| `Members[].@odata.id` | string | 指定 Bond 的访问路径。 |

### 3.4 查询指定 Bond

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/{SystemId}/NetworkBondings/{NetworkBondingId}` |
| 路径参数 | `NetworkBondingId`：从 Bond 资源合集获取，以下示例使用 `bond1` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="查询 bond1"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkBondings/bond1'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询 bond1"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkBondings/bond1'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK · 真实响应节选"
{
  "@odata.id": "/redfish/v1/Systems/bmc/NetworkBondings/bond1",
  "@odata.type": "#EthNetworkBonding.v1_9_0.EthNetworkBonding",
  "Id": "bond1",
  "Name": "bond1",
  "IPv4Addresses": [],
  "MACAddress": "de:38:b5:b6:5f:1d",
  "LinkStatus": "1",
  "Oem": {
    "Firefly": {
      "Mode": "802.3ad",
      "Interfaces": ["nic2"],
      "MIIStatus": "up",
      "SettingEnabled": true,
      "Bonding": true,
      "BondingMaster": "bond1"
    }
  }
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | Bond 资源路径。 |
| `@odata.type` | string | Bond 资源的 OData 类型。 |
| `Id` | string | Bond 标识。 |
| `Name` | string | Bond 名称。 |
| `IPv4Addresses` | array | Bond 的 IPv4 地址列表。 |
| `MACAddress` | string | Bond 的 MAC 地址。 |
| `LinkStatus` | string | Bond 链路状态。 |
| `Oem` | object | OEM 扩展信息。 |
| `Oem.Firefly` | object | Firefly Bond 扩展信息。 |
| `Oem.Firefly.Mode` | string | Bond 模式。 |
| `Oem.Firefly.Interfaces` | array | Bond 包含的物理网口。 |
| `Oem.Firefly.MIIStatus` | string | MII 链路状态。 |
| `Oem.Firefly.SettingEnabled` | boolean | 是否允许配置。 |
| `Oem.Firefly.Bonding` | boolean | 是否为 Bond 接口。 |
| `Oem.Firefly.BondingMaster` | string | Bond 主接口名称。 |

### 3.5 查询 VLAN 资源合集

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/{SystemId}/NetworkVLANs` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="查询 VLAN 资源合集"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkVLANs'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询 VLAN 资源合集"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkVLANs'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Systems/bmc/NetworkVLANs",
  "@odata.type": "#EthernetInterface.v1_9_0.EthernetInterface",
  "Name": "Ethernet VLAN Collection",
  "Members@odata.count": 2,
  "Members": [
    { "@odata.id": "/redfish/v1/Systems/bmc/NetworkVLANs/vlan4000" },
    { "@odata.id": "/redfish/v1/Systems/bmc/NetworkVLANs/vlan400" }
  ]
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | VLAN 资源合集路径。 |
| `@odata.type` | string | VLAN 资源合集的 OData 类型。 |
| `Name` | string | VLAN 资源合集名称。 |
| `Members@odata.count` | number | VLAN 数量。 |
| `Members` | array | VLAN 成员列表。 |
| `Members[].@odata.id` | string | 指定 VLAN 的访问路径。 |

### 3.6 查询指定 VLAN

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/{SystemId}/NetworkVLANs/{NetworkVLANId}` |
| 路径参数 | `NetworkVLANId`：从 VLAN 资源合集获取，以下示例使用 `vlan400` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="查询 vlan400"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkVLANs/vlan400'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询 vlan400"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkVLANs/vlan400'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

以下是实机返回的 `200 OK` 核心字段，`vlan400` 关联非管理网口 `nic3`。

```json title="200 OK · 真实响应节选"
{
  "@odata.id": "/redfish/v1/Systems/bmc/NetworkVLANs/vlan400",
  "@odata.type": "#EthNetworkVLAN.v1_9_0.EthNetworkVLAN",
  "Id": "vlan400",
  "Name": "vlan400",
  "DHCPv4": false,
  "EthernetInterfaceType": "Virtual",
  "IPv4Addresses": [],
  "InterfaceEnabled": false,
  "LinkStatus": "1",
  "MACAddress": "56:bd:58:51:7f:f7",
  "MTUSize": "1500",
  "NameServers": ["114.114.114.114"],
  "Routes": [],
  "Speed": "1000",
  "Oem": {
    "Firefly": {
      "VlanId": "400",
      "VlanLink": "nic3",
      "SettingEnabled": true,
      "HaveConfig": false,
      "NicType": 4
    }
  }
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | VLAN 资源路径。 |
| `@odata.type` | string | VLAN 资源的 OData 类型。 |
| `Id` | string | VLAN 资源标识。 |
| `Name` | string | VLAN 名称。 |
| `DHCPv4` | boolean | 是否通过 DHCPv4 获取地址。 |
| `EthernetInterfaceType` | string | 接口类型，VLAN 为虚拟接口。 |
| `IPv4Addresses` | array | VLAN 的 IPv4 地址列表。 |
| `InterfaceEnabled` | boolean | VLAN 接口是否启用。 |
| `LinkStatus` | string | VLAN 链路状态。 |
| `MACAddress` | string | VLAN 接口的 MAC 地址。 |
| `MTUSize` | string | 最大传输单元。 |
| `NameServers` | array | DNS 服务器列表。 |
| `Routes` | array | 静态路由列表。 |
| `Speed` | string | 接口速率，单位为 Mbps。 |
| `Oem` | object | OEM 扩展信息。 |
| `Oem.Firefly` | object | Firefly VLAN 扩展信息。 |
| `Oem.Firefly.VlanId` | string | IEEE 802.1Q VLAN ID。 |
| `Oem.Firefly.VlanLink` | string | VLAN 依附的底层网口。 |
| `Oem.Firefly.SettingEnabled` | boolean | 是否允许配置该 VLAN。 |
| `Oem.Firefly.HaveConfig` | boolean | 后端是否检测到持久化配置。 |
| `Oem.Firefly.NicType` | number | 后端定义的网口类型编号。 |

### 3.7 配置非管理网口

<Callout title="禁止直接修改管理网口" type="warn">
  配置前必须先查询网口资源，确认当前管理 IP 不在目标网口上。实机测试确认管理地址位于 `MGMT`，因此只对 `nic1` 执行测试。
</Callout>

| 项目 | 内容 |
| --- | --- |
| 方法 | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/EthernetInterfaces/{EthernetInterfaceId}/Actions/Oem/Firefly/ConfigureActionInfo` |
| 路径 | `/redfish/v1/Systems/{SystemId}/EthernetInterfaces/{EthernetInterfaceId}/Actions/Oem/Firefly/EthernetInterface.Configure` |
| 权限 | `OemConfigureNetwork` |

| 请求字段 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `IPv4Addresses` | object | 是 | IPv4 配置对象。 |
| `IPv4Addresses.Dhcp4` | boolean | 否 | 是否启用 DHCPv4。 |
| `IPv4Addresses.Addresses` | object[] | 否 | 静态 IPv4 地址列表。 |
| `IPv4Addresses.Addresses[].Address` | string | 是 | IPv4 地址。 |
| `IPv4Addresses.Addresses[].PrefixLen` | number | 是 | 网络前缀长度。 |
| `IPv4Addresses.Gateway4` | string | 否 | 默认网关地址。 |
| `IPv4Addresses.Gateway4Priority` | number | 否 | 默认网关优先级。 |
| `IPv4Addresses.Optional` | boolean | 否 | 启动时是否允许该接口未就绪。 |
| `IPv4Addresses.NameServers` | string[] | 否 | DNS 服务器列表。 |
| `Routes` | object[] | 否 | 静态路由列表。 |
| `Routes[].To` | string | 是 | 路由目标网段。 |
| `Routes[].Via` | string | 是 | 下一跳地址。 |
| `Routes[].Metric` | number | 否 | 路由度量值。 |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="配置非管理网口 nic1"
    curl --request POST \
      --user '<username>:<password>' \
      --header 'Content-Type: application/json' \
      --data '{
        "IPv4Addresses": {
          "Dhcp4": false,
          "Addresses": [{ "Address": "198.51.100.10", "PrefixLen": 24 }],
          "NameServers": [],
          "Optional": true
        },
        "Routes": []
      }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/EthernetInterfaces/nic1/Actions/Oem/Firefly/EthernetInterface.Configure'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="使用 Token 配置非管理网口 nic1"
    curl --request POST \
      --header 'X-Xsrf-Token: <token>' \
      --header 'Content-Type: application/json' \
      --data '{
        "IPv4Addresses": {
          "Dhcp4": false,
          "Addresses": [{ "Address": "198.51.100.10", "PrefixLen": 24 }],
          "NameServers": [],
          "Optional": true
        },
        "Routes": []
      }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/EthernetInterfaces/nic1/Actions/Oem/Firefly/EthernetInterface.Configure'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

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
| `Timestamp` | string | 服务端生成响应时记录的时间戳。 |
| `MessageId` | string | 消息标识，成功时为 `Base.1.11.0.Success`。 |
| `Message` | string | 请求处理结果说明。 |
| `MessageArgs` | array | 消息格式化参数列表；本响应为空数组。 |
| `MessageSeverity` | string | 消息严重级别，成功时为 `OK`。 |
| `Severity` | string | 兼容保留的严重级别字段；本响应为空字符串。 |
| `Oem` | object \| null | OEM 扩展消息；本响应为 `null`。 |
| `RelatedProperties` | array \| null | 与消息相关的属性路径；本响应为 `null`。 |
| `Resolution` | string | 建议的处理方式；成功时为 `None`。 |

<Callout title="成功消息不代表运行态已变更" type="info">
  当次 POST 返回 `200 OK`，但随后 GET `nic1` 时 `IPv4Addresses` 仍为空数组。客户端必须在配置后重新查询资源，不能只根据成功消息判断配置已生效。
</Callout>

### 3.8 配置 Bond

| 项目 | 内容 |
| --- | --- |
| 方法 | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/NetworkBondings/{NetworkBondingId}/Actions/Oem/Firefly/ConfigureActionInfo` |
| 路径 | `/redfish/v1/Systems/{SystemId}/NetworkBondings/{NetworkBondingId}/Actions/Oem/Firefly/NetworkBonding.Configure` |
| 实机对象 | `bond1`，成员为非管理网口 `nic2` |
| 成功状态码 | `200 OK` |

| 请求字段 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `Mode` | string | 是 | Bond 模式：`802.3ad`、`active-backup`、`balance-alb`、`balance-rr`、`balance-tlb`、`balance-xor` 或 `broadcast`。 |
| `Interfaces` | string[] | 是 | Bond 的物理网口成员，不应包含承载管理地址的网口。 |
| `IPv4Addresses` | object | 是 | IPv4 配置。 |
| `IPv4Addresses.Dhcp4` | boolean | 否 | 是否启用 DHCPv4。 |
| `IPv4Addresses.Addresses` | object[] | 否 | 静态 IPv4 地址列表。 |
| `IPv4Addresses.Addresses[].Address` | string | 是 | IPv4 地址。 |
| `IPv4Addresses.Addresses[].PrefixLen` | number | 是 | 网络前缀长度。 |
| `IPv4Addresses.Gateway4` | string | 否 | 默认网关。 |
| `IPv4Addresses.Gateway4Priority` | number | 否 | 默认网关优先级。 |
| `IPv4Addresses.Optional` | boolean | 否 | 启动时是否允许该接口未就绪。 |
| `IPv4Addresses.NameServers` | string[] | 否 | DNS 服务器。 |
| `Routes` | object[] | 否 | 静态路由列表，每项支持 `To`、`Via` 和可选 `Metric`。 |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="配置 bond1"
    curl --request POST \
      --user '<username>:<password>' \
      --header 'Content-Type: application/json' \
      --data '{
        "Mode": "802.3ad",
        "Interfaces": ["nic2"],
        "IPv4Addresses": {
          "Dhcp4": false,
          "Addresses": [],
          "NameServers": ["114.114.114.114"],
          "Optional": true
        },
        "Routes": []
      }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkBondings/bond1/Actions/Oem/Firefly/NetworkBonding.Configure'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="使用 Token 配置 bond1"
    curl --request POST \
      --header 'X-Xsrf-Token: <token>' \
      --header 'Content-Type: application/json' \
      --data '{
        "Mode": "802.3ad",
        "Interfaces": ["nic2"],
        "IPv4Addresses": {
          "Dhcp4": false,
          "Addresses": [],
          "NameServers": ["114.114.114.114"],
          "Optional": true
        },
        "Routes": []
      }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkBondings/bond1/Actions/Oem/Firefly/NetworkBonding.Configure'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

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
| `Timestamp` | string | 服务端生成响应时记录的时间戳。 |
| `MessageId` | string | 消息标识，成功时为 `Base.1.11.0.Success`。 |
| `Message` | string | 请求处理结果说明。 |
| `MessageArgs` | array | 消息格式化参数列表；本响应为空数组。 |
| `MessageSeverity` | string | 消息严重级别，成功时为 `OK`。 |
| `Severity` | string | 兼容保留的严重级别字段；本响应为空字符串。 |
| `Oem` | object \| null | OEM 扩展消息；本响应为 `null`。 |
| `RelatedProperties` | array \| null | 与消息相关的属性路径；本响应为 `null`。 |
| `Resolution` | string | 建议的处理方式；成功时为 `None`。 |

实机将 `bond1` 原配置写回后，再次 GET 确认其模式仍为 `802.3ad`，成员仍为 `nic2`。

### 3.9 新增 Bond

| 项目 | 内容 |
| --- | --- |
| 方法 | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/NetworkBondings/Actions/Oem/Firefly/AddConfigureActionInfo` |
| 路径 | `/redfish/v1/Systems/{SystemId}/NetworkBondings/Actions/Oem/Firefly/NetworkBondingAdd.Configure` |
| 新增字段 | `Device`：Bond 名称；其余字段与“配置 Bond”相同 |
| 实机对象 | 临时 `bond9`，成员为非管理网口 `nic1` |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="新增 bond9"
    curl --request POST \
      --user '<username>:<password>' \
      --header 'Content-Type: application/json' \
      --data '{
        "Mode": "active-backup",
        "Interfaces": ["nic1"],
        "Device": "bond9",
        "IPv4Addresses": {
          "Dhcp4": false,
          "Addresses": [],
          "NameServers": [],
          "Optional": true
        },
        "Routes": []
      }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkBondings/Actions/Oem/Firefly/NetworkBondingAdd.Configure'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="使用 Token 新增 bond9"
    curl --request POST \
      --header 'X-Xsrf-Token: <token>' \
      --header 'Content-Type: application/json' \
      --data '{
        "Mode": "active-backup",
        "Interfaces": ["nic1"],
        "Device": "bond9",
        "IPv4Addresses": {
          "Dhcp4": false,
          "Addresses": [],
          "NameServers": [],
          "Optional": true
        },
        "Routes": []
      }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkBondings/Actions/Oem/Firefly/NetworkBondingAdd.Configure'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

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
| `Timestamp` | string | 服务端生成响应时记录的时间戳。 |
| `MessageId` | string | 消息标识，成功时为 `Base.1.11.0.Success`。 |
| `Message` | string | 请求处理结果说明。 |
| `MessageArgs` | array | 消息格式化参数列表；本响应为空数组。 |
| `MessageSeverity` | string | 消息严重级别，成功时为 `OK`。 |
| `Severity` | string | 兼容保留的严重级别字段；本响应为空字符串。 |
| `Oem` | object \| null | OEM 扩展消息；本响应为 `null`。 |
| `RelatedProperties` | array \| null | 与消息相关的属性路径；本响应为 `null`。 |
| `Resolution` | string | 建议的处理方式；成功时为 `None`。 |

<Callout title="必须查询确认新增结果" type="warn">
  实机虽然返回成功消息，但随后查询 Bond 合集时未出现 `bond9`。调用方不能仅依据 `200 OK` 判断 Bond 已创建，必须重新 GET 资源合集确认。
</Callout>

### 3.10 删除 Bond

| 项目 | 内容 |
| --- | --- |
| 方法 | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/NetworkBondings/Actions/Oem/Firefly/DelConfigureActionInfo` |
| 路径 | `/redfish/v1/Systems/{SystemId}/NetworkBondings/Actions/Oem/Firefly/NetworkBondingDel.Configure` |
| 请求字段 | `Device`：要删除的 Bond 名称 |
| 实机对象 | 临时 `bond9` |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="删除 bond9"
    curl --request POST \
      --user '<username>:<password>' \
      --header 'Content-Type: application/json' \
      --data '{ "Device": "bond9" }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkBondings/Actions/Oem/Firefly/NetworkBondingDel.Configure'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="使用 Token 删除 bond9"
    curl --request POST \
      --header 'X-Xsrf-Token: <token>' \
      --header 'Content-Type: application/json' \
      --data '{ "Device": "bond9" }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkBondings/Actions/Oem/Firefly/NetworkBondingDel.Configure'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

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
| `Timestamp` | string | 服务端生成响应时记录的时间戳。 |
| `MessageId` | string | 消息标识，成功时为 `Base.1.11.0.Success`。 |
| `Message` | string | 请求处理结果说明。 |
| `MessageArgs` | array | 消息格式化参数列表；本响应为空数组。 |
| `MessageSeverity` | string | 消息严重级别，成功时为 `OK`。 |
| `Severity` | string | 兼容保留的严重级别字段；本响应为空字符串。 |
| `Oem` | object \| null | OEM 扩展消息；本响应为 `null`。 |
| `RelatedProperties` | array \| null | 与消息相关的属性路径；本响应为 `null`。 |
| `Resolution` | string | 建议的处理方式；成功时为 `None`。 |

删除后重新查询 Bond 合集，实机只包含 `bond0` 和 `bond1`。

### 3.11 配置 VLAN

| 项目 | 内容 |
| --- | --- |
| 方法 | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/NetworkVLANs/{NetworkVLANId}/Actions/Oem/Firefly/ConfigureActionInfo` |
| 路径 | `/redfish/v1/Systems/{SystemId}/NetworkVLANs/{NetworkVLANId}/Actions/Oem/Firefly/NetworkVLAN.Configure` |
| 实机对象 | `vlan400`，底层接口为非管理网口 `nic3` |
| 成功状态码 | `200 OK` |

| 请求字段 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `Id` | number | 是 | VLAN ID，取值应符合 IEEE 802.1Q 范围。 |
| `Link` | string | 是 | VLAN 依附的底层网口，不应使用承载管理地址的网口。 |
| `IPv4Addresses` | object | 否 | IPv4 配置，子字段与网口配置相同。 |
| `Routes` | object[] | 否 | 静态路由列表。 |

<Callout title="配置接口不要传 Device" type="warn">
  配置已有 VLAN 时，后端会根据 URL 中的 `{NetworkVLANId}` 确定设备名。实机请求体显式传入 `Device` 时返回 `400 Base.1.11.0.PropertyUnknown`。
</Callout>

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="配置 vlan400"
    curl --request POST \
      --user '<username>:<password>' \
      --header 'Content-Type: application/json' \
      --data '{
        "IPv4Addresses": {
          "Dhcp4": false,
          "Addresses": [],
          "NameServers": ["114.114.114.114"],
          "Optional": true
        },
        "Link": "nic3",
        "Id": 400,
        "Routes": []
      }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkVLANs/vlan400/Actions/Oem/Firefly/NetworkVLAN.Configure'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="使用 Token 配置 vlan400"
    curl --request POST \
      --header 'X-Xsrf-Token: <token>' \
      --header 'Content-Type: application/json' \
      --data '{
        "IPv4Addresses": {
          "Dhcp4": false,
          "Addresses": [],
          "NameServers": ["114.114.114.114"],
          "Optional": true
        },
        "Link": "nic3",
        "Id": 400,
        "Routes": []
      }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkVLANs/vlan400/Actions/Oem/Firefly/NetworkVLAN.Configure'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

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
| `Timestamp` | string | 服务端生成响应时记录的时间戳。 |
| `MessageId` | string | 消息标识，成功时为 `Base.1.11.0.Success`。 |
| `Message` | string | 请求处理结果说明。 |
| `MessageArgs` | array | 消息格式化参数列表；本响应为空数组。 |
| `MessageSeverity` | string | 消息严重级别，成功时为 `OK`。 |
| `Severity` | string | 兼容保留的严重级别字段；本响应为空字符串。 |
| `Oem` | object \| null | OEM 扩展消息；本响应为 `null`。 |
| `RelatedProperties` | array \| null | 与消息相关的属性路径；本响应为 `null`。 |
| `Resolution` | string | 建议的处理方式；成功时为 `None`。 |

配置后重新 GET `vlan400`，实机仍为 `VlanId=400`、`VlanLink=nic3`。

### 3.12 新增 VLAN

| 项目 | 内容 |
| --- | --- |
| 方法 | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/NetworkVLANs/Actions/Oem/Firefly/AddConfigureActionInfo` |
| 路径 | `/redfish/v1/Systems/{SystemId}/NetworkVLANs/Actions/Oem/Firefly/NetworkVlANAdd.Configure` |
| 请求字段 | `Id`、`Link`、`Device` 必填；`IPv4Addresses`、`Routes` 可选 |
| 实机对象 | 临时 `vlan4090`，底层接口为非管理网口 `nic1` |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="新增 vlan4090"
    curl --request POST \
      --user '<username>:<password>' \
      --header 'Content-Type: application/json' \
      --data '{
        "IPv4Addresses": {
          "Dhcp4": false,
          "Addresses": [],
          "NameServers": [],
          "Optional": true
        },
        "Link": "nic1",
        "Id": 4090,
        "Device": "vlan4090",
        "Routes": []
      }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkVLANs/Actions/Oem/Firefly/NetworkVlANAdd.Configure'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="使用 Token 新增 vlan4090"
    curl --request POST \
      --header 'X-Xsrf-Token: <token>' \
      --header 'Content-Type: application/json' \
      --data '{
        "IPv4Addresses": {
          "Dhcp4": false,
          "Addresses": [],
          "NameServers": [],
          "Optional": true
        },
        "Link": "nic1",
        "Id": 4090,
        "Device": "vlan4090",
        "Routes": []
      }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkVLANs/Actions/Oem/Firefly/NetworkVlANAdd.Configure'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

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
| `Timestamp` | string | 服务端生成响应时记录的时间戳。 |
| `MessageId` | string | 消息标识，成功时为 `Base.1.11.0.Success`。 |
| `Message` | string | 请求处理结果说明。 |
| `MessageArgs` | array | 消息格式化参数列表；本响应为空数组。 |
| `MessageSeverity` | string | 消息严重级别，成功时为 `OK`。 |
| `Severity` | string | 兼容保留的严重级别字段；本响应为空字符串。 |
| `Oem` | object \| null | OEM 扩展消息；本响应为 `null`。 |
| `RelatedProperties` | array \| null | 与消息相关的属性路径；本响应为 `null`。 |
| `Resolution` | string | 建议的处理方式；成功时为 `None`。 |

<Callout title="必须查询确认新增结果" type="warn">
  实机返回成功消息后，VLAN 合集没有出现 `vlan4090`。调用方必须重新查询 VLAN 合集或指定 VLAN，确认配置是否真正生效。
</Callout>

### 3.13 删除 VLAN

| 项目 | 内容 |
| --- | --- |
| 方法 | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/NetworkVLANs/Actions/Oem/Firefly/DelConfigureActionInfo` |
| 路径 | `/redfish/v1/Systems/{SystemId}/NetworkVLANs/Actions/Oem/Firefly/NetworkVLANDel.Configure` |
| 请求字段 | `Device`：要删除的 VLAN 设备名，必填 |
| 实机对象 | 临时 `vlan4090` |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="删除 vlan4090"
    curl --request POST \
      --user '<username>:<password>' \
      --header 'Content-Type: application/json' \
      --data '{ "Device": "vlan4090" }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkVLANs/Actions/Oem/Firefly/NetworkVLANDel.Configure'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="使用 Token 删除 vlan4090"
    curl --request POST \
      --header 'X-Xsrf-Token: <token>' \
      --header 'Content-Type: application/json' \
      --data '{ "Device": "vlan4090" }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkVLANs/Actions/Oem/Firefly/NetworkVLANDel.Configure'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

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
| `Timestamp` | string | 服务端生成响应时记录的时间戳。 |
| `MessageId` | string | 消息标识，成功时为 `Base.1.11.0.Success`。 |
| `Message` | string | 请求处理结果说明。 |
| `MessageArgs` | array | 消息格式化参数列表；本响应为空数组。 |
| `MessageSeverity` | string | 消息严重级别，成功时为 `OK`。 |
| `Severity` | string | 兼容保留的严重级别字段；本响应为空字符串。 |
| `Oem` | object \| null | OEM 扩展消息；本响应为 `null`。 |
| `RelatedProperties` | array \| null | 与消息相关的属性路径；本响应为 `null`。 |
| `Resolution` | string | 建议的处理方式；成功时为 `None`。 |

删除后重新查询 VLAN 合集，实机只包含 `vlan400` 和 `vlan4000`。

### 3.14 查询非管理网口配置 ActionInfo

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/bmc/EthernetInterfaces/nic1/Actions/Oem/Firefly/ConfigureActionInfo` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 实机状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询非管理网口配置 ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/EthernetInterfaces/nic1/Actions/Oem/Firefly/ConfigureActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询非管理网口配置 ActionInfo"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/EthernetInterfaces/nic1/Actions/Oem/Firefly/ConfigureActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Systems/bmc/EthernetInterfaces/nic1/Actions/Oem/Firefly/ConfigureActionInfo",
  "@odata.type": "#ActionInfo.v1_1_2.ActionInfo",
  "Id": "ActionInfo",
  "Name": "Action Info",
  "Parameters": [
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "AllowableValues": [
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "Boolean",
          "Name": "Dhcp4",
          "Required": false
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "AllowableValues": [
            {
              "DisallowedInput": false,
              "AllowablePattern": "",
              "DataType": "String",
              "Name": "Address",
              "Required": true
            },
            {
              "DisallowedInput": false,
              "AllowablePattern": "",
              "DataType": "Number",
              "Name": "PrefixLen",
              "Required": true
            }
          ],
          "DataType": "ObjectArray",
          "Name": "Addresses",
          "Required": false
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "String",
          "Name": "Gateway4",
          "Required": false
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "Number",
          "Name": "Gateway4Priority",
          "Required": false
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "Boolean",
          "Name": "Optional",
          "Required": false
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "StringArray",
          "Name": "NameServers",
          "Required": false
        }
      ],
      "DataType": "Object",
      "Name": "IPv4Addresses",
      "Required": true
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "AllowableValues": [
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "String",
          "Name": "To",
          "Required": true
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "String",
          "Name": "Via",
          "Required": true
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "Number",
          "Name": "Metric",
          "Required": false
        }
      ],
      "DataType": "ObjectArray",
      "Name": "Routes",
      "Required": false
    }
  ]
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | ActionInfo 资源路径。 |
| `@odata.type` | string | ActionInfo 或消息资源的 OData 类型。 |
| `Id` | string | ActionInfo 资源标识。 |
| `Name` | string | ActionInfo 资源名称。 |
| `Parameters` | array | 操作参数定义列表。 |
| `Parameters[].DisallowedInput` | boolean | 该参数是否禁止输入。 |
| `Parameters[].AllowablePattern` | string | 参数允许的字符串模式；空字符串表示未限制模式。 |
| `Parameters[].AllowableValues` | array | 参数或子参数允许的取值与结构定义。 |
| `Parameters[].AllowableValues[].DisallowedInput` | boolean | 该参数是否禁止输入。 |
| `Parameters[].AllowableValues[].AllowablePattern` | string | 参数允许的字符串模式；空字符串表示未限制模式。 |
| `Parameters[].AllowableValues[].DataType` | string | 参数数据类型。 |
| `Parameters[].AllowableValues[].Name` | string | 参数名称。 |
| `Parameters[].AllowableValues[].Required` | boolean | 参数是否必填。 |
| `Parameters[].AllowableValues[].AllowableValues` | array | 参数或子参数允许的取值与结构定义。 |
| `Parameters[].AllowableValues[].AllowableValues[].DisallowedInput` | boolean | 该参数是否禁止输入。 |
| `Parameters[].AllowableValues[].AllowableValues[].AllowablePattern` | string | 参数允许的字符串模式；空字符串表示未限制模式。 |
| `Parameters[].AllowableValues[].AllowableValues[].DataType` | string | 参数数据类型。 |
| `Parameters[].AllowableValues[].AllowableValues[].Name` | string | 参数名称。 |
| `Parameters[].AllowableValues[].AllowableValues[].Required` | boolean | 参数是否必填。 |
| `Parameters[].DataType` | string | 参数数据类型。 |
| `Parameters[].Name` | string | 参数名称。 |
| `Parameters[].Required` | boolean | 参数是否必填。 |

### 3.15 查询 Bond 配置 ActionInfo

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/bmc/NetworkBondings/bond1/Actions/Oem/Firefly/ConfigureActionInfo` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 实机状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询 Bond 配置 ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkBondings/bond1/Actions/Oem/Firefly/ConfigureActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询 Bond 配置 ActionInfo"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkBondings/bond1/Actions/Oem/Firefly/ConfigureActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Systems/bmc/NetworkBondings/bond1/Actions/Oem/Firefly/ConfigureActionInfo",
  "@odata.type": "#ActionInfo.v1_1_2.ActionInfo",
  "Id": "ActionInfo",
  "Name": "Action Info",
  "Parameters": [
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "AllowableValues": [
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "Boolean",
          "Name": "Dhcp4",
          "Required": false
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "AllowableValues": [
            {
              "DisallowedInput": false,
              "AllowablePattern": "",
              "DataType": "String",
              "Name": "Address",
              "Required": true
            },
            {
              "DisallowedInput": false,
              "AllowablePattern": "",
              "DataType": "Number",
              "Name": "PrefixLen",
              "Required": true
            }
          ],
          "DataType": "ObjectArray",
          "Name": "Addresses",
          "Required": false
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "String",
          "Name": "Gateway4",
          "Required": false
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "Number",
          "Name": "Gateway4Priority",
          "Required": false
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "Boolean",
          "Name": "Optional",
          "Required": false
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "StringArray",
          "Name": "NameServers",
          "Required": false
        }
      ],
      "DataType": "Object",
      "Name": "IPv4Addresses",
      "Required": true
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "AllowableValues": [
        "802.3ad",
        "active-backup",
        "balance-alb",
        "balance-rr",
        "balance-tlb",
        "balance-xor",
        "broadcast"
      ],
      "DataType": "String",
      "Name": "Mode",
      "Required": true
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "DataType": "StringArray",
      "Name": "Interfaces",
      "Required": true
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "AllowableValues": [
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "String",
          "Name": "To",
          "Required": true
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "String",
          "Name": "Via",
          "Required": true
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "Number",
          "Name": "Metric",
          "Required": false
        }
      ],
      "DataType": "ObjectArray",
      "Name": "Routes",
      "Required": false
    }
  ]
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | ActionInfo 资源路径。 |
| `@odata.type` | string | ActionInfo 或消息资源的 OData 类型。 |
| `Id` | string | ActionInfo 资源标识。 |
| `Name` | string | ActionInfo 资源名称。 |
| `Parameters` | array | 操作参数定义列表。 |
| `Parameters[].DisallowedInput` | boolean | 该参数是否禁止输入。 |
| `Parameters[].AllowablePattern` | string | 参数允许的字符串模式；空字符串表示未限制模式。 |
| `Parameters[].AllowableValues` | array | 参数或子参数允许的取值与结构定义。 |
| `Parameters[].AllowableValues[].DisallowedInput` | boolean | 该参数是否禁止输入。 |
| `Parameters[].AllowableValues[].AllowablePattern` | string | 参数允许的字符串模式；空字符串表示未限制模式。 |
| `Parameters[].AllowableValues[].DataType` | string | 参数数据类型。 |
| `Parameters[].AllowableValues[].Name` | string | 参数名称。 |
| `Parameters[].AllowableValues[].Required` | boolean | 参数是否必填。 |
| `Parameters[].AllowableValues[].AllowableValues` | array | 参数或子参数允许的取值与结构定义。 |
| `Parameters[].AllowableValues[].AllowableValues[].DisallowedInput` | boolean | 该参数是否禁止输入。 |
| `Parameters[].AllowableValues[].AllowableValues[].AllowablePattern` | string | 参数允许的字符串模式；空字符串表示未限制模式。 |
| `Parameters[].AllowableValues[].AllowableValues[].DataType` | string | 参数数据类型。 |
| `Parameters[].AllowableValues[].AllowableValues[].Name` | string | 参数名称。 |
| `Parameters[].AllowableValues[].AllowableValues[].Required` | boolean | 参数是否必填。 |
| `Parameters[].DataType` | string | 参数数据类型。 |
| `Parameters[].Name` | string | 参数名称。 |
| `Parameters[].Required` | boolean | 参数是否必填。 |

### 3.16 查询新增 Bond ActionInfo

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/bmc/NetworkBondings/Actions/Oem/Firefly/AddConfigureActionInfo` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 实机状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询新增 Bond ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkBondings/Actions/Oem/Firefly/AddConfigureActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询新增 Bond ActionInfo"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkBondings/Actions/Oem/Firefly/AddConfigureActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Systems/bmc/NetworkBondings/Actions/Oem/Firefly/AddConfigureActionInfo",
  "@odata.type": "#ActionInfo.v1_1_2.ActionInfo",
  "Id": "ActionInfo",
  "Name": "Action Info",
  "Parameters": [
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "AllowableValues": [
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "Boolean",
          "Name": "Dhcp4",
          "Required": false
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "AllowableValues": [
            {
              "DisallowedInput": false,
              "AllowablePattern": "",
              "DataType": "String",
              "Name": "Address",
              "Required": true
            },
            {
              "DisallowedInput": false,
              "AllowablePattern": "",
              "DataType": "Number",
              "Name": "PrefixLen",
              "Required": true
            }
          ],
          "DataType": "ObjectArray",
          "Name": "Addresses",
          "Required": false
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "String",
          "Name": "Gateway4",
          "Required": false
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "Number",
          "Name": "Gateway4Priority",
          "Required": false
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "Boolean",
          "Name": "Optional",
          "Required": false
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "StringArray",
          "Name": "NameServers",
          "Required": false
        }
      ],
      "DataType": "Object",
      "Name": "IPv4Addresses",
      "Required": true
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "AllowableValues": [
        "802.3ad",
        "active-backup",
        "balance-alb",
        "balance-rr",
        "balance-tlb",
        "balance-xor",
        "broadcast"
      ],
      "DataType": "String",
      "Name": "Mode",
      "Required": true
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "DataType": "StringArray",
      "Name": "Interfaces",
      "Required": true
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "DataType": "String",
      "Name": "Device",
      "Required": false
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "AllowableValues": [
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "String",
          "Name": "To",
          "Required": true
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "String",
          "Name": "Via",
          "Required": true
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "Number",
          "Name": "Metric",
          "Required": false
        }
      ],
      "DataType": "ObjectArray",
      "Name": "Routes",
      "Required": false
    }
  ]
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | ActionInfo 资源路径。 |
| `@odata.type` | string | ActionInfo 或消息资源的 OData 类型。 |
| `Id` | string | ActionInfo 资源标识。 |
| `Name` | string | ActionInfo 资源名称。 |
| `Parameters` | array | 操作参数定义列表。 |
| `Parameters[].DisallowedInput` | boolean | 该参数是否禁止输入。 |
| `Parameters[].AllowablePattern` | string | 参数允许的字符串模式；空字符串表示未限制模式。 |
| `Parameters[].AllowableValues` | array | 参数或子参数允许的取值与结构定义。 |
| `Parameters[].AllowableValues[].DisallowedInput` | boolean | 该参数是否禁止输入。 |
| `Parameters[].AllowableValues[].AllowablePattern` | string | 参数允许的字符串模式；空字符串表示未限制模式。 |
| `Parameters[].AllowableValues[].DataType` | string | 参数数据类型。 |
| `Parameters[].AllowableValues[].Name` | string | 参数名称。 |
| `Parameters[].AllowableValues[].Required` | boolean | 参数是否必填。 |
| `Parameters[].AllowableValues[].AllowableValues` | array | 参数或子参数允许的取值与结构定义。 |
| `Parameters[].AllowableValues[].AllowableValues[].DisallowedInput` | boolean | 该参数是否禁止输入。 |
| `Parameters[].AllowableValues[].AllowableValues[].AllowablePattern` | string | 参数允许的字符串模式；空字符串表示未限制模式。 |
| `Parameters[].AllowableValues[].AllowableValues[].DataType` | string | 参数数据类型。 |
| `Parameters[].AllowableValues[].AllowableValues[].Name` | string | 参数名称。 |
| `Parameters[].AllowableValues[].AllowableValues[].Required` | boolean | 参数是否必填。 |
| `Parameters[].DataType` | string | 参数数据类型。 |
| `Parameters[].Name` | string | 参数名称。 |
| `Parameters[].Required` | boolean | 参数是否必填。 |

### 3.17 查询删除 Bond ActionInfo

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/bmc/NetworkBondings/Actions/Oem/Firefly/DelConfigureActionInfo` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 实机状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询删除 Bond ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkBondings/Actions/Oem/Firefly/DelConfigureActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询删除 Bond ActionInfo"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkBondings/Actions/Oem/Firefly/DelConfigureActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Systems/bmc/NetworkBondings/Actions/Oem/Firefly/DelConfigureActionInfo",
  "@odata.type": "#ActionInfo.v1_1_2.ActionInfo",
  "Id": "ActionInfo",
  "Name": "Action Info",
  "Parameters": [
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "DataType": "String",
      "Name": "Device",
      "Required": false
    }
  ]
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | ActionInfo 资源路径。 |
| `@odata.type` | string | ActionInfo 或消息资源的 OData 类型。 |
| `Id` | string | ActionInfo 资源标识。 |
| `Name` | string | ActionInfo 资源名称。 |
| `Parameters` | array | 操作参数定义列表。 |
| `Parameters[].DisallowedInput` | boolean | 该参数是否禁止输入。 |
| `Parameters[].AllowablePattern` | string | 参数允许的字符串模式；空字符串表示未限制模式。 |
| `Parameters[].DataType` | string | 参数数据类型。 |
| `Parameters[].Name` | string | 参数名称。 |
| `Parameters[].Required` | boolean | 参数是否必填。 |

### 3.18 查询 VLAN 配置 ActionInfo

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/bmc/NetworkVLANs/vlan400/Actions/Oem/Firefly/ConfigureActionInfo` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 实机状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询 VLAN 配置 ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkVLANs/vlan400/Actions/Oem/Firefly/ConfigureActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询 VLAN 配置 ActionInfo"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkVLANs/vlan400/Actions/Oem/Firefly/ConfigureActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Systems/bmc/NetworkVLANs/vlan400/Actions/Oem/Firefly/ConfigureActionInfo",
  "@odata.type": "#ActionInfo.v1_1_2.ActionInfo",
  "Id": "ActionInfo",
  "Name": "Action Info",
  "Parameters": [
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "AllowableValues": [
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "Boolean",
          "Name": "Dhcp4",
          "Required": false
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "AllowableValues": [
            {
              "DisallowedInput": false,
              "AllowablePattern": "",
              "DataType": "String",
              "Name": "Address",
              "Required": true
            },
            {
              "DisallowedInput": false,
              "AllowablePattern": "",
              "DataType": "Number",
              "Name": "PrefixLen",
              "Required": true
            }
          ],
          "DataType": "ObjectArray",
          "Name": "Addresses",
          "Required": false
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "String",
          "Name": "Gateway4",
          "Required": false
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "Number",
          "Name": "Gateway4Priority",
          "Required": false
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "Boolean",
          "Name": "Optional",
          "Required": false
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "StringArray",
          "Name": "NameServers",
          "Required": false
        }
      ],
      "DataType": "Object",
      "Name": "IPv4Addresses",
      "Required": false
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "DataType": "Number",
      "Name": "Id",
      "Required": true
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "DataType": "String",
      "Name": "Link",
      "Required": true
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "AllowableValues": [
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "String",
          "Name": "To",
          "Required": true
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "String",
          "Name": "Via",
          "Required": true
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "Number",
          "Name": "Metric",
          "Required": false
        }
      ],
      "DataType": "ObjectArray",
      "Name": "Routes",
      "Required": false
    }
  ]
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | ActionInfo 资源路径。 |
| `@odata.type` | string | ActionInfo 或消息资源的 OData 类型。 |
| `Id` | string | ActionInfo 资源标识。 |
| `Name` | string | ActionInfo 资源名称。 |
| `Parameters` | array | 操作参数定义列表。 |
| `Parameters[].DisallowedInput` | boolean | 该参数是否禁止输入。 |
| `Parameters[].AllowablePattern` | string | 参数允许的字符串模式；空字符串表示未限制模式。 |
| `Parameters[].AllowableValues` | array | 参数或子参数允许的取值与结构定义。 |
| `Parameters[].AllowableValues[].DisallowedInput` | boolean | 该参数是否禁止输入。 |
| `Parameters[].AllowableValues[].AllowablePattern` | string | 参数允许的字符串模式；空字符串表示未限制模式。 |
| `Parameters[].AllowableValues[].DataType` | string | 参数数据类型。 |
| `Parameters[].AllowableValues[].Name` | string | 参数名称。 |
| `Parameters[].AllowableValues[].Required` | boolean | 参数是否必填。 |
| `Parameters[].AllowableValues[].AllowableValues` | array | 参数或子参数允许的取值与结构定义。 |
| `Parameters[].AllowableValues[].AllowableValues[].DisallowedInput` | boolean | 该参数是否禁止输入。 |
| `Parameters[].AllowableValues[].AllowableValues[].AllowablePattern` | string | 参数允许的字符串模式；空字符串表示未限制模式。 |
| `Parameters[].AllowableValues[].AllowableValues[].DataType` | string | 参数数据类型。 |
| `Parameters[].AllowableValues[].AllowableValues[].Name` | string | 参数名称。 |
| `Parameters[].AllowableValues[].AllowableValues[].Required` | boolean | 参数是否必填。 |
| `Parameters[].DataType` | string | 参数数据类型。 |
| `Parameters[].Name` | string | 参数名称。 |
| `Parameters[].Required` | boolean | 参数是否必填。 |

### 3.19 查询新增 VLAN ActionInfo

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/bmc/NetworkVLANs/Actions/Oem/Firefly/AddConfigureActionInfo` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 实机状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询新增 VLAN ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkVLANs/Actions/Oem/Firefly/AddConfigureActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询新增 VLAN ActionInfo"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkVLANs/Actions/Oem/Firefly/AddConfigureActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Systems/bmc/NetworkVLANs/Actions/Oem/Firefly/AddConfigureActionInfo",
  "@odata.type": "#ActionInfo.v1_1_2.ActionInfo",
  "Id": "ActionInfo",
  "Name": "Action Info",
  "Parameters": [
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "AllowableValues": [
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "Boolean",
          "Name": "Dhcp4",
          "Required": false
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "AllowableValues": [
            {
              "DisallowedInput": false,
              "AllowablePattern": "",
              "DataType": "String",
              "Name": "Address",
              "Required": true
            },
            {
              "DisallowedInput": false,
              "AllowablePattern": "",
              "DataType": "Number",
              "Name": "PrefixLen",
              "Required": true
            }
          ],
          "DataType": "ObjectArray",
          "Name": "Addresses",
          "Required": false
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "String",
          "Name": "Gateway4",
          "Required": false
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "Number",
          "Name": "Gateway4Priority",
          "Required": false
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "Boolean",
          "Name": "Optional",
          "Required": false
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "StringArray",
          "Name": "NameServers",
          "Required": false
        }
      ],
      "DataType": "Object",
      "Name": "IPv4Addresses",
      "Required": false
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "DataType": "Number",
      "Name": "Id",
      "Required": true
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "DataType": "String",
      "Name": "Link",
      "Required": true
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "DataType": "String",
      "Name": "Device",
      "Required": true
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "AllowableValues": [
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "String",
          "Name": "To",
          "Required": true
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "String",
          "Name": "Via",
          "Required": true
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "Number",
          "Name": "Metric",
          "Required": false
        }
      ],
      "DataType": "ObjectArray",
      "Name": "Routes",
      "Required": false
    }
  ]
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | ActionInfo 资源路径。 |
| `@odata.type` | string | ActionInfo 或消息资源的 OData 类型。 |
| `Id` | string | ActionInfo 资源标识。 |
| `Name` | string | ActionInfo 资源名称。 |
| `Parameters` | array | 操作参数定义列表。 |
| `Parameters[].DisallowedInput` | boolean | 该参数是否禁止输入。 |
| `Parameters[].AllowablePattern` | string | 参数允许的字符串模式；空字符串表示未限制模式。 |
| `Parameters[].AllowableValues` | array | 参数或子参数允许的取值与结构定义。 |
| `Parameters[].AllowableValues[].DisallowedInput` | boolean | 该参数是否禁止输入。 |
| `Parameters[].AllowableValues[].AllowablePattern` | string | 参数允许的字符串模式；空字符串表示未限制模式。 |
| `Parameters[].AllowableValues[].DataType` | string | 参数数据类型。 |
| `Parameters[].AllowableValues[].Name` | string | 参数名称。 |
| `Parameters[].AllowableValues[].Required` | boolean | 参数是否必填。 |
| `Parameters[].AllowableValues[].AllowableValues` | array | 参数或子参数允许的取值与结构定义。 |
| `Parameters[].AllowableValues[].AllowableValues[].DisallowedInput` | boolean | 该参数是否禁止输入。 |
| `Parameters[].AllowableValues[].AllowableValues[].AllowablePattern` | string | 参数允许的字符串模式；空字符串表示未限制模式。 |
| `Parameters[].AllowableValues[].AllowableValues[].DataType` | string | 参数数据类型。 |
| `Parameters[].AllowableValues[].AllowableValues[].Name` | string | 参数名称。 |
| `Parameters[].AllowableValues[].AllowableValues[].Required` | boolean | 参数是否必填。 |
| `Parameters[].DataType` | string | 参数数据类型。 |
| `Parameters[].Name` | string | 参数名称。 |
| `Parameters[].Required` | boolean | 参数是否必填。 |

### 3.20 查询删除 VLAN ActionInfo

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/bmc/NetworkVLANs/Actions/Oem/Firefly/DelConfigureActionInfo` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 实机状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询删除 VLAN ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkVLANs/Actions/Oem/Firefly/DelConfigureActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询删除 VLAN ActionInfo"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkVLANs/Actions/Oem/Firefly/DelConfigureActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Systems/bmc/NetworkVLANs/Actions/Oem/Firefly/DelConfigureActionInfo",
  "@odata.type": "#ActionInfo.v1_1_2.ActionInfo",
  "Id": "ActionInfo",
  "Name": "Action Info",
  "Parameters": [
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "DataType": "String",
      "Name": "Device",
      "Required": true
    }
  ]
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | ActionInfo 资源路径。 |
| `@odata.type` | string | ActionInfo 或消息资源的 OData 类型。 |
| `Id` | string | ActionInfo 资源标识。 |
| `Name` | string | ActionInfo 资源名称。 |
| `Parameters` | array | 操作参数定义列表。 |
| `Parameters[].DisallowedInput` | boolean | 该参数是否禁止输入。 |
| `Parameters[].AllowablePattern` | string | 参数允许的字符串模式；空字符串表示未限制模式。 |
| `Parameters[].DataType` | string | 参数数据类型。 |
| `Parameters[].Name` | string | 参数名称。 |
| `Parameters[].Required` | boolean | 参数是否必填。 |

## 4 时间管理资源

### 4.1 查询系统时间

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/{SystemId}/Oem/Firefly/DateTime` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询系统时间"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Oem/Firefly/DateTime'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询系统时间"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Oem/Firefly/DateTime'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Systems/bmc/Oem/Firefly/DateTime",
  "@odata.type": "#FireflyDateTime.v1_9_0.FireflyDateTime",
  "Id": "DateTime",
  "Name": "DateTime",
  "Actions": {
    "#DateTime.Configure": {
      "target": "/redfish/v1/Systems/bmc/Oem/Firefly/DateTime/Actions/ComputerSystem.SetDateTime",
      "@Redfish.ActionInfo": "/redfish/v1/Systems/bmc/Oem/Firefly/DateTime/Actions/SetDateTimeActionInfo"
    }
  },
  "DateTime": "2026-08-04 17:05:28"
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | 系统时间资源的访问路径。 |
| `@odata.type` | string | 系统时间资源的 OData 类型。 |
| `Id` | string | 系统时间资源标识。 |
| `Name` | string | 系统时间资源名称。 |
| `Actions` | object | 系统时间可执行操作。 |
| `Actions.#DateTime.Configure` | object | 设置系统时间的操作定义。 |
| `Actions.#DateTime.Configure.target` | string | 设置系统时间的 POST 路径。 |
| `Actions.#DateTime.Configure.@Redfish.ActionInfo` | string | 设置时间的 ActionInfo 路径。 |
| `DateTime` | string | 当前系统日期和时间。 |

### 4.2 查询时区

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/{SystemId}/Oem/Firefly/TimeZone` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 请求体 | 无 |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="查询时区"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Oem/Firefly/TimeZone'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询时区"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Oem/Firefly/TimeZone'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

以下是测试设备返回的真实 `200 OK` 响应。

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Systems/bmc/Oem/Firefly/TimeZone",
  "@odata.type": "#FireflyDateTime.v1_9_0.FireflyTimeZone",
  "Id": "TimeZone",
  "Name": "TimeZone",
  "Actions": {
    "#TimeZone.Configure": {
      "target": "/redfish/v1/Systems/bmc/Oem/Firefly/TimeZone/Actions/ComputerSystem.SetTimeZone",
      "@Redfish.ActionInfo": "/redfish/v1/Systems/bmc/Oem/Firefly/TimeZone/Actions/SetTimeZoneActionInfo"
    }
  },
  "TimeZone": "Etc/GMT-8"
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | 时区资源路径。 |
| `@odata.type` | string | 时区资源的 OData 类型。 |
| `Id` | string | 时区资源标识。 |
| `Name` | string | 时区资源名称。 |
| `Actions` | object | 时区资源支持的操作。 |
| `Actions.#TimeZone.Configure` | object | 设置时区操作定义。 |
| `Actions.#TimeZone.Configure.target` | string | 设置时区的 POST 路径。 |
| `Actions.#TimeZone.Configure.@Redfish.ActionInfo` | string | 设置时区的 ActionInfo 路径。 |
| `TimeZone` | string | 当前 IANA 时区名称。 |

### 4.3 查询时间服务完整配置

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/{SystemId}/Oem/Firefly/TimeServiceSetting` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 请求体 | 无 |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="查询时间服务完整配置"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Oem/Firefly/TimeServiceSetting'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询时间服务完整配置"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Oem/Firefly/TimeServiceSetting'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

以下是测试设备返回的真实 `200 OK` 响应。

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Systems/bmc/Oem/Firefly/TimeServiceSetting",
  "@odata.type": "#FireflyTimeService.v1_9_0.FireflyTimeServiceSetting",
  "Id": "TimeServiceSetting",
  "Name": "TimeServiceSetting",
  "Actions": {},
  "manual": false,
  "ntp_config": {
    "preferred": "ntp.aliyun.com",
    "alternative": "",
    "rtc": true,
    "make_step": {
      "threshold": "1",
      "limit": "3"
    },
    "server_config": {
      "port": "123",
      "local_stratum": "10"
    }
  },
  "time": "",
  "time_zone": "GMT+08:00",
  "chrony_config": {
    "servers": [
      {
        "addr": "ntp.aliyun.com",
        "iburst": true,
        "minpoll": "6",
        "maxpoll": "7",
        "port": "123",
        "nts": false,
        "burst": false,
        "trust": false
      }
    ],
    "log": {
      "measurements": true,
      "statistics": true,
      "tracking": true,
      "rtc": false,
      "refclocks": false,
      "tempcomp": false,
      "rawmeasurements": false,
      "selection": false
    },
    "makestep": {
      "threshold": "1",
      "limit": "3"
    },
    "allow": ["0.0.0.0/0"],
    "deny": [],
    "port": "123",
    "binddevice": null,
    "local": {
      "distance": "",
      "stratum": "10",
      "orphan": false
    },
    "logdir": "var/lib/chrony/",
    "driftfile": "/var/lib/chrony/chrony.drift",
    "minsources": "",
    "rtcsync": true,
    "maxupdateskew": "",
    "maxdistance": "",
    "ntsdumpdir": "",
    "ntsservercert": "",
    "ntsserverkey": "",
    "ntstrustedcerts": ""
  }
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | 时间服务完整配置资源路径。 |
| `@odata.type` | string | 时间服务配置的 OData 类型。 |
| `Id` | string | 资源标识。 |
| `Name` | string | 资源名称。 |
| `Actions` | object | 当前资源直接提供的操作定义。 |
| `manual` | boolean | 是否使用手动设置时间。 |
| `ntp_config` | object | 时间服务的简化同步配置。 |
| `ntp_config.preferred` | string | 首选时间服务器。 |
| `ntp_config.alternative` | string | 备用时间服务器。 |
| `ntp_config.rtc` | boolean | 是否同步硬件时钟。 |
| `ntp_config.make_step` | object | 时间跳变策略。 |
| `ntp_config.make_step.threshold` | string | 允许直接校正时间的偏差阈值。 |
| `ntp_config.make_step.limit` | string | 启动后允许直接校正的次数。 |
| `ntp_config.server_config` | object | 本地时间服务器配置。 |
| `ntp_config.server_config.port` | string | 时间服务端口。 |
| `ntp_config.server_config.local_stratum` | string | 本地时间源层级。 |
| `time` | string | 手动时间值；非手动模式下可以为空。 |
| `time_zone` | string | 时间服务使用的 GMT 时区，例如 `GMT+08:00`。独立时区资源可能返回对应的 IANA 名称。 |
| `chrony_config` | object | Chrony 完整配置。 |
| `chrony_config.servers` | array | 上游时间服务器列表。 |
| `chrony_config.servers[].addr` | string | 时间服务器地址。 |
| `chrony_config.servers[].iburst` | boolean | 是否在启动时快速发送同步请求。 |
| `chrony_config.servers[].minpoll` | string | 最小轮询间隔指数。 |
| `chrony_config.servers[].maxpoll` | string | 最大轮询间隔指数。 |
| `chrony_config.servers[].port` | string | 时间服务器端口。 |
| `chrony_config.servers[].nts` | boolean | 是否启用 Network Time Security。 |
| `chrony_config.servers[].burst` | boolean | 是否启用 burst 模式。 |
| `chrony_config.servers[].trust` | boolean | 是否信任该时间源。 |
| `chrony_config.log` | object | Chrony 日志类别配置。 |
| `chrony_config.log.measurements` | boolean | 是否记录测量日志。 |
| `chrony_config.log.statistics` | boolean | 是否记录统计日志。 |
| `chrony_config.log.tracking` | boolean | 是否记录跟踪日志。 |
| `chrony_config.log.rtc` | boolean | 是否记录 RTC 日志。 |
| `chrony_config.log.refclocks` | boolean | 是否记录参考时钟日志。 |
| `chrony_config.log.tempcomp` | boolean | 是否记录温度补偿日志。 |
| `chrony_config.log.rawmeasurements` | boolean | 是否记录原始测量日志。 |
| `chrony_config.log.selection` | boolean | 是否记录时间源选择日志。 |
| `chrony_config.makestep` | object | Chrony 时间跳变策略。 |
| `chrony_config.makestep.threshold` | string | 时间跳变阈值。 |
| `chrony_config.makestep.limit` | string | 时间跳变次数限制。 |
| `chrony_config.allow` | array | 允许访问本机时间服务的网段。 |
| `chrony_config.deny` | array | 拒绝访问本机时间服务的网段。 |
| `chrony_config.port` | string | 本机时间服务端口。 |
| `chrony_config.binddevice` | string[] \| null | Chrony 绑定的网口。 |
| `chrony_config.local` | object | 本地时间源配置。 |
| `chrony_config.local.distance` | string | 本地时间源距离。 |
| `chrony_config.local.stratum` | string | 本地时间源层级。 |
| `chrony_config.local.orphan` | boolean | 是否启用孤立模式。 |
| `chrony_config.logdir` | string | Chrony 日志目录。 |
| `chrony_config.driftfile` | string | 时钟漂移数据文件。 |
| `chrony_config.minsources` | string | 同步所需的最少时间源数量。 |
| `chrony_config.rtcsync` | boolean | 是否将系统时间同步到 RTC。 |
| `chrony_config.maxupdateskew` | string | 允许更新时间的最大偏差估计。 |
| `chrony_config.maxdistance` | string | 时间源允许的最大根距离。 |
| `chrony_config.ntsdumpdir` | string | NTS 数据保存目录。 |
| `chrony_config.ntsservercert` | string | NTS 服务端证书路径。 |
| `chrony_config.ntsserverkey` | string | NTS 服务端私钥路径。 |
| `chrony_config.ntstrustedcerts` | string | NTS 信任证书配置。 |

### 4.4 设置系统时间

| 项目 | 内容 |
| --- | --- |
| 方法 | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/Oem/Firefly/DateTime/Actions/SetDateTimeActionInfo` |
| 路径 | `/redfish/v1/Systems/{SystemId}/Oem/Firefly/DateTime/Actions/ComputerSystem.SetDateTime` |
| 请求字段 | `DateTime`，string，必填 |
| 日期格式 | `YYYY-MM-DD HH:mm:ss` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="设置系统时间"
    curl --request POST \
      --user '<username>:<password>' \
      --header 'Content-Type: application/json' \
      --data '{ "DateTime": "2026-08-04 18:00:00" }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Oem/Firefly/DateTime/Actions/ComputerSystem.SetDateTime'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="使用 Token 设置系统时间"
    curl --request POST \
      --header 'X-Xsrf-Token: <token>' \
      --header 'Content-Type: application/json' \
      --data '{ "DateTime": "2026-08-04 18:00:00" }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Oem/Firefly/DateTime/Actions/ComputerSystem.SetDateTime'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

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
| `Timestamp` | string | 服务端生成响应时记录的时间戳。 |
| `MessageId` | string | 消息标识，成功时为 `Base.1.11.0.Success`。 |
| `Message` | string | 请求处理结果说明。 |
| `MessageArgs` | array | 消息格式化参数列表；本响应为空数组。 |
| `MessageSeverity` | string | 消息严重级别，成功时为 `OK`。 |
| `Severity` | string | 兼容保留的严重级别字段；本响应为空字符串。 |
| `Oem` | object \| null | OEM 扩展消息；本响应为 `null`。 |
| `RelatedProperties` | array \| null | 与消息相关的属性路径；本响应为 `null`。 |
| `Resolution` | string | 建议的处理方式；成功时为 `None`。 |

<Callout title="操作影响" type="warn">
  设置系统时间会影响日志顺序、Token、证书、定时任务和集群同步。POST 返回成功消息后，应立即查询系统时间，确认设备时间已更新为预期值。
</Callout>

### 4.5 设置时区

| 项目 | 内容 |
| --- | --- |
| 方法 | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/Oem/Firefly/TimeZone/Actions/SetTimeZoneActionInfo` |
| 路径 | `/redfish/v1/Systems/{SystemId}/Oem/Firefly/TimeZone/Actions/ComputerSystem.SetTimeZone` |
| 请求字段 | `TimeZone`，string，必填 |
| 示例值 | `Etc/GMT-8` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="设置时区"
    curl --request POST \
      --user '<username>:<password>' \
      --header 'Content-Type: application/json' \
      --data '{ "TimeZone": "Etc/GMT-8" }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Oem/Firefly/TimeZone/Actions/ComputerSystem.SetTimeZone'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="使用 Token 设置时区"
    curl --request POST \
      --header 'X-Xsrf-Token: <token>' \
      --header 'Content-Type: application/json' \
      --data '{ "TimeZone": "Etc/GMT-8" }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Oem/Firefly/TimeZone/Actions/ComputerSystem.SetTimeZone'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

以下是将当前时区 `Etc/GMT-8` 原值写回后返回的真实响应：

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
| `Timestamp` | string | 服务端生成响应时记录的时间戳。 |
| `MessageId` | string | 消息标识，成功时为 `Base.1.11.0.Success`。 |
| `Message` | string | 请求处理结果说明。 |
| `MessageArgs` | array | 消息格式化参数列表；本响应为空数组。 |
| `MessageSeverity` | string | 消息严重级别，成功时为 `OK`。 |
| `Severity` | string | 兼容保留的严重级别字段；本响应为空字符串。 |
| `Oem` | object \| null | OEM 扩展消息；本响应为 `null`。 |
| `RelatedProperties` | array \| null | 与消息相关的属性路径；本响应为 `null`。 |
| `Resolution` | string | 建议的处理方式；成功时为 `None`。 |

### 4.6 配置时间服务

| 项目 | 内容 |
| --- | --- |
| 方法 | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/Oem/Firefly/TimeService/Actions/ConfigureTimeServiceActionInfo` |
| 路径 | `/redfish/v1/Systems/{SystemId}/Oem/Firefly/TimeService/Actions/ConfigueTimeService` |
| 必填字段 | `manual`、`time_zone` |

| 请求字段 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `manual` | boolean | 是 | 是否使用手动时间。 |
| `time` | string | 否 | 手动时间；`manual=true` 时使用。 |
| `time_zone` | string | 是 | GMT 时区，例如 `GMT+08:00`。 |
| `ntp_config` | object | 否 | 时间同步配置。 |
| `ntp_config.preferred` | string | 否 | 首选时间服务器。 |
| `ntp_config.alternative` | string | 否 | 备用时间服务器。 |
| `ntp_config.rtc` | boolean | 否 | 是否同步硬件时钟。 |
| `ntp_config.make_step` | object | 否 | 时间跳变策略。 |
| `ntp_config.make_step.threshold` | string | 否 | 时间跳变阈值。 |
| `ntp_config.make_step.limit` | string | 否 | 时间跳变次数限制。 |
| `ntp_config.server_config` | object | 否 | 本地时间服务配置。 |
| `ntp_config.server_config.port` | string | 否 | 时间服务端口。 |
| `ntp_config.server_config.local_stratum` | string | 否 | 本地时间源层级。 |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="配置时间服务"
    curl --request POST \
      --user '<username>:<password>' \
      --header 'Content-Type: application/json' \
      --data '{
        "manual": false,
        "ntp_config": {
          "preferred": "ntp.aliyun.com",
          "alternative": "",
          "rtc": true,
          "make_step": {
            "threshold": "1",
            "limit": "3"
          },
          "server_config": {
            "port": "123",
            "local_stratum": "10"
          }
        },
        "time": "",
        "time_zone": "GMT+08:00"
      }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Oem/Firefly/TimeService/Actions/ConfigueTimeService'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="使用 Token 配置时间服务"
    curl --request POST \
      --header 'X-Xsrf-Token: <token>' \
      --header 'Content-Type: application/json' \
      --data '{
        "manual": false,
        "ntp_config": {
          "preferred": "ntp.aliyun.com",
          "alternative": "",
          "rtc": true,
          "make_step": {
            "threshold": "1",
            "limit": "3"
          },
          "server_config": {
            "port": "123",
            "local_stratum": "10"
          }
        },
        "time": "",
        "time_zone": "GMT+08:00"
      }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Oem/Firefly/TimeService/Actions/ConfigueTimeService'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

以下是将当前时间服务配置原值写回后，设备返回的真实响应。

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
| `Timestamp` | string | 服务端生成响应时记录的时间戳。 |
| `MessageId` | string | 消息标识，成功时为 `Base.1.11.0.Success`。 |
| `Message` | string | 请求处理结果说明。 |
| `MessageArgs` | array | 消息格式化参数列表；本响应为空数组。 |
| `MessageSeverity` | string | 消息严重级别，成功时为 `OK`。 |
| `Severity` | string | 兼容保留的严重级别字段；本响应为空字符串。 |
| `Oem` | object \| null | OEM 扩展消息；本响应为 `null`。 |
| `RelatedProperties` | array \| null | 与消息相关的属性路径；本响应为 `null`。 |
| `Resolution` | string | 建议的处理方式；成功时为 `None`。 |

<Callout title="实机验证结果" type="info">
  POST 返回 `200 OK` 后，重新查询确认首选时间源、RTC、步进参数、端口和本地层级均与操作前一致；时间服务配置中的 `time_zone` 为 `GMT+08:00`，时区资源对应返回 `Etc/GMT-8`。系统时间持续正常递增。成功响应后仍应执行 GET 复核，不能只检查 `MessageId`。
</Callout>

### 4.7 查询设置系统时间 ActionInfo

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/bmc/Oem/Firefly/DateTime/Actions/SetDateTimeActionInfo` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 实机状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询设置系统时间 ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Oem/Firefly/DateTime/Actions/SetDateTimeActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询设置系统时间 ActionInfo"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Oem/Firefly/DateTime/Actions/SetDateTimeActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Systems/bmc/Oem/Firefly/DateTime/Actions/SetDateTimeActionInfo",
  "@odata.type": "#ActionInfo.v1_1_2.ActionInfo",
  "Id": "ActionInfo",
  "Name": "Action Info",
  "Parameters": [
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "DataType": "String",
      "Name": "DateTime",
      "Required": true
    }
  ]
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | ActionInfo 资源路径。 |
| `@odata.type` | string | ActionInfo 或消息资源的 OData 类型。 |
| `Id` | string | ActionInfo 资源标识。 |
| `Name` | string | ActionInfo 资源名称。 |
| `Parameters` | array | 操作参数定义列表。 |
| `Parameters[].DisallowedInput` | boolean | 该参数是否禁止输入。 |
| `Parameters[].AllowablePattern` | string | 参数允许的字符串模式；空字符串表示未限制模式。 |
| `Parameters[].DataType` | string | 参数数据类型。 |
| `Parameters[].Name` | string | 参数名称。 |
| `Parameters[].Required` | boolean | 参数是否必填。 |

### 4.8 查询设置时区 ActionInfo

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/bmc/Oem/Firefly/TimeZone/Actions/SetTimeZoneActionInfo` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 实机状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询设置时区 ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Oem/Firefly/TimeZone/Actions/SetTimeZoneActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询设置时区 ActionInfo"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Oem/Firefly/TimeZone/Actions/SetTimeZoneActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Systems/bmc/Oem/Firefly/TimeZone/Actions/SetTimeZoneActionInfo",
  "@odata.type": "#ActionInfo.v1_1_2.ActionInfo",
  "Id": "ActionInfo",
  "Name": "Action Info",
  "Parameters": [
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "DataType": "String",
      "Name": "TimeZone",
      "Required": true
    }
  ]
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | ActionInfo 资源路径。 |
| `@odata.type` | string | ActionInfo 或消息资源的 OData 类型。 |
| `Id` | string | ActionInfo 资源标识。 |
| `Name` | string | ActionInfo 资源名称。 |
| `Parameters` | array | 操作参数定义列表。 |
| `Parameters[].DisallowedInput` | boolean | 该参数是否禁止输入。 |
| `Parameters[].AllowablePattern` | string | 参数允许的字符串模式；空字符串表示未限制模式。 |
| `Parameters[].DataType` | string | 参数数据类型。 |
| `Parameters[].Name` | string | 参数名称。 |
| `Parameters[].Required` | boolean | 参数是否必填。 |

### 4.9 查询配置时间服务 ActionInfo

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/bmc/Oem/Firefly/TimeService/Actions/ConfigureTimeServiceActionInfo` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 实机状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询配置时间服务 ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Oem/Firefly/TimeService/Actions/ConfigureTimeServiceActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询配置时间服务 ActionInfo"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Oem/Firefly/TimeService/Actions/ConfigureTimeServiceActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Systems/bmc/Oem/Firefly/TimeService/Actions/ConfigureTimeServiceActionInfo",
  "@odata.type": "#ActionInfo.v1_1_2.ActionInfo",
  "Id": "ActionInfo",
  "Name": "Action Info",
  "Parameters": [
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "DataType": "Boolean",
      "Name": "manual",
      "Required": true
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "AllowableValues": [
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "String",
          "Name": "preferred",
          "Required": false
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "String",
          "Name": "alternative",
          "Required": false
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "Boolean",
          "Name": "rtc",
          "Required": false
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "AllowableValues": [
            {
              "DisallowedInput": false,
              "AllowablePattern": "",
              "DataType": "String",
              "Name": "threshold",
              "Required": false
            },
            {
              "DisallowedInput": false,
              "AllowablePattern": "",
              "DataType": "String",
              "Name": "limit",
              "Required": false
            }
          ],
          "DataType": "Object",
          "Name": "make_step",
          "Required": false
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "AllowableValues": [
            {
              "DisallowedInput": false,
              "AllowablePattern": "",
              "DataType": "String",
              "Name": "port",
              "Required": false
            },
            {
              "DisallowedInput": false,
              "AllowablePattern": "",
              "DataType": "String",
              "Name": "local_stratum",
              "Required": false
            }
          ],
          "DataType": "Object",
          "Name": "server_config",
          "Required": false
        }
      ],
      "DataType": "Object",
      "Name": "ntp_config",
      "Required": false
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "DataType": "String",
      "Name": "time",
      "Required": false
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "AllowableValues": [
        "GMT+03:00",
        "GMT+06:00",
        "GMT+11:00",
        "GMT+13:00",
        "GMT+14:00",
        "GMT-02:00",
        "GMT-03:00",
        "GMT-12:00",
        "GMT+02:00",
        "GMT+08:00",
        "GMT-01:00",
        "GMT-04:00",
        "GMT-05:00",
        "GMT-06:00",
        "GMT-07:00",
        "GMT-10:00",
        "GMT+01:00",
        "GMT+04:00",
        "GMT+05:00",
        "GMT+07:00",
        "GMT+09:00",
        "GMT-11:00",
        "GMT+00:00",
        "GMT+10:00",
        "GMT+12:00",
        "GMT-08:00",
        "GMT-09:00"
      ],
      "DataType": "String",
      "Name": "time_zone",
      "Required": true
    }
  ]
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | ActionInfo 资源路径。 |
| `@odata.type` | string | ActionInfo 或消息资源的 OData 类型。 |
| `Id` | string | ActionInfo 资源标识。 |
| `Name` | string | ActionInfo 资源名称。 |
| `Parameters` | array | 操作参数定义列表。 |
| `Parameters[].DisallowedInput` | boolean | 该参数是否禁止输入。 |
| `Parameters[].AllowablePattern` | string | 参数允许的字符串模式；空字符串表示未限制模式。 |
| `Parameters[].DataType` | string | 参数数据类型。 |
| `Parameters[].Name` | string | 参数名称。 |
| `Parameters[].Required` | boolean | 参数是否必填。 |
| `Parameters[].AllowableValues` | array | 参数或子参数允许的取值与结构定义。 |
| `Parameters[].AllowableValues[].DisallowedInput` | boolean | 该参数是否禁止输入。 |
| `Parameters[].AllowableValues[].AllowablePattern` | string | 参数允许的字符串模式；空字符串表示未限制模式。 |
| `Parameters[].AllowableValues[].DataType` | string | 参数数据类型。 |
| `Parameters[].AllowableValues[].Name` | string | 参数名称。 |
| `Parameters[].AllowableValues[].Required` | boolean | 参数是否必填。 |
| `Parameters[].AllowableValues[].AllowableValues` | array | 参数或子参数允许的取值与结构定义。 |
| `Parameters[].AllowableValues[].AllowableValues[].DisallowedInput` | boolean | 该参数是否禁止输入。 |
| `Parameters[].AllowableValues[].AllowableValues[].AllowablePattern` | string | 参数允许的字符串模式；空字符串表示未限制模式。 |
| `Parameters[].AllowableValues[].AllowableValues[].DataType` | string | 参数数据类型。 |
| `Parameters[].AllowableValues[].AllowableValues[].Name` | string | 参数名称。 |
| `Parameters[].AllowableValues[].AllowableValues[].Required` | boolean | 参数是否必填。 |

## 5 硬盘管理资源

### 5.1 查询块设备存储控制器

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/{SystemId}/Storages/BlockDevice` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询块设备存储控制器"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询块设备存储控制器"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK · 真实响应节选"
{
  "@odata.id": "/redfish/v1/Systems/bmc/Storages/BlockDevice",
  "@odata.type": "#Storage.v1_4_0.Storage",
  "Oem": {},
  "StorageControllers@odata.count": 1,
  "StorageControllers": [
    {
      "@odata.id": "/redfish/v1/Systems/bmc/Storages/BlockDevice#/StorageControllers/0",
      "@odata.type": "#StorageController.v1_16_0.StorageController",
      "Id": "lsblk",
      "Description": "System block Tool",
      "Manufacturer": "Linux"
    }
  ],
  "Volumes": {
    "@odata.id": "/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes"
  }
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | 块设备存储资源的访问路径。 |
| `@odata.type` | string | 存储资源的 OData 类型。 |
| `Oem` | object | 存储资源 OEM 扩展信息。 |
| `StorageControllers@odata.count` | number | 存储控制器数量。 |
| `StorageControllers` | array | 存储控制器列表。 |
| `StorageControllers[].@odata.id` | string | 存储控制器的资源路径。 |
| `StorageControllers[].@odata.type` | string | 存储控制器的 OData 类型。 |
| `StorageControllers[].Id` | string | 存储控制器标识。 |
| `StorageControllers[].Description` | string | 存储控制器描述。 |
| `StorageControllers[].Manufacturer` | string | 存储控制器实现方。 |
| `Volumes` | object | 块设备卷资源入口。 |
| `Volumes.@odata.id` | string | 块设备卷资源合集路径。 |

### 5.2 查询块设备卷合集

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/{SystemId}/Storages/BlockDevice/Volumes` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="查询块设备卷合集"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询块设备卷合集"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例


```json title="200 OK · 真实响应节选"
{
  "@odata.id": "/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes",
  "@odata.type": "#VolumeCollection.VolumeCollection",
  "Name": "lsblk Volume Collection",
  "Members@odata.count": 1,
  "Members": [
    {
      "@odata.id": "/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0"
    }
  ],
  "Oem": {
    "Firefly": {
      "Tree": {
        "blockdevices": [
          {
            "name": "mmcblk0",
            "path": "/dev/mmcblk0",
            "size": 62545461248,
            "type": "disk",
            "pttype": "gpt",
            "StorageType": "MMC",
            "OemEnableCreatePartition": false,
            "OemEnableDeletePartition": false,
            "children": [
              {
                "name": "mmcblk0p3",
                "fstype": "ext4",
                "mountpoint": "/boot",
                "size": 268435456
              },
              {
                "name": "mmcblk0p6",
                "fstype": "ext4",
                "mountpoint": "/",
                "size": 7516192768
              },
              {
                "name": "mmcblk0p7",
                "fstype": "ext4",
                "mountpoint": "/userdata",
                "size": 53632548352
              }
            ]
          }
        ]
      }
    }
  }
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | 块设备卷合集路径。 |
| `@odata.type` | string | 卷合集的 OData 类型。 |
| `Name` | string | 卷合集名称。 |
| `Members@odata.count` | number | 根块设备数量。 |
| `Members` | array | 根块设备成员列表。 |
| `Members[].@odata.id` | string | 指定块设备的访问路径。 |
| `Oem` | object | OEM 扩展信息。 |
| `Oem.Firefly` | object | Firefly 存储扩展信息。 |
| `Oem.Firefly.Tree` | object | `lsblk` 设备树。 |
| `Oem.Firefly.Tree.blockdevices` | array | 根块设备列表。 |
| `Oem.Firefly.Tree.blockdevices[].name` | string | Linux 块设备名称。 |
| `Oem.Firefly.Tree.blockdevices[].path` | string | 设备节点路径。 |
| `Oem.Firefly.Tree.blockdevices[].size` | number | 设备容量，单位为字节。 |
| `Oem.Firefly.Tree.blockdevices[].type` | string | 设备类型。 |
| `Oem.Firefly.Tree.blockdevices[].pttype` | string | 分区表类型。 |
| `Oem.Firefly.Tree.blockdevices[].StorageType` | string | 存储介质类型。 |
| `Oem.Firefly.Tree.blockdevices[].OemEnableCreatePartition` | boolean | 是否允许新建分区。 |
| `Oem.Firefly.Tree.blockdevices[].OemEnableDeletePartition` | boolean | 是否允许删除分区。 |
| `Oem.Firefly.Tree.blockdevices[].children` | array | 子分区列表。 |
| `Oem.Firefly.Tree.blockdevices[].children[].name` | string | 分区名称。 |
| `Oem.Firefly.Tree.blockdevices[].children[].fstype` | string | 文件系统类型。 |
| `Oem.Firefly.Tree.blockdevices[].children[].mountpoint` | string | 当前挂载点。 |
| `Oem.Firefly.Tree.blockdevices[].children[].size` | number | 分区容量，单位为字节。 |

### 5.3 订阅块设备状态

| 项目 | 内容 |
| --- | --- |
| 协议 | WebSocket |
| 握手方法 | `GET` + `Upgrade: websocket` |
| 路径 | `/redfish/v1/Systems/{SystemId}/Storages/BlockDevice/Volumes/ws` |
| 认证 | 登录权限 |

```text title="WebSocket 地址"
<ws-protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/ws
```

连接建立后，服务端首先发送 `ActionType=Action_GetInfo` 的完整块设备树；随后订阅存储 Redis 主题，并持续推送设备或任务状态变化。

<Callout title="代理兼容性" type="warn">
  当前测试入口经过 Polipo HTTP 代理。普通 GET 返回 `502 Server dropped connection`，手工 Upgrade 请求返回 `503 Couldn't parse client headers`，说明该代理不能转发 WebSocket 握手。服务端路由和 Upgrade 实现存在，但需要绕过该 HTTP 代理或使用支持 WebSocket 的反向代理后再验证连接。
</Callout>

### 5.4 查询指定块设备

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/{SystemId}/Storages/BlockDevice/Volumes/{BlockDeviceId}` |
| 路径参数 | `BlockDeviceId`：从块设备卷合集获取，以下示例使用 `mmcblk0` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="查询 mmcblk0"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询 mmcblk0"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK · 真实响应节选"
{
  "@odata.id": "/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0",
  "@odata.type": "#Volume.v1_8_0.Volume",
  "CapacityBytes": "62545461248",
  "RAIDType": "None",
  "VolumeType": "RawDevice",
  "Oem": {
    "Firefly": {
      "Detail": {
        "name": "mmcblk0",
        "path": "/dev/mmcblk0",
        "serial": "0x9d722704",
        "size": 62545461248,
        "type": "disk",
        "pttype": "gpt",
        "StorageType": "MMC",
        "OemEnableCreatePartition": false,
        "OemEnableDeletePartition": false,
        "OemEnableMakeFS": false,
        "OemEnableMount": false,
        "OemEnableUnmount": false,
        "OemBlockBusy": false
      }
    }
  },
  "Links": {
    "Drives@odata.count": 1,
    "Drives": [
      {
        "@odata.id": "/redfish/v1/Chassis/mmcblk0/Drives/"
      }
    ]
  }
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | 指定块设备资源路径。 |
| `@odata.type` | string | 块设备卷的 OData 类型。 |
| `CapacityBytes` | string | 设备容量，单位为字节。 |
| `RAIDType` | string | RAID 类型。 |
| `VolumeType` | string | 卷类型。 |
| `Oem` | object | OEM 扩展信息。 |
| `Oem.Firefly` | object | Firefly 存储扩展信息。 |
| `Oem.Firefly.Detail` | object | 块设备详细信息。 |
| `Oem.Firefly.Detail.name` | string | 设备名称。 |
| `Oem.Firefly.Detail.path` | string | Linux 设备节点路径。 |
| `Oem.Firefly.Detail.serial` | string | 设备序列号。 |
| `Oem.Firefly.Detail.size` | number | 设备容量，单位为字节。 |
| `Oem.Firefly.Detail.type` | string | 设备类型。 |
| `Oem.Firefly.Detail.pttype` | string | 分区表类型。 |
| `Oem.Firefly.Detail.StorageType` | string | 存储介质类型。 |
| `Oem.Firefly.Detail.OemEnableCreatePartition` | boolean | 是否允许创建分区。 |
| `Oem.Firefly.Detail.OemEnableDeletePartition` | boolean | 是否允许删除分区。 |
| `Oem.Firefly.Detail.OemEnableMakeFS` | boolean | 是否允许创建文件系统。 |
| `Oem.Firefly.Detail.OemEnableMount` | boolean | 是否允许挂载。 |
| `Oem.Firefly.Detail.OemEnableUnmount` | boolean | 是否允许卸载。 |
| `Oem.Firefly.Detail.OemBlockBusy` | boolean | 设备是否正被任务占用。 |
| `Links` | object | 关联资源。 |
| `Links.Drives@odata.count` | number | 关联驱动器数量。 |
| `Links.Drives` | array | 关联驱动器列表。 |
| `Links.Drives[].@odata.id` | string | 关联驱动器路径。 |

<Callout title="先检查能力标志" type="warn">
  当前 `mmcblk0` 是正在运行的系统盘，创建/删除分区、格式化、挂载和卸载能力标志均为 `false`。客户端必须依据详情资源中的 `OemEnable*` 字段决定是否显示或调用对应操作。
</Callout>

### 5.5 查询 SMART 信息

该接口使用 POST，但只读取设备健康信息，不修改分区或文件系统。

| 项目 | 内容 |
| --- | --- |
| 方法 | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/Storages/BlockDevice/Volumes/{BlockDeviceId}/Actions/Oem/Firefly/SmartInfoActionInfo` |
| 路径 | `/redfish/v1/Systems/{SystemId}/Storages/BlockDevice/Volumes/{BlockDeviceId}/Actions/Oem/Firefly/SmartInfo` |
| 请求体 | 空 JSON 对象 `{}` |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="查询 mmcblk0 SMART 信息"
    curl --request POST \
      --user '<username>:<password>' \
      --header 'Content-Type: application/json' \
      --data '{}' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/SmartInfo'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询 mmcblk0 SMART 信息"
    curl --request POST \
      --header 'X-Xsrf-Token: <token>' \
      --header 'Content-Type: application/json' \
      --data '{}' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/SmartInfo'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

以下是该 POST 成功时返回的标准 Redfish Message 响应。成功消息只表示请求处理完成，不包含 SMART 指标字段。

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
| `Timestamp` | string | 服务端生成响应时记录的时间戳。 |
| `MessageId` | string | 消息标识，成功时为 `Base.1.11.0.Success`。 |
| `Message` | string | 请求处理结果说明。 |
| `MessageArgs` | array | 消息格式化参数列表；本响应为空数组。 |
| `MessageSeverity` | string | 消息严重级别，成功时为 `OK`。 |
| `Severity` | string | 兼容保留的严重级别字段；本响应为空字符串。 |
| `Oem` | object \| null | OEM 扩展消息；本响应为 `null`。 |
| `RelatedProperties` | array \| null | 与消息相关的属性路径；本响应为 `null`。 |
| `Resolution` | string | 建议的处理方式；成功时为 `None`。 |

### 5.6 新建分区

| 项目 | 内容 |
| --- | --- |
| 方法 | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/Storages/BlockDevice/Volumes/{BlockDeviceId}/{FreeSpaceId}/Actions/Oem/Firefly/NewPartitionActionInfo` |
| 路径 | `/redfish/v1/Systems/{SystemId}/Storages/BlockDevice/Volumes/{BlockDeviceId}/{FreeSpaceId}/Actions/Oem/Firefly/NewPartition` |
| 前置条件 | 设备树中存在 `FreeSpace=true` 的空闲区域，且 `OemEnableCreatePartition=true` |

| 请求字段 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `StartOffsetMB` | number | 是 | 相对空闲区域起点的偏移，单位为 MiB。 |
| `SizeMB` | number | 是 | 新分区容量，单位为 MiB。 |
| `AlignmentByte` | number | 否 | 对齐字节数：512、1024、2048、4096 或 8192。 |
| `Partlabel` | string | 否 | GPT 分区标签。 |
| `Label` | string | 否 | 文件系统卷标。 |
| `FsType` | string | 否 | `ext4`、`exfat`、`ntfs`、`ext2`、`ext3`、`fat32` 或 `<None>`。 |

```json title="请求体示例"
{
  "StartOffsetMB": 0,
  "SizeMB": 1024,
  "AlignmentByte": 4096,
  "Partlabel": "data",
  "Label": "data",
  "FsType": "ext4"
}
```

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
| `Timestamp` | string | 服务端生成响应时记录的时间戳。 |
| `MessageId` | string | 消息标识，成功时为 `Base.1.11.0.Success`。 |
| `Message` | string | 请求处理结果说明。 |
| `MessageArgs` | array | 消息格式化参数列表；本响应为空数组。 |
| `MessageSeverity` | string | 消息严重级别，成功时为 `OK`。 |
| `Severity` | string | 兼容保留的严重级别字段；本响应为空字符串。 |
| `Oem` | object \| null | OEM 扩展消息；本响应为 `null`。 |
| `RelatedProperties` | array \| null | 与消息相关的属性路径；本响应为 `null`。 |
| `Resolution` | string | 建议的处理方式；成功时为 `None`。 |

### 5.7 写入卷标

| 项目 | 内容 |
| --- | --- |
| 方法 | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/Storages/BlockDevice/Volumes/{BlockDeviceId}/Actions/Oem/Firefly/WriteLabelActionInfo` |
| 路径 | `/redfish/v1/Systems/{SystemId}/Storages/BlockDevice/Volumes/{BlockDeviceId}/Actions/Oem/Firefly/WriteLabel` |
| 请求字段 | `Label`，string，必填 |
| 前置条件 | `OemEnableCreteLabel=true`，且目标不是正在使用的系统分区 |

```json title="请求体示例"
{
  "Label": "data"
}
```

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
| `Timestamp` | string | 服务端生成响应时记录的时间戳。 |
| `MessageId` | string | 消息标识，成功时为 `Base.1.11.0.Success`。 |
| `Message` | string | 请求处理结果说明。 |
| `MessageArgs` | array | 消息格式化参数列表；本响应为空数组。 |
| `MessageSeverity` | string | 消息严重级别，成功时为 `OK`。 |
| `Severity` | string | 兼容保留的严重级别字段；本响应为空字符串。 |
| `Oem` | object \| null | OEM 扩展消息；本响应为 `null`。 |
| `RelatedProperties` | array \| null | 与消息相关的属性路径；本响应为 `null`。 |
| `Resolution` | string | 建议的处理方式；成功时为 `None`。 |

### 5.8 挂载块设备

| 项目 | 内容 |
| --- | --- |
| 方法 | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/Storages/BlockDevice/Volumes/{BlockDeviceId}/Actions/Oem/Firefly/MountActionInfo` |
| 路径 | `/redfish/v1/Systems/{SystemId}/Storages/BlockDevice/Volumes/{BlockDeviceId}/Actions/Oem/Firefly/Mount` |
| 前置条件 | `OemEnableMount=true` |

| 请求字段 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `MountPath` | string | 是 | 绝对且非根目录的挂载路径。 |
| `MountType` | string | 是 | `forever` 表示持久挂载，`tmp` 表示临时挂载。 |
| `RwModel` | string | 否 | `rw` 或 `ro`。 |

```json title="请求体示例"
{
  "MountPath": "/mnt/data",
  "MountType": "tmp",
  "RwModel": "rw"
}
```

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
| `Timestamp` | string | 服务端生成响应时记录的时间戳。 |
| `MessageId` | string | 消息标识，成功时为 `Base.1.11.0.Success`。 |
| `Message` | string | 请求处理结果说明。 |
| `MessageArgs` | array | 消息格式化参数列表；本响应为空数组。 |
| `MessageSeverity` | string | 消息严重级别，成功时为 `OK`。 |
| `Severity` | string | 兼容保留的严重级别字段；本响应为空字符串。 |
| `Oem` | object \| null | OEM 扩展消息；本响应为 `null`。 |
| `RelatedProperties` | array \| null | 与消息相关的属性路径；本响应为 `null`。 |
| `Resolution` | string | 建议的处理方式；成功时为 `None`。 |

### 5.9 卸载块设备

| 项目 | 内容 |
| --- | --- |
| 方法 | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/Storages/BlockDevice/Volumes/{BlockDeviceId}/Actions/Oem/Firefly/UMountActionInfo` |
| 路径 | `/redfish/v1/Systems/{SystemId}/Storages/BlockDevice/Volumes/{BlockDeviceId}/Actions/Oem/Firefly/UMount` |
| 请求字段 | `MountPath`，string，必填 |
| 前置条件 | `OemEnableUnmount=true` |

```json title="请求体示例"
{
  "MountPath": "/mnt/data"
}
```

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
| `Timestamp` | string | 服务端生成响应时记录的时间戳。 |
| `MessageId` | string | 消息标识，成功时为 `Base.1.11.0.Success`。 |
| `Message` | string | 请求处理结果说明。 |
| `MessageArgs` | array | 消息格式化参数列表；本响应为空数组。 |
| `MessageSeverity` | string | 消息严重级别，成功时为 `OK`。 |
| `Severity` | string | 兼容保留的严重级别字段；本响应为空字符串。 |
| `Oem` | object \| null | OEM 扩展消息；本响应为 `null`。 |
| `RelatedProperties` | array \| null | 与消息相关的属性路径；本响应为 `null`。 |
| `Resolution` | string | 建议的处理方式；成功时为 `None`。 |

### 5.10 创建文件系统

| 项目 | 内容 |
| --- | --- |
| 方法 | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/Storages/BlockDevice/Volumes/{BlockDeviceId}/Actions/Oem/Firefly/MakeFileSystemActionInfo` |
| 路径 | `/redfish/v1/Systems/{SystemId}/Storages/BlockDevice/Volumes/{BlockDeviceId}/Actions/Oem/Firefly/MakeFileSystem` |
| 前置条件 | `OemEnableMakeFS=true` |

| 请求字段 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `FsType` | string | 是 | `ext4`、`exfat`、`ntfs`、`ext2`、`ext3` 或 `fat32`。 |
| `Label` | string | 否 | 文件系统卷标。 |

```json title="请求体示例"
{
  "FsType": "ext4",
  "Label": "data"
}
```

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
| `Timestamp` | string | 服务端生成响应时记录的时间戳。 |
| `MessageId` | string | 消息标识，成功时为 `Base.1.11.0.Success`。 |
| `Message` | string | 请求处理结果说明。 |
| `MessageArgs` | array | 消息格式化参数列表；本响应为空数组。 |
| `MessageSeverity` | string | 消息严重级别，成功时为 `OK`。 |
| `Severity` | string | 兼容保留的严重级别字段；本响应为空字符串。 |
| `Oem` | object \| null | OEM 扩展消息；本响应为 `null`。 |
| `RelatedProperties` | array \| null | 与消息相关的属性路径；本响应为 `null`。 |
| `Resolution` | string | 建议的处理方式；成功时为 `None`。 |

### 5.11 检查或修复块设备

| 项目 | 内容 |
| --- | --- |
| 方法 | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/Storages/BlockDevice/Volumes/{BlockDeviceId}/Actions/Oem/Firefly/CheckRepairActionInfo` |
| 路径 | `/redfish/v1/Systems/{SystemId}/Storages/BlockDevice/Volumes/{BlockDeviceId}/Actions/Oem/Firefly/CheckRepair` |
| 请求字段 | `ActionType`，string，必填 |

`ActionType` 允许值：

- `Action_CheckFS`：检查文件系统。
- `Action_CheckFSAutoRepair`：检查并自动修复文件系统。
- `Action_CheckSector`：检查扇区。

```json title="请求体示例"
{
  "ActionType": "Action_CheckFS"
}
```


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
| `Timestamp` | string | 服务端生成响应时记录的时间戳。 |
| `MessageId` | string | 消息标识，成功时为 `Base.1.11.0.Success`。 |
| `Message` | string | 请求处理结果说明。 |
| `MessageArgs` | array | 消息格式化参数列表；本响应为空数组。 |
| `MessageSeverity` | string | 消息严重级别，成功时为 `OK`。 |
| `Severity` | string | 兼容保留的严重级别字段；本响应为空字符串。 |
| `Oem` | object \| null | OEM 扩展消息；本响应为 `null`。 |
| `RelatedProperties` | array \| null | 与消息相关的属性路径；本响应为 `null`。 |
| `Resolution` | string | 建议的处理方式；成功时为 `None`。 |

### 5.12 取消存储任务

| 项目 | 内容 |
| --- | --- |
| 方法 | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/Storages/BlockDevice/Volumes/{BlockDeviceId}/Actions/Oem/Firefly/TaskListActionInfo` |
| 路径 | `/redfish/v1/Systems/{SystemId}/Storages/BlockDevice/Volumes/{BlockDeviceId}/Actions/Oem/Firefly/TaskList` |
| `ActionType` | `Action_CheckFS`、`Action_CheckFSAutoRepair`、`Action_CheckSector` 或 `Action_Mkfs` |
| `Model` | 仅允许 `Cancel` |

```json title="请求体示例"
{
  "ActionType": "Action_CheckFS",
  "Model": "Cancel"
}
```

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
| `Timestamp` | string | 服务端生成响应时记录的时间戳。 |
| `MessageId` | string | 消息标识，成功时为 `Base.1.11.0.Success`。 |
| `Message` | string | 请求处理结果说明。 |
| `MessageArgs` | array | 消息格式化参数列表；本响应为空数组。 |
| `MessageSeverity` | string | 消息严重级别，成功时为 `OK`。 |
| `Severity` | string | 兼容保留的严重级别字段；本响应为空字符串。 |
| `Oem` | object \| null | OEM 扩展消息；本响应为 `null`。 |
| `RelatedProperties` | array \| null | 与消息相关的属性路径；本响应为 `null`。 |
| `Resolution` | string | 建议的处理方式；成功时为 `None`。 |

### 5.13 删除分区

| 项目 | 内容 |
| --- | --- |
| 方法 | `DELETE` |
| 路径 | `/redfish/v1/Systems/{SystemId}/Storages/BlockDevice/Volumes/{BlockDeviceId}/{PartitionId}` |
| 请求体 | 无 |
| 前置条件 | `OemEnableDeletePartition=true`，且目标分区未挂载、不属于系统或 RAID |

<Callout title="不可恢复操作" type="warn">
  删除分区会修改分区表并导致分区数据不可访问。当前 `mmcblk0` 的删除能力为 `false`，未执行 DELETE。
</Callout>

### 5.14 查询软 RAID 存储控制器

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="查询软 RAID 存储控制器"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询软 RAID 存储控制器"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK · 真实响应节选"
{
  "@odata.id": "/redfish/v1/Systems/bmc/Storages/Mdraid",
  "@odata.type": "#Storage.v1_4_0.Storage",
  "StorageControllers@odata.count": 1,
  "StorageControllers": [
    {
      "Id": "mdadm",
      "Description": "SoftRaid Tool in Linux",
      "Manufacturer": "RedHat",
      "Oem": {
        "Firefly": {
          "FreeResource": {
            "Mdname": "",
            "Levels": ["Linear", "Raid0", "Raid1", "Raid4", "Raid5", "Raid6", "Raid10"],
            "Chunks": ["64K", "128K", "256K", "512K"],
            "Layouts": ["left-symmetric", "far", "offset"],
            "Blocks": []
          },
          "RiskInfo": null
        }
      }
    }
  ],
  "Volumes": {}
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | 软 RAID 控制器资源路径。 |
| `@odata.type` | string | 存储资源的 OData 类型。 |
| `StorageControllers@odata.count` | number | 软 RAID 控制器数量。 |
| `StorageControllers` | array | 控制器列表。 |
| `StorageControllers[].Id` | string | 控制器标识。 |
| `StorageControllers[].Description` | string | 控制器描述。 |
| `StorageControllers[].Manufacturer` | string | 控制器实现方。 |
| `StorageControllers[].Oem` | object | 控制器 OEM 信息。 |
| `StorageControllers[].Oem.Firefly` | object | Firefly RAID 信息。 |
| `StorageControllers[].Oem.Firefly.FreeResource` | object | 可用于创建 RAID 的资源。 |
| `StorageControllers[].Oem.Firefly.FreeResource.Mdname` | string | 建议的阵列设备名。 |
| `StorageControllers[].Oem.Firefly.FreeResource.Levels` | array | 支持的 RAID 级别。 |
| `StorageControllers[].Oem.Firefly.FreeResource.Chunks` | array | 支持的条带块大小。 |
| `StorageControllers[].Oem.Firefly.FreeResource.Layouts` | array | 支持的布局。 |
| `StorageControllers[].Oem.Firefly.FreeResource.Blocks` | array | 可加入 RAID 的块设备。 |
| `StorageControllers[].Oem.Firefly.RiskInfo` | object \| null | 最近一次风险评估结果。 |
| `Volumes` | object | RAID 卷入口。 |

### 5.15 查询软 RAID 卷合集

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid/Volumes` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="查询软 RAID 卷合集"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询软 RAID 卷合集"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes",
  "@odata.type": "#VolumeCollection.VolumeCollection",
  "Name": "Soft RaidArray Volume Collection",
  "Members@odata.count": 0,
  "Members": [],
  "Oem": {
    "Firefly": {
      "All": {
        "Raidlist": [],
        "Mdadmconf": null
      }
    }
  }
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | 软 RAID 卷合集路径。 |
| `@odata.type` | string | 卷合集的 OData 类型。 |
| `Name` | string | 卷合集名称。 |
| `Members@odata.count` | number | RAID 阵列数量。 |
| `Members` | array | RAID 阵列成员列表。 |
| `Oem` | object | OEM 扩展信息。 |
| `Oem.Firefly` | object | Firefly RAID 扩展信息。 |
| `Oem.Firefly.All` | object | 全部 RAID 数据。 |
| `Oem.Firefly.All.Raidlist` | array | 后端识别到的 RAID 阵列列表。 |
| `Oem.Firefly.All.Mdadmconf` | object \| null | `mdadm` 配置内容。 |

### 5.16 创建软 RAID 阵列

| 项目 | 内容 |
| --- | --- |
| 方法 | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid/Actions/Oem/Firefly/CreateRaidArrayActionInfo` |
| 路径 | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid/Actions/Oem/Firefly/CreateRaidArray` |

| 请求字段 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `DevName` | string | 是 | 阵列设备路径或设备名。 |
| `MdName` | string | 是 | RAID 阵列名称。 |
| `Level` | string | 是 | `Linear`、`Raid0`、`Raid1`、`Raid4`、`Raid5`、`Raid6` 或 `Raid10`。 |
| `Chunk` | string | 否 | `64K`、`128K`、`256K` 或 `512K`。 |
| `Metadata` | string | 否 | `0.90`、`1.0`、`1.1` 或 `1.2`。 |
| `Blocks` | string[] | 是 | 参与阵列的块设备路径。 |

```json title="请求体示例"
{
  "DevName": "/dev/md0",
  "MdName": "md0",
  "Level": "Raid1",
  "Chunk": "512K",
  "Metadata": "1.2",
  "Blocks": ["/dev/sda", "/dev/sdb"]
}
```

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
| `Timestamp` | string | 服务端生成响应时记录的时间戳。 |
| `MessageId` | string | 消息标识，成功时为 `Base.1.11.0.Success`。 |
| `Message` | string | 请求处理结果说明。 |
| `MessageArgs` | array | 消息格式化参数列表；本响应为空数组。 |
| `MessageSeverity` | string | 消息严重级别，成功时为 `OK`。 |
| `Severity` | string | 兼容保留的严重级别字段；本响应为空字符串。 |
| `Oem` | object \| null | OEM 扩展消息；本响应为 `null`。 |
| `RelatedProperties` | array \| null | 与消息相关的属性路径；本响应为 `null`。 |
| `Resolution` | string | 建议的处理方式；成功时为 `None`。 |

### 5.17 设置 RAID 风险信息

| 项目 | 内容 |
| --- | --- |
| 方法 | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid/Actions/Oem/Firefly/SetRiskInfoActionInfo` |
| 路径 | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid/Actions/Oem/Firefly/SetRiskInfo` |

| 请求字段 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `Level` | string | 否 | 目标 RAID 级别。 |
| `Ops` | string | 是 | `create`、`expan` 或 `change`。 |
| `Blocks` | object[] | 是 | 风险评估使用的磁盘列表。 |
| `Blocks[].BlockName` | string | 是 | 块设备名称。 |
| `Blocks[].Size` | number | 是 | 块设备容量。 |
| `Blocks[].Rota` | boolean | 是 | 是否为旋转介质。 |

```json title="请求体示例"
{
  "Level": "Raid1",
  "Ops": "create",
  "Blocks": [
    {
      "BlockName": "/dev/sda",
      "Size": 1000000000000,
      "Rota": true
    }
  ]
}
```

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
| `Timestamp` | string | 服务端生成响应时记录的时间戳。 |
| `MessageId` | string | 消息标识，成功时为 `Base.1.11.0.Success`。 |
| `Message` | string | 请求处理结果说明。 |
| `MessageArgs` | array | 消息格式化参数列表；本响应为空数组。 |
| `MessageSeverity` | string | 消息严重级别，成功时为 `OK`。 |
| `Severity` | string | 兼容保留的严重级别字段；本响应为空字符串。 |
| `Oem` | object \| null | OEM 扩展消息；本响应为 `null`。 |
| `RelatedProperties` | array \| null | 与消息相关的属性路径；本响应为 `null`。 |
| `Resolution` | string | 建议的处理方式；成功时为 `None`。 |

### 5.18 查询指定软 RAID 阵列

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid/Volumes/{RaidArrayId}` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="查询指定软 RAID 阵列"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/<raid-array-id>'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询指定软 RAID 阵列"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/<raid-array-id>'
    ```
  </CodeBlockTab>
</CodeBlockTabs>


### 5.19 从 RAID 移除成员盘

| 项目 | 内容 |
| --- | --- |
| 方法 | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid/Volumes/{RaidArrayId}/Actions/Oem/Firefly/RemoveDiskActionInfo` |
| 路径 | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid/Volumes/{RaidArrayId}/Actions/Oem/Firefly/RemoveDisk` |
| 请求字段 | `BlocksPath`，string[]，必填 |
| 服务端限制 | 代码注释说明仅 RAID1 支持移除成员盘 |

```json title="请求体示例"
{
  "BlocksPath": ["/dev/sdb"]
}
```

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
| `Timestamp` | string | 服务端生成响应时记录的时间戳。 |
| `MessageId` | string | 消息标识，成功时为 `Base.1.11.0.Success`。 |
| `Message` | string | 请求处理结果说明。 |
| `MessageArgs` | array | 消息格式化参数列表；本响应为空数组。 |
| `MessageSeverity` | string | 消息严重级别，成功时为 `OK`。 |
| `Severity` | string | 兼容保留的严重级别字段；本响应为空字符串。 |
| `Oem` | object \| null | OEM 扩展消息；本响应为 `null`。 |
| `RelatedProperties` | array \| null | 与消息相关的属性路径；本响应为 `null`。 |
| `Resolution` | string | 建议的处理方式；成功时为 `None`。 |

### 5.20 向 RAID 添加成员盘

| 项目 | 内容 |
| --- | --- |
| 方法 | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid/Volumes/{RaidArrayId}/Actions/Oem/Firefly/AddDiskActionInfo` |
| 路径 | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid/Volumes/{RaidArrayId}/Actions/Oem/Firefly/AddDisk` |
| 请求字段 | `BlocksPath`，string[]，必填 |

```json title="请求体示例"
{
  "BlocksPath": ["/dev/sdc"]
}
```

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
| `Timestamp` | string | 服务端生成响应时记录的时间戳。 |
| `MessageId` | string | 消息标识，成功时为 `Base.1.11.0.Success`。 |
| `Message` | string | 请求处理结果说明。 |
| `MessageArgs` | array | 消息格式化参数列表；本响应为空数组。 |
| `MessageSeverity` | string | 消息严重级别，成功时为 `OK`。 |
| `Severity` | string | 兼容保留的严重级别字段；本响应为空字符串。 |
| `Oem` | object \| null | OEM 扩展消息；本响应为 `null`。 |
| `RelatedProperties` | array \| null | 与消息相关的属性路径；本响应为 `null`。 |
| `Resolution` | string | 建议的处理方式；成功时为 `None`。 |

### 5.21 设置 RAID Bitmap

| 项目 | 内容 |
| --- | --- |
| 方法 | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid/Volumes/{RaidArrayId}/Actions/Oem/Firefly/BitmapActionInfo` |
| 路径 | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid/Volumes/{RaidArrayId}/Actions/Oem/Firefly/Bitmap` |
| 请求字段 | `BitmapFlag`，boolean，必填 |

```json title="请求体示例"
{
  "BitmapFlag": true
}
```

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
| `Timestamp` | string | 服务端生成响应时记录的时间戳。 |
| `MessageId` | string | 消息标识，成功时为 `Base.1.11.0.Success`。 |
| `Message` | string | 请求处理结果说明。 |
| `MessageArgs` | array | 消息格式化参数列表；本响应为空数组。 |
| `MessageSeverity` | string | 消息严重级别，成功时为 `OK`。 |
| `Severity` | string | 兼容保留的严重级别字段；本响应为空字符串。 |
| `Oem` | object \| null | OEM 扩展消息；本响应为 `null`。 |
| `RelatedProperties` | array \| null | 与消息相关的属性路径；本响应为 `null`。 |
| `Resolution` | string | 建议的处理方式；成功时为 `None`。 |

### 5.22 替换 RAID 成员盘

| 项目 | 内容 |
| --- | --- |
| 方法 | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid/Volumes/{RaidArrayId}/Actions/Oem/Firefly/ReplaceDiskActionInfo` |
| 路径 | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid/Volumes/{RaidArrayId}/Actions/Oem/Firefly/ReplaceDisk` |

| 请求字段 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `OldBlocksPath` | string[] | 是 | 要替换的原成员盘路径。 |
| `NewBlocksPath` | string[] | 是 | 新成员盘路径。 |

```json title="请求体示例"
{
  "OldBlocksPath": ["/dev/sdb"],
  "NewBlocksPath": ["/dev/sdc"]
}
```

<Callout title="成员盘操作未执行" type="warn">
  当前 RAID 卷合集为空，无法生成对应 ActionInfo，也没有可安全操作的成员盘。以上方法、路径和参数均已从服务端注册代码与请求结构核对，但未执行 POST。
</Callout>

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
| `Timestamp` | string | 服务端生成响应时记录的时间戳。 |
| `MessageId` | string | 消息标识，成功时为 `Base.1.11.0.Success`。 |
| `Message` | string | 请求处理结果说明。 |
| `MessageArgs` | array | 消息格式化参数列表；本响应为空数组。 |
| `MessageSeverity` | string | 消息严重级别，成功时为 `OK`。 |
| `Severity` | string | 兼容保留的严重级别字段；本响应为空字符串。 |
| `Oem` | object \| null | OEM 扩展消息；本响应为 `null`。 |
| `RelatedProperties` | array \| null | 与消息相关的属性路径；本响应为 `null`。 |
| `Resolution` | string | 建议的处理方式；成功时为 `None`。 |

### 5.23 获取 RAID 整形风险上下文

| 项目 | 内容 |
| --- | --- |
| 方法 | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid/Volumes/{RaidArrayId}/Actions/Oem/Firefly/Reshape/SetCurrentRiskInfoActionInfo` |
| 路径 | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid/Volumes/{RaidArrayId}/Actions/Oem/Firefly/Reshape/SetCurrentRiskInfo` |
| 请求字段 | `Level`，string，必填 |
| 允许值 | `Linear`、`Raid0`、`Raid1`、`Raid4`、`Raid5`、`Raid6`、`Raid10` |

```json title="请求体示例"
{
  "Level": "Raid5"
}
```

该接口计算当前阵列的整形风险和 RAID 级别能力，并保存到阵列运行态。当前没有阵列，未执行 POST。

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
| `Timestamp` | string | 服务端生成响应时记录的时间戳。 |
| `MessageId` | string | 消息标识，成功时为 `Base.1.11.0.Success`。 |
| `Message` | string | 请求处理结果说明。 |
| `MessageArgs` | array | 消息格式化参数列表；本响应为空数组。 |
| `MessageSeverity` | string | 消息严重级别，成功时为 `OK`。 |
| `Severity` | string | 兼容保留的严重级别字段；本响应为空字符串。 |
| `Oem` | object \| null | OEM 扩展消息；本响应为 `null`。 |
| `RelatedProperties` | array \| null | 与消息相关的属性路径；本响应为 `null`。 |
| `Resolution` | string | 建议的处理方式；成功时为 `None`。 |

### 5.24 比较 RAID 整形目标级别

| 项目 | 内容 |
| --- | --- |
| 方法 | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid/Volumes/{RaidArrayId}/Actions/Oem/Firefly/Reshape/CompareActionInfo` |
| 路径 | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid/Volumes/{RaidArrayId}/Actions/Oem/Firefly/Reshape/Compare` |
| 请求字段 | `Level`，string，必填 |
| 允许值 | `Linear`、`Raid0`、`Raid1`、`Raid4`、`Raid5`、`Raid6`、`Raid10` |

```json title="请求体示例"
{
  "Level": "Raid5"
}
```

服务端会计算升级或降级方向、目标容量、需要增加或释放的磁盘数量，以及 RAID10 的磁盘数量约束。

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
| `Timestamp` | string | 服务端生成响应时记录的时间戳。 |
| `MessageId` | string | 消息标识，成功时为 `Base.1.11.0.Success`。 |
| `Message` | string | 请求处理结果说明。 |
| `MessageArgs` | array | 消息格式化参数列表；本响应为空数组。 |
| `MessageSeverity` | string | 消息严重级别，成功时为 `OK`。 |
| `Severity` | string | 兼容保留的严重级别字段；本响应为空字符串。 |
| `Oem` | object \| null | OEM 扩展消息；本响应为 `null`。 |
| `RelatedProperties` | array \| null | 与消息相关的属性路径；本响应为 `null`。 |
| `Resolution` | string | 建议的处理方式；成功时为 `None`。 |

### 5.25 计算 RAID 整形风险

| 项目 | 内容 |
| --- | --- |
| 方法 | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid/Volumes/{RaidArrayId}/Actions/Oem/Firefly/Reshape/RiskActionInfo` |
| 路径 | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid/Volumes/{RaidArrayId}/Actions/Oem/Firefly/Reshape/Risk` |

| 请求字段 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `Level` | string | 否 | 目标 RAID 级别。 |
| `Ops` | string | 是 | `create`、`expan` 或 `change`。 |
| `Blocks` | object[] | 是 | 参与风险计算的磁盘。 |
| `Blocks[].BlockName` | string | 是 | 块设备名称。 |
| `Blocks[].Size` | number | 是 | 块设备容量。 |
| `Blocks[].Rota` | boolean | 是 | 是否为旋转介质。 |

```json title="请求体示例"
{
  "Level": "Raid5",
  "Ops": "change",
  "Blocks": [
    {
      "BlockName": "/dev/sdc",
      "Size": 1000000000000,
      "Rota": true
    }
  ]
}
```

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
| `Timestamp` | string | 服务端生成响应时记录的时间戳。 |
| `MessageId` | string | 消息标识，成功时为 `Base.1.11.0.Success`。 |
| `Message` | string | 请求处理结果说明。 |
| `MessageArgs` | array | 消息格式化参数列表；本响应为空数组。 |
| `MessageSeverity` | string | 消息严重级别，成功时为 `OK`。 |
| `Severity` | string | 兼容保留的严重级别字段；本响应为空字符串。 |
| `Oem` | object \| null | OEM 扩展消息；本响应为 `null`。 |
| `RelatedProperties` | array \| null | 与消息相关的属性路径；本响应为 `null`。 |
| `Resolution` | string | 建议的处理方式；成功时为 `None`。 |

### 5.26 执行 RAID 整形

| 项目 | 内容 |
| --- | --- |
| 方法 | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid/Volumes/{RaidArrayId}/Actions/Oem/Firefly/Reshape/ExecActionInfo` |
| 路径 | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid/Volumes/{RaidArrayId}/Actions/Oem/Firefly/Reshape/Exec` |
| 请求字段 | 与“计算 RAID 整形风险”相同 |

```json title="请求体示例"
{
  "Level": "Raid5",
  "Ops": "change",
  "Blocks": [
    {
      "BlockName": "/dev/sdc",
      "Size": 1000000000000,
      "Rota": true
    }
  ]
}
```

<Callout title="整形不可仅凭请求成功判断" type="warn">
  整形可能长时间运行并导致阵列降级。执行前应先调用风险上下文、级别比较和风险计算接口；执行后持续查询指定 RAID 阵列状态。当前没有 RAID 阵列，本次未执行任何整形 POST。
</Callout>

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
| `Timestamp` | string | 服务端生成响应时记录的时间戳。 |
| `MessageId` | string | 消息标识，成功时为 `Base.1.11.0.Success`。 |
| `Message` | string | 请求处理结果说明。 |
| `MessageArgs` | array | 消息格式化参数列表；本响应为空数组。 |
| `MessageSeverity` | string | 消息严重级别，成功时为 `OK`。 |
| `Severity` | string | 兼容保留的严重级别字段；本响应为空字符串。 |
| `Oem` | object \| null | OEM 扩展消息；本响应为 `null`。 |
| `RelatedProperties` | array \| null | 与消息相关的属性路径；本响应为 `null`。 |
| `Resolution` | string | 建议的处理方式；成功时为 `None`。 |

### 5.27 组装软 RAID 阵列

| 项目 | 内容 |
| --- | --- |
| 方法 | `POST` |
| 路径 | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid/Volumes/{RaidArrayId}/Actions/Assemble` |
| ActionInfo | 无独立 ActionInfo |
| 请求体 | 无 |

该路由调用服务端 `AssembleRaid`。当前没有可组装阵列，未执行 POST。

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
| `Timestamp` | string | 服务端生成响应时记录的时间戳。 |
| `MessageId` | string | 消息标识，成功时为 `Base.1.11.0.Success`。 |
| `Message` | string | 请求处理结果说明。 |
| `MessageArgs` | array | 消息格式化参数列表；本响应为空数组。 |
| `MessageSeverity` | string | 消息严重级别，成功时为 `OK`。 |
| `Severity` | string | 兼容保留的严重级别字段；本响应为空字符串。 |
| `Oem` | object \| null | OEM 扩展消息；本响应为 `null`。 |
| `RelatedProperties` | array \| null | 与消息相关的属性路径；本响应为 `null`。 |
| `Resolution` | string | 建议的处理方式；成功时为 `None`。 |

### 5.28 删除软 RAID 阵列

| 项目 | 内容 |
| --- | --- |
| 方法 | `DELETE` |
| 路径 | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid/Volumes/{RaidArrayId}` |
| 请求体 | 无 |

<Callout title="RAID 删除风险" type="warn">
  服务端会调用 `DeleteRaid` 删除阵列。该操作可能清除 RAID 元数据并导致数据不可访问；当前没有 RAID 阵列，未执行 DELETE。
</Callout>

### 5.29 查询 SMART 操作 ActionInfo

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/SmartInfoActionInfo` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 实机状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询 SMART 操作 ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/SmartInfoActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询 SMART 操作 ActionInfo"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/SmartInfoActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/SmartInfoActionInfo",
  "@odata.type": "#ActionInfo.v1_1_2.ActionInfo",
  "Id": "ActionInfo",
  "Name": "Action Info",
  "Parameters": null
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | ActionInfo 资源路径。 |
| `@odata.type` | string | ActionInfo 或消息资源的 OData 类型。 |
| `Id` | string | ActionInfo 资源标识。 |
| `Name` | string | ActionInfo 资源名称。 |
| `Parameters` | null | 操作参数定义列表。 |

### 5.30 查询新建分区 ActionInfo

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/free0/Actions/Oem/Firefly/NewPartitionActionInfo` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 实机状态码 | `405 Method Not Allowed` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询新建分区 ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/free0/Actions/Oem/Firefly/NewPartitionActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询新建分区 ActionInfo"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/free0/Actions/Oem/Firefly/NewPartitionActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="405 Method Not Allowed"
{
  "error": {
    "@Message.ExtendedInfo": [
      {
        "@odata.type": "#Message.v1_1_1.Message",
        "Timestamp": "1785863973",
        "MessageId": "Base.1.11.0.GeneralError",
        "Message": "[free0] not found in unallocated partition",
        "MessageArgs": [

        ],
        "MessageSeverity": "Critical",
        "Severity": "",
        "Oem": null,
        "RelatedProperties": null,
        "Resolution": "None"
      }
    ],
    "code": "Base.1.11.0.GeneralError",
    "message": "A general error has occurred. See ExtendedInfo for more information."
  }
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `error` | object | Redfish 错误对象。 |
| `error.@Message.ExtendedInfo` | array | 错误详细信息列表。 |
| `error.@Message.ExtendedInfo[].@odata.type` | string | ActionInfo 或消息资源的 OData 类型。 |
| `error.@Message.ExtendedInfo[].Timestamp` | string | 消息生成时间戳。 |
| `error.@Message.ExtendedInfo[].MessageId` | string | Redfish 消息标识。 |
| `error.@Message.ExtendedInfo[].Message` | string | 具体错误消息。 |
| `error.@Message.ExtendedInfo[].MessageArgs` | array | 消息格式化参数列表。 |
| `error.@Message.ExtendedInfo[].MessageSeverity` | string | 消息严重级别。 |
| `error.@Message.ExtendedInfo[].Severity` | string | 兼容保留的严重级别字段。 |
| `error.@Message.ExtendedInfo[].Oem` | null | OEM 扩展消息。 |
| `error.@Message.ExtendedInfo[].RelatedProperties` | null | 与消息相关的属性路径。 |
| `error.@Message.ExtendedInfo[].Resolution` | string | 建议的处理方式。 |
| `error.code` | string | Redfish 错误代码。 |
| `error.message` | string | Redfish 通用错误说明。 |

<Callout title="当前设备无未分配空间" type="info">
  当前系统盘没有可用于新建分区的未分配区域，因此以 `free0` 查询时真实返回 `405 Method Not Allowed`。出现有效空闲区资源后，应将路径中的 `free0` 替换为查询结果返回的实际空闲区标识。
</Callout>

### 5.31 查询写入卷标 ActionInfo

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/WriteLabelActionInfo` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 实机状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询写入卷标 ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/WriteLabelActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询写入卷标 ActionInfo"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/WriteLabelActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/WriteLabelActionInfo",
  "@odata.type": "#ActionInfo.v1_1_2.ActionInfo",
  "Id": "ActionInfo",
  "Name": "Action Info",
  "Parameters": [
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "DataType": "String",
      "Name": "Label",
      "Required": true
    }
  ]
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | ActionInfo 资源路径。 |
| `@odata.type` | string | ActionInfo 或消息资源的 OData 类型。 |
| `Id` | string | ActionInfo 资源标识。 |
| `Name` | string | ActionInfo 资源名称。 |
| `Parameters` | array | 操作参数定义列表。 |
| `Parameters[].DisallowedInput` | boolean | 该参数是否禁止输入。 |
| `Parameters[].AllowablePattern` | string | 参数允许的字符串模式；空字符串表示未限制模式。 |
| `Parameters[].DataType` | string | 参数数据类型。 |
| `Parameters[].Name` | string | 参数名称。 |
| `Parameters[].Required` | boolean | 参数是否必填。 |

### 5.32 查询挂载块设备 ActionInfo

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/MountActionInfo` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 实机状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询挂载块设备 ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/MountActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询挂载块设备 ActionInfo"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/MountActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/MountActionInfo",
  "@odata.type": "#ActionInfo.v1_1_2.ActionInfo",
  "Id": "ActionInfo",
  "Name": "Action Info",
  "Parameters": [
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "DataType": "String",
      "Name": "MountPath",
      "Required": true
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "AllowableValues": [
        "forever",
        "tmp"
      ],
      "DataType": "String",
      "Name": "MountType",
      "Required": true
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "AllowableValues": [
        "rw",
        "ro"
      ],
      "DataType": "String",
      "Name": "RwModel",
      "Required": false
    }
  ]
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | ActionInfo 资源路径。 |
| `@odata.type` | string | ActionInfo 或消息资源的 OData 类型。 |
| `Id` | string | ActionInfo 资源标识。 |
| `Name` | string | ActionInfo 资源名称。 |
| `Parameters` | array | 操作参数定义列表。 |
| `Parameters[].DisallowedInput` | boolean | 该参数是否禁止输入。 |
| `Parameters[].AllowablePattern` | string | 参数允许的字符串模式；空字符串表示未限制模式。 |
| `Parameters[].DataType` | string | 参数数据类型。 |
| `Parameters[].Name` | string | 参数名称。 |
| `Parameters[].Required` | boolean | 参数是否必填。 |
| `Parameters[].AllowableValues` | array | 参数或子参数允许的取值与结构定义。 |

### 5.33 查询卸载块设备 ActionInfo

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/UMountActionInfo` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 实机状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询卸载块设备 ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/UMountActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询卸载块设备 ActionInfo"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/UMountActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/UMountActionInfo",
  "@odata.type": "#ActionInfo.v1_1_2.ActionInfo",
  "Id": "ActionInfo",
  "Name": "Action Info",
  "Parameters": [
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "DataType": "String",
      "Name": "MountPath",
      "Required": true
    }
  ]
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | ActionInfo 资源路径。 |
| `@odata.type` | string | ActionInfo 或消息资源的 OData 类型。 |
| `Id` | string | ActionInfo 资源标识。 |
| `Name` | string | ActionInfo 资源名称。 |
| `Parameters` | array | 操作参数定义列表。 |
| `Parameters[].DisallowedInput` | boolean | 该参数是否禁止输入。 |
| `Parameters[].AllowablePattern` | string | 参数允许的字符串模式；空字符串表示未限制模式。 |
| `Parameters[].DataType` | string | 参数数据类型。 |
| `Parameters[].Name` | string | 参数名称。 |
| `Parameters[].Required` | boolean | 参数是否必填。 |

### 5.34 查询创建文件系统 ActionInfo

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/MakeFileSystemActionInfo` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 实机状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询创建文件系统 ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/MakeFileSystemActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询创建文件系统 ActionInfo"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/MakeFileSystemActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/MakeFileSystemActionInfo",
  "@odata.type": "#ActionInfo.v1_1_2.ActionInfo",
  "Id": "ActionInfo",
  "Name": "Action Info",
  "Parameters": [
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "AllowableValues": [
        "ext4",
        "exfat",
        "ntfs",
        "ext2",
        "ext3",
        "fat32"
      ],
      "DataType": "String",
      "Name": "FsType",
      "Required": true
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "DataType": "String",
      "Name": "Label",
      "Required": false
    }
  ]
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | ActionInfo 资源路径。 |
| `@odata.type` | string | ActionInfo 或消息资源的 OData 类型。 |
| `Id` | string | ActionInfo 资源标识。 |
| `Name` | string | ActionInfo 资源名称。 |
| `Parameters` | array | 操作参数定义列表。 |
| `Parameters[].DisallowedInput` | boolean | 该参数是否禁止输入。 |
| `Parameters[].AllowablePattern` | string | 参数允许的字符串模式；空字符串表示未限制模式。 |
| `Parameters[].AllowableValues` | array | 参数或子参数允许的取值与结构定义。 |
| `Parameters[].DataType` | string | 参数数据类型。 |
| `Parameters[].Name` | string | 参数名称。 |
| `Parameters[].Required` | boolean | 参数是否必填。 |

### 5.35 查询检查修复 ActionInfo

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/CheckRepairActionInfo` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 实机状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询检查修复 ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/CheckRepairActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询检查修复 ActionInfo"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/CheckRepairActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/CheckRepairActionInfo",
  "@odata.type": "#ActionInfo.v1_1_2.ActionInfo",
  "Id": "ActionInfo",
  "Name": "Action Info",
  "Parameters": [
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "AllowableValues": [
        "Action_CheckFS",
        "Action_CheckFSAutoRepair",
        "Action_CheckSector"
      ],
      "DataType": "String",
      "Name": "ActionType",
      "Required": true
    }
  ]
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | ActionInfo 资源路径。 |
| `@odata.type` | string | ActionInfo 或消息资源的 OData 类型。 |
| `Id` | string | ActionInfo 资源标识。 |
| `Name` | string | ActionInfo 资源名称。 |
| `Parameters` | array | 操作参数定义列表。 |
| `Parameters[].DisallowedInput` | boolean | 该参数是否禁止输入。 |
| `Parameters[].AllowablePattern` | string | 参数允许的字符串模式；空字符串表示未限制模式。 |
| `Parameters[].AllowableValues` | array | 参数或子参数允许的取值与结构定义。 |
| `Parameters[].DataType` | string | 参数数据类型。 |
| `Parameters[].Name` | string | 参数名称。 |
| `Parameters[].Required` | boolean | 参数是否必填。 |

### 5.36 查询取消存储任务 ActionInfo

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/TaskListActionInfo` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 实机状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询取消存储任务 ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/TaskListActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询取消存储任务 ActionInfo"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/TaskListActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/TaskListActionInfo",
  "@odata.type": "#ActionInfo.v1_1_2.ActionInfo",
  "Id": "ActionInfo",
  "Name": "Action Info",
  "Parameters": [
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "AllowableValues": [
        "Action_CheckFS",
        "Action_CheckFSAutoRepair",
        "Action_CheckSector",
        "Action_CheckFS",
        "Action_Mkfs"
      ],
      "DataType": "String",
      "Name": "ActionType",
      "Required": true
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "AllowableValues": [
        "Cancel"
      ],
      "DataType": "String",
      "Name": "Model",
      "Required": true
    }
  ]
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | ActionInfo 资源路径。 |
| `@odata.type` | string | ActionInfo 或消息资源的 OData 类型。 |
| `Id` | string | ActionInfo 资源标识。 |
| `Name` | string | ActionInfo 资源名称。 |
| `Parameters` | array | 操作参数定义列表。 |
| `Parameters[].DisallowedInput` | boolean | 该参数是否禁止输入。 |
| `Parameters[].AllowablePattern` | string | 参数允许的字符串模式；空字符串表示未限制模式。 |
| `Parameters[].AllowableValues` | array | 参数或子参数允许的取值与结构定义。 |
| `Parameters[].DataType` | string | 参数数据类型。 |
| `Parameters[].Name` | string | 参数名称。 |
| `Parameters[].Required` | boolean | 参数是否必填。 |

### 5.37 查询创建软 RAID ActionInfo

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/bmc/Storages/Mdraid/Actions/Oem/Firefly/CreateRaidArrayActionInfo` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 实机状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询创建软 RAID ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid/Actions/Oem/Firefly/CreateRaidArrayActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询创建软 RAID ActionInfo"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid/Actions/Oem/Firefly/CreateRaidArrayActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Systems/bmc/Storages/Mdraid/Actions/Oem/Firefly/CreateRaidArrayActionInfo",
  "@odata.type": "#ActionInfo.v1_1_2.ActionInfo",
  "Id": "ActionInfo",
  "Name": "Action Info",
  "Parameters": [
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "DataType": "String",
      "Name": "DevName",
      "Required": true
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "DataType": "String",
      "Name": "MdName",
      "Required": true
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "AllowableValues": [
        "Linear",
        "Raid0",
        "Raid1",
        "Raid4",
        "Raid5",
        "Raid6",
        "Raid10"
      ],
      "DataType": "String",
      "Name": "Level",
      "Required": true
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "AllowableValues": [
        "64K",
        "128K",
        "256K",
        "512K"
      ],
      "DataType": "String",
      "Name": "Chunk",
      "Required": false
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "AllowableValues": [
        "0.90",
        "1.0",
        "1.1",
        "1.2"
      ],
      "DataType": "String",
      "Name": "Metadata",
      "Required": false
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "DataType": "StringArray",
      "Name": "Blocks",
      "Required": true
    }
  ]
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | ActionInfo 资源路径。 |
| `@odata.type` | string | ActionInfo 或消息资源的 OData 类型。 |
| `Id` | string | ActionInfo 资源标识。 |
| `Name` | string | ActionInfo 资源名称。 |
| `Parameters` | array | 操作参数定义列表。 |
| `Parameters[].DisallowedInput` | boolean | 该参数是否禁止输入。 |
| `Parameters[].AllowablePattern` | string | 参数允许的字符串模式；空字符串表示未限制模式。 |
| `Parameters[].DataType` | string | 参数数据类型。 |
| `Parameters[].Name` | string | 参数名称。 |
| `Parameters[].Required` | boolean | 参数是否必填。 |
| `Parameters[].AllowableValues` | array | 参数或子参数允许的取值与结构定义。 |

### 5.38 查询设置 RAID 风险信息 ActionInfo

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/bmc/Storages/Mdraid/Actions/Oem/Firefly/SetRiskInfoActionInfo` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 实机状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询设置 RAID 风险信息 ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid/Actions/Oem/Firefly/SetRiskInfoActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询设置 RAID 风险信息 ActionInfo"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid/Actions/Oem/Firefly/SetRiskInfoActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Systems/bmc/Storages/Mdraid/Actions/Oem/Firefly/SetRiskInfoActionInfo",
  "@odata.type": "#ActionInfo.v1_1_2.ActionInfo",
  "Id": "ActionInfo",
  "Name": "Action Info",
  "Parameters": [
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "AllowableValues": [
        "Linear",
        "Raid0",
        "Raid1",
        "Raid4",
        "Raid5",
        "Raid6",
        "Raid10"
      ],
      "DataType": "String",
      "Name": "Level",
      "Required": false
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "AllowableValues": [
        "create",
        "expan",
        "change"
      ],
      "DataType": "String",
      "Name": "Ops",
      "Required": true
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "AllowableValues": [
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "String",
          "Name": "BlockName",
          "Required": true
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "Number",
          "Name": "Size",
          "Required": true
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "Boolean",
          "Name": "Rota",
          "Required": true
        }
      ],
      "DataType": "ObjectArray",
      "Name": "Blocks",
      "Required": true
    }
  ]
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | ActionInfo 资源路径。 |
| `@odata.type` | string | ActionInfo 或消息资源的 OData 类型。 |
| `Id` | string | ActionInfo 资源标识。 |
| `Name` | string | ActionInfo 资源名称。 |
| `Parameters` | array | 操作参数定义列表。 |
| `Parameters[].DisallowedInput` | boolean | 该参数是否禁止输入。 |
| `Parameters[].AllowablePattern` | string | 参数允许的字符串模式；空字符串表示未限制模式。 |
| `Parameters[].AllowableValues` | array | 参数或子参数允许的取值与结构定义。 |
| `Parameters[].DataType` | string | 参数数据类型。 |
| `Parameters[].Name` | string | 参数名称。 |
| `Parameters[].Required` | boolean | 参数是否必填。 |
| `Parameters[].AllowableValues[].DisallowedInput` | boolean | 该参数是否禁止输入。 |
| `Parameters[].AllowableValues[].AllowablePattern` | string | 参数允许的字符串模式；空字符串表示未限制模式。 |
| `Parameters[].AllowableValues[].DataType` | string | 参数数据类型。 |
| `Parameters[].AllowableValues[].Name` | string | 参数名称。 |
| `Parameters[].AllowableValues[].Required` | boolean | 参数是否必填。 |

### 5.39 查询移除 RAID 成员盘 ActionInfo

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/RemoveDiskActionInfo` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 实机状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询移除 RAID 成员盘 ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/RemoveDiskActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询移除 RAID 成员盘 ActionInfo"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/RemoveDiskActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/RemoveDiskActionInfo",
  "@odata.type": "#ActionInfo.v1_1_2.ActionInfo",
  "Id": "ActionInfo",
  "Name": "Action Info",
  "Parameters": [
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "DataType": "StringArray",
      "Name": "BlocksPath",
      "Required": true
    }
  ]
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | ActionInfo 资源路径。 |
| `@odata.type` | string | ActionInfo 或消息资源的 OData 类型。 |
| `Id` | string | ActionInfo 资源标识。 |
| `Name` | string | ActionInfo 资源名称。 |
| `Parameters` | array | 操作参数定义列表。 |
| `Parameters[].DisallowedInput` | boolean | 该参数是否禁止输入。 |
| `Parameters[].AllowablePattern` | string | 参数允许的字符串模式；空字符串表示未限制模式。 |
| `Parameters[].DataType` | string | 参数数据类型。 |
| `Parameters[].Name` | string | 参数名称。 |
| `Parameters[].Required` | boolean | 参数是否必填。 |

### 5.40 查询添加 RAID 成员盘 ActionInfo

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/AddDiskActionInfo` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 实机状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询添加 RAID 成员盘 ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/AddDiskActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询添加 RAID 成员盘 ActionInfo"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/AddDiskActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/AddDiskActionInfo",
  "@odata.type": "#ActionInfo.v1_1_2.ActionInfo",
  "Id": "ActionInfo",
  "Name": "Action Info",
  "Parameters": [
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "DataType": "StringArray",
      "Name": "BlocksPath",
      "Required": true
    }
  ]
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | ActionInfo 资源路径。 |
| `@odata.type` | string | ActionInfo 或消息资源的 OData 类型。 |
| `Id` | string | ActionInfo 资源标识。 |
| `Name` | string | ActionInfo 资源名称。 |
| `Parameters` | array | 操作参数定义列表。 |
| `Parameters[].DisallowedInput` | boolean | 该参数是否禁止输入。 |
| `Parameters[].AllowablePattern` | string | 参数允许的字符串模式；空字符串表示未限制模式。 |
| `Parameters[].DataType` | string | 参数数据类型。 |
| `Parameters[].Name` | string | 参数名称。 |
| `Parameters[].Required` | boolean | 参数是否必填。 |

### 5.41 查询 RAID Bitmap ActionInfo

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/BitmapActionInfo` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 实机状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询 RAID Bitmap ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/BitmapActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询 RAID Bitmap ActionInfo"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/BitmapActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/BitmapActionInfo",
  "@odata.type": "#ActionInfo.v1_1_2.ActionInfo",
  "Id": "ActionInfo",
  "Name": "Action Info",
  "Parameters": [
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "AllowableValues": [
        true,
        false
      ],
      "DataType": "Boolean",
      "Name": "BitmapFlag",
      "Required": true
    }
  ]
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | ActionInfo 资源路径。 |
| `@odata.type` | string | ActionInfo 或消息资源的 OData 类型。 |
| `Id` | string | ActionInfo 资源标识。 |
| `Name` | string | ActionInfo 资源名称。 |
| `Parameters` | array | 操作参数定义列表。 |
| `Parameters[].DisallowedInput` | boolean | 该参数是否禁止输入。 |
| `Parameters[].AllowablePattern` | string | 参数允许的字符串模式；空字符串表示未限制模式。 |
| `Parameters[].AllowableValues` | array | 参数或子参数允许的取值与结构定义。 |
| `Parameters[].DataType` | string | 参数数据类型。 |
| `Parameters[].Name` | string | 参数名称。 |
| `Parameters[].Required` | boolean | 参数是否必填。 |

### 5.42 查询替换 RAID 成员盘 ActionInfo

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/ReplaceDiskActionInfo` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 实机状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询替换 RAID 成员盘 ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/ReplaceDiskActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询替换 RAID 成员盘 ActionInfo"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/ReplaceDiskActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/ReplaceDiskActionInfo",
  "@odata.type": "#ActionInfo.v1_1_2.ActionInfo",
  "Id": "ActionInfo",
  "Name": "Action Info",
  "Parameters": [
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "DataType": "StringArray",
      "Name": "OldBlocksPath",
      "Required": true
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "DataType": "StringArray",
      "Name": "NewBlocksPath",
      "Required": true
    }
  ]
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | ActionInfo 资源路径。 |
| `@odata.type` | string | ActionInfo 或消息资源的 OData 类型。 |
| `Id` | string | ActionInfo 资源标识。 |
| `Name` | string | ActionInfo 资源名称。 |
| `Parameters` | array | 操作参数定义列表。 |
| `Parameters[].DisallowedInput` | boolean | 该参数是否禁止输入。 |
| `Parameters[].AllowablePattern` | string | 参数允许的字符串模式；空字符串表示未限制模式。 |
| `Parameters[].DataType` | string | 参数数据类型。 |
| `Parameters[].Name` | string | 参数名称。 |
| `Parameters[].Required` | boolean | 参数是否必填。 |

### 5.43 查询 RAID 整形当前风险 ActionInfo

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/Reshape/SetCurrentRiskInfoActionInfo` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 实机状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询 RAID 整形当前风险 ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/Reshape/SetCurrentRiskInfoActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询 RAID 整形当前风险 ActionInfo"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/Reshape/SetCurrentRiskInfoActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/Reshape/SetCurrentRiskInfoActionInfo",
  "@odata.type": "#ActionInfo.v1_1_2.ActionInfo",
  "Id": "ActionInfo",
  "Name": "Action Info",
  "Parameters": [
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "AllowableValues": [
        "Linear",
        "Raid0",
        "Raid1",
        "Raid4",
        "Raid5",
        "Raid6",
        "Raid10"
      ],
      "DataType": "String",
      "Name": "Level",
      "Required": true
    }
  ]
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | ActionInfo 资源路径。 |
| `@odata.type` | string | ActionInfo 或消息资源的 OData 类型。 |
| `Id` | string | ActionInfo 资源标识。 |
| `Name` | string | ActionInfo 资源名称。 |
| `Parameters` | array | 操作参数定义列表。 |
| `Parameters[].DisallowedInput` | boolean | 该参数是否禁止输入。 |
| `Parameters[].AllowablePattern` | string | 参数允许的字符串模式；空字符串表示未限制模式。 |
| `Parameters[].AllowableValues` | array | 参数或子参数允许的取值与结构定义。 |
| `Parameters[].DataType` | string | 参数数据类型。 |
| `Parameters[].Name` | string | 参数名称。 |
| `Parameters[].Required` | boolean | 参数是否必填。 |

### 5.44 查询 RAID 整形比较 ActionInfo

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/Reshape/CompareActionInfo` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 实机状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询 RAID 整形比较 ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/Reshape/CompareActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询 RAID 整形比较 ActionInfo"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/Reshape/CompareActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/Reshape/CompareActionInfo",
  "@odata.type": "#ActionInfo.v1_1_2.ActionInfo",
  "Id": "ActionInfo",
  "Name": "Action Info",
  "Parameters": [
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "AllowableValues": [
        "Linear",
        "Raid0",
        "Raid1",
        "Raid4",
        "Raid5",
        "Raid6",
        "Raid10"
      ],
      "DataType": "String",
      "Name": "Level",
      "Required": true
    }
  ]
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | ActionInfo 资源路径。 |
| `@odata.type` | string | ActionInfo 或消息资源的 OData 类型。 |
| `Id` | string | ActionInfo 资源标识。 |
| `Name` | string | ActionInfo 资源名称。 |
| `Parameters` | array | 操作参数定义列表。 |
| `Parameters[].DisallowedInput` | boolean | 该参数是否禁止输入。 |
| `Parameters[].AllowablePattern` | string | 参数允许的字符串模式；空字符串表示未限制模式。 |
| `Parameters[].AllowableValues` | array | 参数或子参数允许的取值与结构定义。 |
| `Parameters[].DataType` | string | 参数数据类型。 |
| `Parameters[].Name` | string | 参数名称。 |
| `Parameters[].Required` | boolean | 参数是否必填。 |

### 5.45 查询 RAID 整形风险 ActionInfo

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/Reshape/RiskActionInfo` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 实机状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询 RAID 整形风险 ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/Reshape/RiskActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询 RAID 整形风险 ActionInfo"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/Reshape/RiskActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/Reshape/RiskActionInfo",
  "@odata.type": "#ActionInfo.v1_1_2.ActionInfo",
  "Id": "ActionInfo",
  "Name": "Action Info",
  "Parameters": [
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "AllowableValues": [
        "Linear",
        "Raid0",
        "Raid1",
        "Raid4",
        "Raid5",
        "Raid6",
        "Raid10"
      ],
      "DataType": "String",
      "Name": "Level",
      "Required": false
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "AllowableValues": [
        "create",
        "expan",
        "change"
      ],
      "DataType": "String",
      "Name": "Ops",
      "Required": true
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "AllowableValues": [
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "String",
          "Name": "BlockName",
          "Required": true
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "Number",
          "Name": "Size",
          "Required": true
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "Boolean",
          "Name": "Rota",
          "Required": true
        }
      ],
      "DataType": "ObjectArray",
      "Name": "Blocks",
      "Required": true
    }
  ]
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | ActionInfo 资源路径。 |
| `@odata.type` | string | ActionInfo 或消息资源的 OData 类型。 |
| `Id` | string | ActionInfo 资源标识。 |
| `Name` | string | ActionInfo 资源名称。 |
| `Parameters` | array | 操作参数定义列表。 |
| `Parameters[].DisallowedInput` | boolean | 该参数是否禁止输入。 |
| `Parameters[].AllowablePattern` | string | 参数允许的字符串模式；空字符串表示未限制模式。 |
| `Parameters[].AllowableValues` | array | 参数或子参数允许的取值与结构定义。 |
| `Parameters[].DataType` | string | 参数数据类型。 |
| `Parameters[].Name` | string | 参数名称。 |
| `Parameters[].Required` | boolean | 参数是否必填。 |
| `Parameters[].AllowableValues[].DisallowedInput` | boolean | 该参数是否禁止输入。 |
| `Parameters[].AllowableValues[].AllowablePattern` | string | 参数允许的字符串模式；空字符串表示未限制模式。 |
| `Parameters[].AllowableValues[].DataType` | string | 参数数据类型。 |
| `Parameters[].AllowableValues[].Name` | string | 参数名称。 |
| `Parameters[].AllowableValues[].Required` | boolean | 参数是否必填。 |

### 5.46 查询执行 RAID 整形 ActionInfo

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/Reshape/ExecActionInfo` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 实机状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询执行 RAID 整形 ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/Reshape/ExecActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询执行 RAID 整形 ActionInfo"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/Reshape/ExecActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/Reshape/ExecActionInfo",
  "@odata.type": "#ActionInfo.v1_1_2.ActionInfo",
  "Id": "ActionInfo",
  "Name": "Action Info",
  "Parameters": [
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "AllowableValues": [
        "Linear",
        "Raid0",
        "Raid1",
        "Raid4",
        "Raid5",
        "Raid6",
        "Raid10"
      ],
      "DataType": "String",
      "Name": "Level",
      "Required": false
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "AllowableValues": [
        "create",
        "expan",
        "change"
      ],
      "DataType": "String",
      "Name": "Ops",
      "Required": true
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "AllowableValues": [
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "String",
          "Name": "BlockName",
          "Required": true
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "Number",
          "Name": "Size",
          "Required": true
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "Boolean",
          "Name": "Rota",
          "Required": true
        }
      ],
      "DataType": "ObjectArray",
      "Name": "Blocks",
      "Required": true
    }
  ]
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | ActionInfo 资源路径。 |
| `@odata.type` | string | ActionInfo 或消息资源的 OData 类型。 |
| `Id` | string | ActionInfo 资源标识。 |
| `Name` | string | ActionInfo 资源名称。 |
| `Parameters` | array | 操作参数定义列表。 |
| `Parameters[].DisallowedInput` | boolean | 该参数是否禁止输入。 |
| `Parameters[].AllowablePattern` | string | 参数允许的字符串模式；空字符串表示未限制模式。 |
| `Parameters[].AllowableValues` | array | 参数或子参数允许的取值与结构定义。 |
| `Parameters[].DataType` | string | 参数数据类型。 |
| `Parameters[].Name` | string | 参数名称。 |
| `Parameters[].Required` | boolean | 参数是否必填。 |
| `Parameters[].AllowableValues[].DisallowedInput` | boolean | 该参数是否禁止输入。 |
| `Parameters[].AllowableValues[].AllowablePattern` | string | 参数允许的字符串模式；空字符串表示未限制模式。 |
| `Parameters[].AllowableValues[].DataType` | string | 参数数据类型。 |
| `Parameters[].AllowableValues[].Name` | string | 参数名称。 |
| `Parameters[].AllowableValues[].Required` | boolean | 参数是否必填。 |

## 6 电源管理资源

### 6.1 查询电源模式参数

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/{SystemId}/Actions/PowerModeSettingActionInfo` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询电源模式参数"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Actions/PowerModeSettingActionInfo'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询电源模式参数"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Actions/PowerModeSettingActionInfo'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Systems/bmc/Actions/PowerModeSettingActionInfo",
  "@odata.type": "#ActionInfo.v1_1_2.ActionInfo",
  "Id": "ActionInfo",
  "Name": "Action Info",
  "Parameters": [
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "AllowableValues": [
        "MaximumPerformance",
        "BalancedPerformance",
        "PowerSaving",
        "Static",
        "OSControlled"
      ],
      "DataType": "String",
      "Name": "PowerMode",
      "Required": true
    }
  ]
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | 电源模式 ActionInfo 资源路径。 |
| `@odata.type` | string | ActionInfo 资源的 OData 类型。 |
| `Id` | string | ActionInfo 资源标识。 |
| `Name` | string | ActionInfo 资源名称。 |
| `Parameters` | array | 电源模式参数定义列表。 |
| `Parameters[].DisallowedInput` | boolean | 该参数是否禁止输入。 |
| `Parameters[].AllowablePattern` | string | 允许的字符串模式。 |
| `Parameters[].AllowableValues` | array | 允许的电源模式列表。 |
| `Parameters[].DataType` | string | 参数数据类型。 |
| `Parameters[].Name` | string | 参数名称，当前为 `PowerMode`。 |
| `Parameters[].Required` | boolean | 参数是否必填。 |

### 6.2 设置电源模式

设置指定系统的 CPU 调频模式。提交前可通过“查询电源模式参数”接口获取当前服务支持的取值。

| 项目 | 内容 |
| --- | --- |
| 方法 | `POST` |
| 路径 | `/redfish/v1/Systems/{SystemId}/Actions/ComputerSystem.PowerMode.Setting` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/Actions/PowerModeSettingActionInfo` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 所需权限 | `OemPowerControl` |
| 成功状态码 | `200 OK` |

#### 请求参数

| 字段 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `PowerMode` | string | 是 | 电源模式。允许值为 `MaximumPerformance`、`BalancedPerformance`、`PowerSaving`、`Static`、`OSControlled`。 |

| 取值 | 说明 |
| --- | --- |
| `MaximumPerformance` | 最大性能模式。服务端映射到 CPU `Performance` governor。 |
| `BalancedPerformance` | 平衡性能模式。服务端映射到 CPU `Interactive` governor。 |
| `PowerSaving` | 节能模式。服务端映射到 CPU `Powersave` governor。 |
| `Static` | 静态模式。服务端映射到 CPU `Userspace` governor。 |
| `OSControlled` | 操作系统控制模式。服务端映射到 CPU `Ondemand` governor。 |

<Callout title="运行状态影响" type="warn">
  该接口会立即修改目标系统的 CPU 调频策略，可能影响性能、功耗和温度。请先确认业务负载，并使用 ActionInfo 返回的受支持值。
</Callout>

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="设置电源模式"
    curl --user '<username>:<password>' \
      --request POST \
      --header 'Content-Type: application/json' \
      --data '{
        "PowerMode": "OSControlled"
      }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Actions/ComputerSystem.PowerMode.Setting'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 设置电源模式"
    curl --request POST \
      --header 'X-Xsrf-Token: <token>' \
      --header 'Content-Type: application/json' \
      --data '{
        "PowerMode": "OSControlled"
      }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Actions/ComputerSystem.PowerMode.Setting'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

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
| `Timestamp` | string | 服务端生成响应时记录的时间戳。 |
| `MessageId` | string | 消息标识，成功时为 `Base.1.11.0.Success`。 |
| `Message` | string | 请求处理结果说明。 |
| `MessageArgs` | array | 消息格式化参数列表；本响应为空数组。 |
| `MessageSeverity` | string | 消息严重级别，成功时为 `OK`。 |
| `Severity` | string | 兼容保留的严重级别字段；本响应为空字符串。 |
| `Oem` | object \| null | OEM 扩展消息；本响应为 `null`。 |
| `RelatedProperties` | array \| null | 与消息相关的属性路径；本响应为 `null`。 |
| `Resolution` | string | 建议的处理方式；成功时为 `None`。 |


### 6.3 查询系统重置参数

查询指定系统支持的重置操作类型。不同系统返回的参数定义可能不同，执行重置前应先查询目标系统的 ActionInfo。

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/{SystemId}/Actions/ResetActionInfo` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询系统重置参数"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/sub01/Actions/ResetActionInfo'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询系统重置参数"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/sub01/Actions/ResetActionInfo'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Systems/sub01/Actions/ResetActionInfo",
  "@odata.type": "#ActionInfo.v1_1_2.ActionInfo",
  "Id": "ActionInfo",
  "Name": "Action Info",
  "Parameters": [
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "AllowableValues": [
        "On",
        "ForceOn",
        "ForceOff",
        "ForceRestart",
        "GracefulRestart",
        "GracefulShutdown",
        "PowerCycle",
        "LoaderByHardware"
      ],
      "DataType": "String",
      "Name": "ResetType",
      "Required": true
    }
  ]
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | 系统重置 ActionInfo 资源路径。 |
| `@odata.type` | string | ActionInfo 资源的 OData 类型。 |
| `Id` | string | ActionInfo 资源标识。 |
| `Name` | string | ActionInfo 资源名称。 |
| `Parameters` | array | 重置参数定义列表。 |
| `Parameters[].DisallowedInput` | boolean | 该参数是否禁止输入。 |
| `Parameters[].AllowablePattern` | string | 允许的字符串模式；空字符串表示未限制模式。 |
| `Parameters[].AllowableValues` | array | 目标系统支持的重置类型列表。 |
| `Parameters[].DataType` | string | 参数数据类型。 |
| `Parameters[].Name` | string | 请求字段名称，子板系统当前为 `ResetType`。 |
| `Parameters[].Required` | boolean | 参数是否必填。 |

### 6.4 重置系统

按照 `ResetType` 对指定系统执行上电、关机、重启或电源循环操作。

| 项目 | 内容 |
| --- | --- |
| 方法 | `POST` |
| 路径 | `/redfish/v1/Systems/{SystemId}/Actions/ComputerSystem.Reset` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/Actions/ResetActionInfo` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 所需权限 | `OemPowerControl` |
| 成功状态码 | `200 OK` |

#### 请求参数

| 字段 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `ResetType` | string | 是 | 子板系统的重置类型。实际允许值以目标系统的 Reset ActionInfo 为准。 |

| 取值 | 说明 |
| --- | --- |
| `On` | 将已关闭的系统上电；系统已经开启时通常保持当前状态。 |
| `ForceOn` | 强制系统上电。 |
| `ForceOff` | 强制系统断电，可能导致未保存数据丢失。 |
| `ForceRestart` | 强制重新启动系统。 |
| `GracefulRestart` | 请求操作系统正常重新启动。 |
| `GracefulShutdown` | 请求操作系统正常关闭。 |
| `PowerCycle` | 先断电再重新上电。 |
| `LoaderByHardware` | 通过硬件方式进入 Loader。 |

<Callout title="业务中断风险" type="warn">
  `ForceOff`、`ForceRestart`、`GracefulRestart`、`GracefulShutdown`、`PowerCycle` 和 `LoaderByHardware` 可能中断业务。执行前应确认目标 `SystemId`、保存业务数据，并预留恢复窗口。
</Callout>

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="将系统置为上电状态"
    curl --user '<username>:<password>' \
      --request POST \
      --header 'Content-Type: application/json' \
      --data '{
        "ResetType": "On"
      }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/<system-id>/Actions/ComputerSystem.Reset'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 将系统置为上电状态"
    curl --request POST \
      --header 'X-Xsrf-Token: <token>' \
      --header 'Content-Type: application/json' \
      --data '{
        "ResetType": "On"
      }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/<system-id>/Actions/ComputerSystem.Reset'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

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
| `Timestamp` | string | 服务端生成响应时记录的时间戳。 |
| `MessageId` | string | 消息标识，成功时为 `Base.1.11.0.Success`。 |
| `Message` | string | 请求处理结果说明。 |
| `MessageArgs` | array | 消息格式化参数列表；本响应为空数组。 |
| `MessageSeverity` | string | 消息严重级别，成功时为 `OK`。 |
| `Severity` | string | 兼容保留的严重级别字段；本响应为空字符串。 |
| `Oem` | object \| null | OEM 扩展消息；本响应为 `null`。 |
| `RelatedProperties` | array \| null | 与消息相关的属性路径；本响应为 `null`。 |
| `Resolution` | string | 建议的处理方式；成功时为 `None`。 |

<Callout title="BMC 目标的参数差异" type="info">
  当前服务端对 `bmc` 返回的 Reset ActionInfo 参数名是 `GracefulRestart`，而不是子板使用的 `ResetType`。调用 `bmc` 时应以其 ActionInfo 为准，提交 `{"GracefulRestart":"GracefulRestart"}`；服务端会返回成功消息，但实现中不会真正重启 BMC。直接向 `bmc` 提交 `ResetType` 会返回 `400 PropertyUnknown`。
</Callout>


### 6.5 查询上电策略参数

查询设置上电策略接口的请求参数、必填状态和允许值。

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/{SystemId}/Actions/SetPowerConfig.ActionInfo` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询上电策略参数"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/sub01/Actions/SetPowerConfig.ActionInfo'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询上电策略参数"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/sub01/Actions/SetPowerConfig.ActionInfo'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Systems/sub01/Actions/SetPowerConfig.ActionInfo",
  "@odata.type": "#ActionInfo.v1_1_2.ActionInfo",
  "Id": "ActionInfo",
  "Name": "Action Info",
  "Parameters": [
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "DataType": "Number",
      "Name": "PowerOnDelaySeconds",
      "Required": false
    },
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "AllowableValues": [
        "AlwaysOn",
        "AlwaysOff",
        "LastState"
      ],
      "DataType": "String",
      "Name": "PowerRestorePolicy",
      "Required": true
    }
  ]
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | 上电策略 ActionInfo 资源路径。 |
| `@odata.type` | string | ActionInfo 资源的 OData 类型。 |
| `Id` | string | ActionInfo 资源标识。 |
| `Name` | string | ActionInfo 资源名称。 |
| `Parameters` | array | 上电策略参数定义列表。 |
| `Parameters[].DisallowedInput` | boolean | 该参数是否禁止输入。 |
| `Parameters[].AllowablePattern` | string | 允许的字符串模式；空字符串表示未限制模式。 |
| `Parameters[].AllowableValues` | array | 字符串参数允许的取值列表；仅 `PowerRestorePolicy` 返回该字段。 |
| `Parameters[].DataType` | string | 参数数据类型，可能为 `Number` 或 `String`。 |
| `Parameters[].Name` | string | 请求字段名称。 |
| `Parameters[].Required` | boolean | 参数是否必填。 |

### 6.6 设置上电策略

设置子板在电源恢复后的启动策略及上电延迟时间。

| 项目 | 内容 |
| --- | --- |
| 方法 | `POST` |
| 路径 | `/redfish/v1/Systems/{SystemId}/Actions/SetPowerConfig` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/Actions/SetPowerConfig.ActionInfo` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 所需权限 | `OemPowerControl` |
| 成功状态码 | `200 OK` |

#### 请求参数

| 字段 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `PowerRestorePolicy` | string | 是 | 电源恢复策略。允许值为 `AlwaysOn`、`AlwaysOff`、`LastState`。 |
| `PowerOnDelaySeconds` | integer | 否 | 电源恢复后延迟上电的秒数。不传时 Go 服务端按整数零值 `0` 处理。 |

| `PowerRestorePolicy` 取值 | 说明 |
| --- | --- |
| `AlwaysOn` | 电源恢复后自动启动子板。 |
| `AlwaysOff` | 电源恢复后保持关机。 |
| `LastState` | 电源恢复后恢复断电前的电源状态。 |

<Callout title="配置影响" type="warn">
  该接口会写入子板运行时电源配置，并影响后续掉电恢复行为。建议先查询系统资源中的 `PowerRestorePolicy` 和 `PowerOnDelaySeconds`，确认目标 `SystemId` 后再提交。`bmc` 不支持该操作。
</Callout>

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="设置上电策略"
    curl --user '<username>:<password>' \
      --request POST \
      --header 'Content-Type: application/json' \
      --data '{
        "PowerRestorePolicy": "AlwaysOn",
        "PowerOnDelaySeconds": 0
      }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/<system-id>/Actions/SetPowerConfig'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 设置上电策略"
    curl --request POST \
      --header 'X-Xsrf-Token: <token>' \
      --header 'Content-Type: application/json' \
      --data '{
        "PowerRestorePolicy": "AlwaysOn",
        "PowerOnDelaySeconds": 0
      }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/<system-id>/Actions/SetPowerConfig'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

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
| `Timestamp` | string | 服务端生成响应时记录的时间戳。 |
| `MessageId` | string | 消息标识，成功时为 `Base.1.11.0.Success`。 |
| `Message` | string | 请求处理结果说明。 |
| `MessageArgs` | array | 消息格式化参数列表；本响应为空数组。 |
| `MessageSeverity` | string | 消息严重级别，成功时为 `OK`。 |
| `Severity` | string | 兼容保留的严重级别字段；本响应为空字符串。 |
| `Oem` | object \| null | OEM 扩展消息；本响应为 `null`。 |
| `RelatedProperties` | array \| null | 与消息相关的属性路径；本响应为 `null`。 |
| `Resolution` | string | 建议的处理方式；成功时为 `None`。 |


## 7 防火墙管理资源

本节说明 BMC 的 OEM 端口映射资源。端口映射支持端口重定向、端口转发和 IP 映射，并提供连通性测试操作。

<Callout title="仅适用于 BMC" type="info">
  本节接口统一使用系统标识 `bmc`。端口映射测试操作的服务端实现也会拒绝非 BMC 系统。
</Callout>

### 7.1 查询端口映射

查询 BMC 当前保存的全部端口映射规则，以及配置和测试操作的入口。

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/bmc/Oem/Firefly/PortMappings` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询端口映射"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Oem/Firefly/PortMappings'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询端口映射"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Oem/Firefly/PortMappings'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Systems/bmc/Oem/Firefly/PortMappings",
  "@odata.type": "#PortMappings.v1_9_0.PortMappings",
  "Id": "PortMappings",
  "Name": "PortMappings",
  "Actions": {
    "#PortMappings.Configure": {
      "target": "/redfish/v1/Systems/bmc/Oem/Firefly/PortMappings/Actions/Configure",
      "@Redfish.ActionInfo": "/redfish/v1/Systems/bmc/Oem/Firefly/PortMappings/Actions/ConfigureActionInfo"
    },
    "#PortMappings.Testing": {
      "target": "/redfish/v1/Systems/bmc/Oem/Firefly/PortMappings/Actions/Testing",
      "@Redfish.ActionInfo": "/redfish/v1/Systems/bmc/Oem/Firefly/PortMappings/Actions/TestingActionInfo"
    }
  },
  "Oem": {
    "Firefly": {
      "PortMappings": [
        {
          "external_ip": "",
          "external_port": 88,
          "protocol": "tcp",
          "internal_ip": "172.168.1.1",
          "internal_port": 8080,
          "service_name": "hjkjhdkjsahdkjasdhkasjdhas",
          "status": true,
          "RedirectUrl": ""
        },
        {
          "external_ip": "",
          "external_port": 8080,
          "protocol": "tcp",
          "internal_ip": "172.168.0.10",
          "internal_port": 8080,
          "service_name": "21",
          "status": false,
          "RedirectUrl": ""
        },
        {
          "external_ip": "",
          "external_port": 1,
          "protocol": "tcp",
          "internal_ip": "172.168.1.10",
          "internal_port": 8080,
          "service_name": "2",
          "status": false,
          "RedirectUrl": ""
        }
      ]
    }
  }
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | 端口映射资源路径。 |
| `@odata.type` | string | 端口映射资源的 OData 类型。 |
| `Id` | string | 资源标识。 |
| `Name` | string | 资源名称。 |
| `Actions` | object | 资源支持的操作集合。 |
| `Actions.#PortMappings.Configure` | object | 配置端口映射操作。 |
| `Actions.#PortMappings.Configure.target` | string | 配置操作的 POST 路径。 |
| `Actions.#PortMappings.Configure.@Redfish.ActionInfo` | string | 配置操作的 ActionInfo 路径。 |
| `Actions.#PortMappings.Testing` | object | 测试端口映射操作。 |
| `Actions.#PortMappings.Testing.target` | string | 测试操作的 POST 路径。 |
| `Actions.#PortMappings.Testing.@Redfish.ActionInfo` | string | 测试操作的 ActionInfo 路径。 |
| `Oem` | object | OEM 扩展对象。 |
| `Oem.Firefly` | object | Firefly OEM 扩展对象。 |
| `Oem.Firefly.PortMappings` | array | 当前保存的端口映射规则列表。 |
| `Oem.Firefly.PortMappings[].external_ip` | string | 外部 IP；端口映射或本机重定向场景可为空。 |
| `Oem.Firefly.PortMappings[].external_port` | integer | 外部端口，范围为 `0` 至 `65535`。 |
| `Oem.Firefly.PortMappings[].protocol` | string | 协议类型，取值为 `tcp`、`udp` 或 IP 映射场景下的 `all`。 |
| `Oem.Firefly.PortMappings[].internal_ip` | string | 内部目标 IP；本机端口重定向场景可为空。 |
| `Oem.Firefly.PortMappings[].internal_port` | integer | 内部目标端口，范围为 `0` 至 `65535`。 |
| `Oem.Firefly.PortMappings[].service_name` | string | 规则名称，也是规则标识。 |
| `Oem.Firefly.PortMappings[].status` | boolean | 是否启用规则。`false` 表示保存但不生成转发规则。 |
| `Oem.Firefly.PortMappings[].RedirectUrl` | string | 可选的重定向 URL 字段。字段名首字母为大写。 |

### 7.2 查询配置端口映射参数

查询配置操作的请求结构、字段类型、必填状态和协议允许值。

| 项目 | 内容 |
| --- | --- |
| 方法 | `GET` |
| 路径 | `/redfish/v1/Systems/bmc/Oem/Firefly/PortMappings/Actions/ConfigureActionInfo` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 成功状态码 | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="查询配置端口映射参数"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Oem/Firefly/PortMappings/Actions/ConfigureActionInfo'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 查询配置端口映射参数"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Oem/Firefly/PortMappings/Actions/ConfigureActionInfo'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### 响应示例

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Systems/bmc/Oem/Firefly/PortMappings/Actions/ConfigureActionInfo",
  "@odata.type": "#ActionInfo.v1_1_2.ActionInfo",
  "Id": "ActionInfo",
  "Name": "Action Info",
  "Parameters": [
    {
      "DisallowedInput": false,
      "AllowablePattern": "",
      "AllowableValues": [
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "String",
          "Name": "external_ip",
          "Required": false
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "Number",
          "Name": "external_port",
          "Required": false
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "AllowableValues": ["tcp", "udp", "all"],
          "DataType": "String",
          "Name": "protocol",
          "Required": true
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "String",
          "Name": "internal_ip",
          "Required": false
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "Number",
          "Name": "internal_port",
          "Required": false
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "String",
          "Name": "service_name",
          "Required": true
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "Boolean",
          "Name": "status",
          "Required": true
        },
        {
          "DisallowedInput": false,
          "AllowablePattern": "",
          "DataType": "String",
          "Name": "RedirectUrl",
          "Required": false
        }
      ],
      "DataType": "ObjectArray",
      "Name": "PortMappings",
      "Required": true
    }
  ]
}
```

#### 响应字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `@odata.id` | string | 配置操作 ActionInfo 资源路径。 |
| `@odata.type` | string | ActionInfo 资源的 OData 类型。 |
| `Id` | string | ActionInfo 资源标识。 |
| `Name` | string | ActionInfo 资源名称。 |
| `Parameters` | array | 操作参数定义列表。 |
| `Parameters[].DisallowedInput` | boolean | 主参数是否禁止输入。 |
| `Parameters[].AllowablePattern` | string | 主参数允许的输入模式。 |
| `Parameters[].AllowableValues` | array | `PortMappings` 数组元素的字段定义列表。 |
| `Parameters[].AllowableValues[].DisallowedInput` | boolean | 子字段是否禁止输入。 |
| `Parameters[].AllowableValues[].AllowablePattern` | string | 子字段允许的输入模式。 |
| `Parameters[].AllowableValues[].AllowableValues` | array | 子字段允许值；当前仅 `protocol` 返回 `tcp`、`udp`、`all`。 |
| `Parameters[].AllowableValues[].DataType` | string | 子字段数据类型。 |
| `Parameters[].AllowableValues[].Name` | string | 子字段名称。 |
| `Parameters[].AllowableValues[].Required` | boolean | 子字段是否必填。 |
| `Parameters[].DataType` | string | 主参数数据类型，当前为对象数组 `ObjectArray`。 |
| `Parameters[].Name` | string | 主参数名称，当前为 `PortMappings`。 |
| `Parameters[].Required` | boolean | 主参数是否必填。 |

### 7.3 配置端口映射

用请求中的完整 `PortMappings` 数组替换 BMC 当前端口映射配置。

| 项目 | 内容 |
| --- | --- |
| 方法 | `POST` |
| 路径 | `/redfish/v1/Systems/bmc/Oem/Firefly/PortMappings/Actions/Configure` |
| ActionInfo | `/redfish/v1/Systems/bmc/Oem/Firefly/PortMappings/Actions/ConfigureActionInfo` |
| 认证 | HTTP Basic Auth 或 `X-Xsrf-Token` |
| 所需权限 | `ConfigureComponents` |
| 成功状态码 | `200 OK` |

#### 请求参数

| 字段 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `PortMappings` | array | 是 | 要保存的完整端口映射规则列表。该数组会替换现有列表。 |
| `PortMappings[].external_ip` | string | 否 | 外部 IP。端口转发和端口重定向场景可传空字符串。 |
| `PortMappings[].external_port` | integer | 否 | 外部端口，范围为 `0` 至 `65535`。IP 映射场景使用 `0`。 |
| `PortMappings[].protocol` | string | 是 | 协议。端口映射使用 `tcp` 或 `udp`；只有纯 IP 映射可使用 `all`。 |
| `PortMappings[].internal_ip` | string | 否 | 内部目标 IP。本机端口重定向场景传空字符串。 |
| `PortMappings[].internal_port` | integer | 否 | 内部目标端口，范围为 `0` 至 `65535`。IP 映射场景使用 `0`。 |
| `PortMappings[].service_name` | string | 是 | 非空规则名称。 |
| `PortMappings[].status` | boolean | 是 | 是否启用规则。 |
| `PortMappings[].RedirectUrl` | string | 否 | 可选重定向 URL。注意字段名首字母大写。 |

<Callout title="覆盖式配置" type="warn">
  该接口不是追加单条规则，而是整体替换端口映射列表。提交前必须先查询并保留现有 `Oem.Firefly.PortMappings`，将需要保留的规则一并放入请求。提交空数组会清空全部端口映射配置。
</Callout>

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="配置端口映射"
    curl --user '<username>:<password>' \
      --request POST \
      --header 'Content-Type: application/json' \
      --data '{
        "PortMappings": [
          {
            "external_ip": "",
            "external_port": 8081,
            "protocol": "tcp",
            "internal_ip": "192.168.2.10",
            "internal_port": 22,
            "service_name": "ssh-forward",
            "status": true,
            "RedirectUrl": ""
          }
        ]
      }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Oem/Firefly/PortMappings/Actions/Configure'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="使用 Token 配置端口映射"
    curl --request POST \
      --header 'X-Xsrf-Token: <token>' \
      --header 'Content-Type: application/json' \
      --data '{
        "PortMappings": [
          {
            "external_ip": "",
            "external_port": 8081,
            "protocol": "tcp",
            "internal_ip": "192.168.2.10",
            "internal_port": 22,
            "service_name": "ssh-forward",
            "status": true,
            "RedirectUrl": ""
          }
        ]
      }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Oem/Firefly/PortMappings/Actions/Configure'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

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
| `Timestamp` | string | 服务端生成响应时记录的时间戳。 |
| `MessageId` | string | 消息标识，成功时为 `Base.1.11.0.Success`。 |
| `Message` | string | 请求处理结果说明。 |
| `MessageArgs` | array | 消息格式化参数列表；本响应为空数组。 |
| `MessageSeverity` | string | 消息严重级别，成功时为 `OK`。 |
| `Severity` | string | 兼容保留的严重级别字段；本响应为空字符串。 |
| `Oem` | object \| null | OEM 扩展消息；本响应为 `null`。 |
| `RelatedProperties` | array \| null | 与消息相关的属性路径；本响应为 `null`。 |
| `Resolution` | string | 建议的处理方式；成功时为 `None`。 |
