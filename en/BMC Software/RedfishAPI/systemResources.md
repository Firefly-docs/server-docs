# System Resources

System resources are used to query the system nodes, operating system status, network, time, storage, power, and firewall information of the device.

<Callout title="Authentication and Access Protocol" type="info">
  Plain HTTP requests on this page support HTTP Basic Auth and session tokens. The token is carried in the `X-Xsrf-Token` request header, not in the request body. In the examples, `<protocol>` is `http` or `https` depending on the service configuration.
</Callout>

## 1 System Resource Collection

Query the system resource members on the current device. Clients should obtain the actual `SystemId` from `Members[].@odata.id` instead of assuming a fixed number or naming of system members.

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Request body | None |
| Success status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query the System Resource Collection"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query the System Resource Collection with a Token"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

### Response Example

The following is a real response returned by a test device, with HTTP status code `200 OK`.

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

### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Access path of the system resource collection. |
| `@odata.type` | string | OData type of the system resource collection. |
| `Name` | string | Name of the system resource collection. |
| `Members@odata.count` | number | Number of system resource members. |
| `Members` | array | List of system resource members. |
| `Members[].@odata.id` | string | Access path of a specific system resource. |

## 2 System Status Resources

### 2.1 Querying a Specific System

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/{SystemId}` |
| Path parameter | `SystemId`: obtained from the system resource collection; the example below uses `bmc` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Success status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query the bmc System Status"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query the bmc System Status with a Token"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

The following is an excerpt of the core fields from a real `200 OK` response.

```json title="200 OK · Excerpt of a Real Response"
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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Access path of the system resource. |
| `@odata.type` | string | OData type of the system resource. |
| `Id` | string | System resource identifier. |
| `Name` | string | System resource name. |
| `SystemType` | string | System type. |
| `Description` | string | System resource description. |
| `Status` | object | System status information. |
| `Status.Health` | string | System health status. |
| `Status.HealthRollup` | string | Rolled-up system health status. |
| `Status.State` | string | System operating state. |
| `SerialNumber` | string | System serial number. |
| `ProcessorSummary` | object | Processor summary information. |
| `ProcessorSummary.CoreCount` | number | Total number of processor cores. |
| `ProcessorSummary.Count` | number | Number of physical processors. |
| `ProcessorSummary.LogicalProcessorCount` | number | Number of logical processors. |
| `ProcessorSummary.Model` | string | Processor model. |
| `ProcessorSummary.ThreadingEnabled` | boolean | Whether hardware threads are enabled. |
| `ProcessorSummary.Status` | object | Processor status information. |
| `ProcessorSummary.Status.Health` | string | Processor health status. |
| `ProcessorSummary.Status.HealthRollup` | string | Rolled-up processor health status. |
| `ProcessorSummary.Status.State` | string | Processor operating state. |
| `HostingRoles` | string | Hosting role of the system. |
| `HostName` | string | System host name. |
| `Manufacturer` | string | System manufacturer. |
| `ManufacturingMode` | boolean | Whether the system is in manufacturing mode. |
| `UUID` | string | Globally unique identifier of the system. |

### 2.2 Other System Status APIs

| Method | Path | Description | Verified on Device |
| --- | --- | --- | --- |
| `GET` | `/redfish/v1/Systems/{SystemId}/Oem/Firefly/Status` | Queries the operating system, kernel, SoC, and BMC software status. | `200 OK` |
| `GET` | `/redfish/v1/Systems/{SystemId}/Oem/Firefly/ProductSpecifications` | Queries memory, storage, and SoC product specifications. | `200 OK` |

## 3 Network Management Resources

### 3.1 Querying the Network Interface Resource Collection

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/{SystemId}/EthernetInterfaces` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Success status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query the Network Interface Resource Collection"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/EthernetInterfaces'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query the Network Interface Resource Collection with a Token"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/EthernetInterfaces'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Access path of the network interface resource collection. |
| `@odata.type` | string | OData type of the network interface resource collection. |
| `Name` | string | Name of the network interface resource collection. |
| `Members@odata.count` | number | Number of network interfaces. |
| `Members` | array | List of network interface resource members. |
| `Members[].@odata.id` | string | Access path of a specific network interface. |

### 3.2 Querying a Specific Network Interface

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/{SystemId}/EthernetInterfaces/{EthernetInterfaceId}` |
| Path parameter | `EthernetInterfaceId`: obtained from the network interface resource collection; the example below uses the non-management network interface `nic0` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Success status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="Query the Non-Management Network Interface nic0"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/EthernetInterfaces/nic0'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="Query the Non-Management Network Interface nic0 with a Token"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/EthernetInterfaces/nic0'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

```json title="200 OK · Excerpt of a Real Response"
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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Access path of the network interface resource. |
| `@odata.type` | string | OData type of the network interface resource. |
| `AutoNeg` | boolean | Whether auto-negotiation is enabled. |
| `DHCPv4` | boolean | Whether the address is obtained through DHCPv4. |
| `EthernetInterfaceType` | string | Network interface type. |
| `FQDN` | string | Fully qualified domain name. |
| `FullDuplex` | string | Duplex mode. |
| `HostName` | string | Host name. |
| `IPv4Addresses` | array | List of IPv4 addresses. |
| `IPv4Addresses[].Address` | string | IPv4 address. |
| `IPv4Addresses[].SubnetMask` | string | IPv4 subnet mask. |
| `IPv4Addresses[].PrefixLen` | number | IPv4 prefix length. |
| `IPv4Addresses[].AddressOrigin` | string | IPv4 address origin. |
| `Id` | string | Network interface identifier. |
| `InterfaceEnabled` | boolean | Whether the network interface is enabled. |
| `LinkStatus` | string | Network interface link status. |
| `MACAddress` | string | MAC address. |
| `MTUSize` | string | Maximum transmission unit. |
| `Name` | string | Network interface name. |
| `NameServers` | array | List of DNS servers. |
| `Routes` | array | List of static routes. |
| `Speed` | string | Network interface speed in Mbps. |

### 3.3 Querying the Bond Resource Collection

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/{SystemId}/NetworkBondings` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Success status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="Query the Bond Resource Collection"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkBondings'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="Query the Bond Resource Collection with a Token"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkBondings'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Path of the Bond resource collection. |
| `@odata.type` | string | OData type of the Bond resource collection. |
| `Name` | string | Name of the Bond resource collection. |
| `Members@odata.count` | number | Number of Bonds. |
| `Members` | array | List of Bond members. |
| `Members[].@odata.id` | string | Access path of a specific Bond. |

### 3.4 Querying a Specific Bond

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/{SystemId}/NetworkBondings/{NetworkBondingId}` |
| Path parameter | `NetworkBondingId`: obtained from the Bond resource collection; the example below uses `bond1` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Success status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="Query bond1"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkBondings/bond1'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="Query bond1 with a Token"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkBondings/bond1'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

```json title="200 OK · Excerpt of a Real Response"
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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Path of the Bond resource. |
| `@odata.type` | string | OData type of the Bond resource. |
| `Id` | string | Bond identifier. |
| `Name` | string | Bond name. |
| `IPv4Addresses` | array | List of IPv4 addresses of the Bond. |
| `MACAddress` | string | MAC address of the Bond. |
| `LinkStatus` | string | Bond link status. |
| `Oem` | object | OEM extension information. |
| `Oem.Firefly` | object | Firefly Bond extension information. |
| `Oem.Firefly.Mode` | string | Bond mode. |
| `Oem.Firefly.Interfaces` | array | Physical network interfaces included in the Bond. |
| `Oem.Firefly.MIIStatus` | string | MII link status. |
| `Oem.Firefly.SettingEnabled` | boolean | Whether configuration is allowed. |
| `Oem.Firefly.Bonding` | boolean | Whether this is a Bond interface. |
| `Oem.Firefly.BondingMaster` | string | Name of the Bond primary interface. |

### 3.5 Querying the VLAN Resource Collection

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/{SystemId}/NetworkVLANs` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Success status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="Query the VLAN Resource Collection"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkVLANs'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="Query the VLAN Resource Collection with a Token"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkVLANs'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Path of the VLAN resource collection. |
| `@odata.type` | string | OData type of the VLAN resource collection. |
| `Name` | string | Name of the VLAN resource collection. |
| `Members@odata.count` | number | Number of VLANs. |
| `Members` | array | List of VLAN members. |
| `Members[].@odata.id` | string | Access path of a specific VLAN. |

### 3.6 Querying a Specific VLAN

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/{SystemId}/NetworkVLANs/{NetworkVLANId}` |
| Path parameter | `NetworkVLANId`: obtained from the VLAN resource collection; the example below uses `vlan400` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Success status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="Query vlan400"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkVLANs/vlan400'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="Query vlan400 with a Token"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkVLANs/vlan400'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

The following shows the core fields of the `200 OK` response returned by the real device; `vlan400` is associated with the non-management network interface `nic3`.

```json title="200 OK · Excerpt of a Real Response"
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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Path of the VLAN resource. |
| `@odata.type` | string | OData type of the VLAN resource. |
| `Id` | string | VLAN resource identifier. |
| `Name` | string | VLAN name. |
| `DHCPv4` | boolean | Whether the address is obtained through DHCPv4. |
| `EthernetInterfaceType` | string | Interface type; for a VLAN this is a virtual interface. |
| `IPv4Addresses` | array | List of IPv4 addresses of the VLAN. |
| `InterfaceEnabled` | boolean | Whether the VLAN interface is enabled. |
| `LinkStatus` | string | VLAN link status. |
| `MACAddress` | string | MAC address of the VLAN interface. |
| `MTUSize` | string | Maximum transmission unit. |
| `NameServers` | array | List of DNS servers. |
| `Routes` | array | List of static routes. |
| `Speed` | string | Interface speed in Mbps. |
| `Oem` | object | OEM extension information. |
| `Oem.Firefly` | object | Firefly VLAN extension information. |
| `Oem.Firefly.VlanId` | string | IEEE 802.1Q VLAN ID. |
| `Oem.Firefly.VlanLink` | string | Underlying network interface the VLAN attaches to. |
| `Oem.Firefly.SettingEnabled` | boolean | Whether configuring this VLAN is allowed. |
| `Oem.Firefly.HaveConfig` | boolean | Whether the backend has detected a persisted configuration. |
| `Oem.Firefly.NicType` | number | Network interface type number defined by the backend. |

### 3.7 Configuring a Non-Management Network Interface

<Callout title="Do Not Modify the Management Network Interface Directly" type="warn">
  Before configuring, query the network interface resources to confirm that the current management IP is not on the target network interface. Testing on the real device confirmed that the management address is on `MGMT`, so the test was performed only on `nic1`.
</Callout>

| Item | Content |
| --- | --- |
| Method | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/EthernetInterfaces/{EthernetInterfaceId}/Actions/Oem/Firefly/ConfigureActionInfo` |
| Path | `/redfish/v1/Systems/{SystemId}/EthernetInterfaces/{EthernetInterfaceId}/Actions/Oem/Firefly/EthernetInterface.Configure` |
| Privilege | `OemConfigureNetwork` |

| Request Field | Type | Required | Description |
| --- | --- | --- | --- |
| `IPv4Addresses` | object | Yes | IPv4 configuration object. |
| `IPv4Addresses.Dhcp4` | boolean | No | Whether DHCPv4 is enabled. |
| `IPv4Addresses.Addresses` | object[] | No | List of static IPv4 addresses. |
| `IPv4Addresses.Addresses[].Address` | string | Yes | IPv4 address. |
| `IPv4Addresses.Addresses[].PrefixLen` | number | Yes | Network prefix length. |
| `IPv4Addresses.Gateway4` | string | No | Default gateway address. |
| `IPv4Addresses.Gateway4Priority` | number | No | Default gateway priority. |
| `IPv4Addresses.Optional` | boolean | No | Whether the interface is allowed to be not ready at startup. |
| `IPv4Addresses.NameServers` | string[] | No | List of DNS servers. |
| `Routes` | object[] | No | List of static routes. |
| `Routes[].To` | string | Yes | Route destination network segment. |
| `Routes[].Via` | string | Yes | Next-hop address. |
| `Routes[].Metric` | number | No | Route metric. |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="Configure the Non-Management Network Interface nic1"
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
    ```bash title="Configure the Non-Management Network Interface nic1 with a Token"
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
| `Timestamp` | string | Timestamp recorded by the server when generating the response. |
| `MessageId` | string | Message identifier; `Base.1.11.0.Success` on success. |
| `Message` | string | Description of the request processing result. |
| `MessageArgs` | array | List of message formatting arguments; an empty array in this response. |
| `MessageSeverity` | string | Message severity; `OK` on success. |
| `Severity` | string | Severity field kept for compatibility; an empty string in this response. |
| `Oem` | object \| null | OEM extension message; `null` in this response. |
| `RelatedProperties` | array \| null | Property paths related to the message; `null` in this response. |
| `Resolution` | string | Recommended resolution; `None` on success. |

<Callout title="A Success Message Does Not Mean the Running State Has Changed" type="info">
  That POST returned `200 OK`, but a subsequent GET of `nic1` still showed `IPv4Addresses` as an empty array. Clients must query the resource again after configuration and must not assume the configuration took effect based on the success message alone.
</Callout>

### 3.8 Configuring a Bond

| Item | Content |
| --- | --- |
| Method | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/NetworkBondings/{NetworkBondingId}/Actions/Oem/Firefly/ConfigureActionInfo` |
| Path | `/redfish/v1/Systems/{SystemId}/NetworkBondings/{NetworkBondingId}/Actions/Oem/Firefly/NetworkBonding.Configure` |
| Test object | `bond1`, with the non-management network interface `nic2` as its member |
| Success status code | `200 OK` |

| Request Field | Type | Required | Description |
| --- | --- | --- | --- |
| `Mode` | string | Yes | Bond mode: `802.3ad`, `active-backup`, `balance-alb`, `balance-rr`, `balance-tlb`, `balance-xor`, or `broadcast`. |
| `Interfaces` | string[] | Yes | Physical network interface members of the Bond; must not include the network interface that carries the management address. |
| `IPv4Addresses` | object | Yes | IPv4 configuration. |
| `IPv4Addresses.Dhcp4` | boolean | No | Whether DHCPv4 is enabled. |
| `IPv4Addresses.Addresses` | object[] | No | List of static IPv4 addresses. |
| `IPv4Addresses.Addresses[].Address` | string | Yes | IPv4 address. |
| `IPv4Addresses.Addresses[].PrefixLen` | number | Yes | Network prefix length. |
| `IPv4Addresses.Gateway4` | string | No | Default gateway. |
| `IPv4Addresses.Gateway4Priority` | number | No | Default gateway priority. |
| `IPv4Addresses.Optional` | boolean | No | Whether the interface is allowed to be not ready at startup. |
| `IPv4Addresses.NameServers` | string[] | No | DNS servers. |
| `Routes` | object[] | No | List of static routes; each entry supports `To`, `Via`, and the optional `Metric`. |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="Configure bond1"
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
    ```bash title="Configure bond1 with a Token"
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
| `Timestamp` | string | Timestamp recorded by the server when generating the response. |
| `MessageId` | string | Message identifier; `Base.1.11.0.Success` on success. |
| `Message` | string | Description of the request processing result. |
| `MessageArgs` | array | List of message formatting arguments; an empty array in this response. |
| `MessageSeverity` | string | Message severity; `OK` on success. |
| `Severity` | string | Severity field kept for compatibility; an empty string in this response. |
| `Oem` | object \| null | OEM extension message; `null` in this response. |
| `RelatedProperties` | array \| null | Property paths related to the message; `null` in this response. |
| `Resolution` | string | Recommended resolution; `None` on success. |

After writing the original configuration of `bond1` back to the real device, a follow-up GET confirms that its mode is still `802.3ad` and its member is still `nic2`.

### 3.9 Adding a Bond

| Item | Content |
| --- | --- |
| Method | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/NetworkBondings/Actions/Oem/Firefly/AddConfigureActionInfo` |
| Path | `/redfish/v1/Systems/{SystemId}/NetworkBondings/Actions/Oem/Firefly/NetworkBondingAdd.Configure` |
| New field | `Device`: Bond name; the other fields are the same as those of "Configuring a Bond" |
| Test object | Temporary `bond9`, with the non-management network interface `nic1` as its member |
| Success status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="Add bond9"
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
    ```bash title="Add bond9 with a Token"
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
| `Timestamp` | string | Timestamp recorded by the server when generating the response. |
| `MessageId` | string | Message identifier; `Base.1.11.0.Success` on success. |
| `Message` | string | Description of the request processing result. |
| `MessageArgs` | array | List of message formatting arguments; an empty array in this response. |
| `MessageSeverity` | string | Message severity; `OK` on success. |
| `Severity` | string | Severity field kept for compatibility; an empty string in this response. |
| `Oem` | object \| null | OEM extension message; `null` in this response. |
| `RelatedProperties` | array \| null | Property paths related to the message; `null` in this response. |
| `Resolution` | string | Recommended resolution; `None` on success. |

<Callout title="You Must Query to Confirm the Add Result" type="warn">
  Although the real device returned a success message, `bond9` did not appear when the Bond collection was queried afterwards. Callers must not conclude from `200 OK` alone that the Bond was created; they must GET the resource collection again to confirm.
</Callout>

### 3.10 Deleting a Bond

| Item | Content |
| --- | --- |
| Method | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/NetworkBondings/Actions/Oem/Firefly/DelConfigureActionInfo` |
| Path | `/redfish/v1/Systems/{SystemId}/NetworkBondings/Actions/Oem/Firefly/NetworkBondingDel.Configure` |
| Request field | `Device`: name of the Bond to delete |
| Test object | Temporary `bond9` |
| Success status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="Delete bond9"
    curl --request POST \
      --user '<username>:<password>' \
      --header 'Content-Type: application/json' \
      --data '{ "Device": "bond9" }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkBondings/Actions/Oem/Firefly/NetworkBondingDel.Configure'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="Delete bond9 with a Token"
    curl --request POST \
      --header 'X-Xsrf-Token: <token>' \
      --header 'Content-Type: application/json' \
      --data '{ "Device": "bond9" }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkBondings/Actions/Oem/Firefly/NetworkBondingDel.Configure'
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
| `Timestamp` | string | Timestamp recorded by the server when generating the response. |
| `MessageId` | string | Message identifier; `Base.1.11.0.Success` on success. |
| `Message` | string | Description of the request processing result. |
| `MessageArgs` | array | List of message formatting arguments; an empty array in this response. |
| `MessageSeverity` | string | Message severity; `OK` on success. |
| `Severity` | string | Severity field kept for compatibility; an empty string in this response. |
| `Oem` | object \| null | OEM extension message; `null` in this response. |
| `RelatedProperties` | array \| null | Property paths related to the message; `null` in this response. |
| `Resolution` | string | Recommended resolution; `None` on success. |

After the deletion, querying the Bond collection again shows that the real device contains only `bond0` and `bond1`.

### 3.11 Configuring a VLAN

| Item | Content |
| --- | --- |
| Method | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/NetworkVLANs/{NetworkVLANId}/Actions/Oem/Firefly/ConfigureActionInfo` |
| Path | `/redfish/v1/Systems/{SystemId}/NetworkVLANs/{NetworkVLANId}/Actions/Oem/Firefly/NetworkVLAN.Configure` |
| Test object | `vlan400`, with the non-management network interface `nic3` as the underlying interface |
| Success status code | `200 OK` |

| Request Field | Type | Required | Description |
| --- | --- | --- | --- |
| `Id` | number | Yes | VLAN ID; the value must be within the IEEE 802.1Q range. |
| `Link` | string | Yes | Underlying network interface the VLAN attaches to; do not use the network interface that carries the management address. |
| `IPv4Addresses` | object | No | IPv4 configuration; the sub-fields are the same as those for network interface configuration. |
| `Routes` | object[] | No | List of static routes. |

<Callout title="Do Not Pass Device to the Configuration API" type="warn">
  When configuring an existing VLAN, the backend determines the device name from `{NetworkVLANId}` in the URL. When the real-device request body explicitly passed `Device`, the response was `400 Base.1.11.0.PropertyUnknown`.
</Callout>

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="Configure vlan400"
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
    ```bash title="Configure vlan400 with a Token"
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
| `Timestamp` | string | Timestamp recorded by the server when generating the response. |
| `MessageId` | string | Message identifier; `Base.1.11.0.Success` on success. |
| `Message` | string | Description of the request processing result. |
| `MessageArgs` | array | List of message formatting arguments; an empty array in this response. |
| `MessageSeverity` | string | Message severity; `OK` on success. |
| `Severity` | string | Severity field kept for compatibility; an empty string in this response. |
| `Oem` | object \| null | OEM extension message; `null` in this response. |
| `RelatedProperties` | array \| null | Property paths related to the message; `null` in this response. |
| `Resolution` | string | Recommended resolution; `None` on success. |

After the configuration, a follow-up GET of `vlan400` shows that the real device still returns `VlanId=400` and `VlanLink=nic3`.

### 3.12 Adding a VLAN

| Item | Content |
| --- | --- |
| Method | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/NetworkVLANs/Actions/Oem/Firefly/AddConfigureActionInfo` |
| Path | `/redfish/v1/Systems/{SystemId}/NetworkVLANs/Actions/Oem/Firefly/NetworkVlANAdd.Configure` |
| Request fields | `Id`, `Link`, and `Device` are required; `IPv4Addresses` and `Routes` are optional |
| Test object | Temporary `vlan4090`, with the non-management network interface `nic1` as the underlying interface |
| Success status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="Add vlan4090"
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
    ```bash title="Add vlan4090 with a Token"
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
| `Timestamp` | string | Timestamp recorded by the server when generating the response. |
| `MessageId` | string | Message identifier; `Base.1.11.0.Success` on success. |
| `Message` | string | Description of the request processing result. |
| `MessageArgs` | array | List of message formatting arguments; an empty array in this response. |
| `MessageSeverity` | string | Message severity; `OK` on success. |
| `Severity` | string | Severity field kept for compatibility; an empty string in this response. |
| `Oem` | object \| null | OEM extension message; `null` in this response. |
| `RelatedProperties` | array \| null | Property paths related to the message; `null` in this response. |
| `Resolution` | string | Recommended resolution; `None` on success. |

<Callout title="You Must Query to Confirm the Add Result" type="warn">
  After the real device returned a success message, `vlan4090` did not appear in the VLAN collection. Callers must query the VLAN collection or the specific VLAN again to confirm whether the configuration actually took effect.
</Callout>

### 3.13 Deleting a VLAN

| Item | Content |
| --- | --- |
| Method | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/NetworkVLANs/Actions/Oem/Firefly/DelConfigureActionInfo` |
| Path | `/redfish/v1/Systems/{SystemId}/NetworkVLANs/Actions/Oem/Firefly/NetworkVLANDel.Configure` |
| Request field | `Device`: name of the VLAN device to delete, required |
| Test object | Temporary `vlan4090` |
| Success status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="Delete vlan4090"
    curl --request POST \
      --user '<username>:<password>' \
      --header 'Content-Type: application/json' \
      --data '{ "Device": "vlan4090" }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkVLANs/Actions/Oem/Firefly/NetworkVLANDel.Configure'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="Delete vlan4090 with a Token"
    curl --request POST \
      --header 'X-Xsrf-Token: <token>' \
      --header 'Content-Type: application/json' \
      --data '{ "Device": "vlan4090" }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkVLANs/Actions/Oem/Firefly/NetworkVLANDel.Configure'
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
| `Timestamp` | string | Timestamp recorded by the server when generating the response. |
| `MessageId` | string | Message identifier; `Base.1.11.0.Success` on success. |
| `Message` | string | Description of the request processing result. |
| `MessageArgs` | array | List of message formatting arguments; an empty array in this response. |
| `MessageSeverity` | string | Message severity; `OK` on success. |
| `Severity` | string | Severity field kept for compatibility; an empty string in this response. |
| `Oem` | object \| null | OEM extension message; `null` in this response. |
| `RelatedProperties` | array \| null | Property paths related to the message; `null` in this response. |
| `Resolution` | string | Recommended resolution; `None` on success. |

After the deletion, querying the VLAN collection again shows that the real device contains only `vlan400` and `vlan4000`.

### 3.14 Querying the Non-Management Network Interface Configuration ActionInfo

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/bmc/EthernetInterfaces/nic1/Actions/Oem/Firefly/ConfigureActionInfo` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Actual status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query the Non-Management Network Interface Configuration ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/EthernetInterfaces/nic1/Actions/Oem/Firefly/ConfigureActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query the Non-Management Network Interface Configuration ActionInfo with a Token"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/EthernetInterfaces/nic1/Actions/Oem/Firefly/ConfigureActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Path of the ActionInfo resource. |
| `@odata.type` | string | OData type of the ActionInfo or message resource. |
| `Id` | string | ActionInfo resource identifier. |
| `Name` | string | ActionInfo resource name. |
| `Parameters` | array | List of operation parameter definitions. |
| `Parameters[].DisallowedInput` | boolean | Whether input is disallowed for this parameter. |
| `Parameters[].AllowablePattern` | string | String pattern allowed for the parameter; an empty string means no pattern restriction. |
| `Parameters[].AllowableValues` | array | Allowed values and structure definitions for a parameter or sub-parameter. |
| `Parameters[].AllowableValues[].DisallowedInput` | boolean | Whether input is disallowed for this parameter. |
| `Parameters[].AllowableValues[].AllowablePattern` | string | String pattern allowed for the parameter; an empty string means no pattern restriction. |
| `Parameters[].AllowableValues[].DataType` | string | Parameter data type. |
| `Parameters[].AllowableValues[].Name` | string | Parameter name. |
| `Parameters[].AllowableValues[].Required` | boolean | Whether the parameter is required. |
| `Parameters[].AllowableValues[].AllowableValues` | array | Allowed values and structure definitions for a parameter or sub-parameter. |
| `Parameters[].AllowableValues[].AllowableValues[].DisallowedInput` | boolean | Whether input is disallowed for this parameter. |
| `Parameters[].AllowableValues[].AllowableValues[].AllowablePattern` | string | String pattern allowed for the parameter; an empty string means no pattern restriction. |
| `Parameters[].AllowableValues[].AllowableValues[].DataType` | string | Parameter data type. |
| `Parameters[].AllowableValues[].AllowableValues[].Name` | string | Parameter name. |
| `Parameters[].AllowableValues[].AllowableValues[].Required` | boolean | Whether the parameter is required. |
| `Parameters[].DataType` | string | Parameter data type. |
| `Parameters[].Name` | string | Parameter name. |
| `Parameters[].Required` | boolean | Whether the parameter is required. |

### 3.15 Querying the Bond Configuration ActionInfo

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/bmc/NetworkBondings/bond1/Actions/Oem/Firefly/ConfigureActionInfo` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Actual status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query the Bond Configuration ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkBondings/bond1/Actions/Oem/Firefly/ConfigureActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query the Bond Configuration ActionInfo with a Token"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkBondings/bond1/Actions/Oem/Firefly/ConfigureActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Path of the ActionInfo resource. |
| `@odata.type` | string | OData type of the ActionInfo or message resource. |
| `Id` | string | ActionInfo resource identifier. |
| `Name` | string | ActionInfo resource name. |
| `Parameters` | array | List of operation parameter definitions. |
| `Parameters[].DisallowedInput` | boolean | Whether input is disallowed for this parameter. |
| `Parameters[].AllowablePattern` | string | String pattern allowed for the parameter; an empty string means no pattern restriction. |
| `Parameters[].AllowableValues` | array | Allowed values and structure definitions for a parameter or sub-parameter. |
| `Parameters[].AllowableValues[].DisallowedInput` | boolean | Whether input is disallowed for this parameter. |
| `Parameters[].AllowableValues[].AllowablePattern` | string | String pattern allowed for the parameter; an empty string means no pattern restriction. |
| `Parameters[].AllowableValues[].DataType` | string | Parameter data type. |
| `Parameters[].AllowableValues[].Name` | string | Parameter name. |
| `Parameters[].AllowableValues[].Required` | boolean | Whether the parameter is required. |
| `Parameters[].AllowableValues[].AllowableValues` | array | Allowed values and structure definitions for a parameter or sub-parameter. |
| `Parameters[].AllowableValues[].AllowableValues[].DisallowedInput` | boolean | Whether input is disallowed for this parameter. |
| `Parameters[].AllowableValues[].AllowableValues[].AllowablePattern` | string | String pattern allowed for the parameter; an empty string means no pattern restriction. |
| `Parameters[].AllowableValues[].AllowableValues[].DataType` | string | Parameter data type. |
| `Parameters[].AllowableValues[].AllowableValues[].Name` | string | Parameter name. |
| `Parameters[].AllowableValues[].AllowableValues[].Required` | boolean | Whether the parameter is required. |
| `Parameters[].DataType` | string | Parameter data type. |
| `Parameters[].Name` | string | Parameter name. |
| `Parameters[].Required` | boolean | Whether the parameter is required. |

### 3.16 Querying the Add Bond ActionInfo

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/bmc/NetworkBondings/Actions/Oem/Firefly/AddConfigureActionInfo` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Actual status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query the Add Bond ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkBondings/Actions/Oem/Firefly/AddConfigureActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query the Add Bond ActionInfo with a Token"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkBondings/Actions/Oem/Firefly/AddConfigureActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Path of the ActionInfo resource. |
| `@odata.type` | string | OData type of the ActionInfo or message resource. |
| `Id` | string | ActionInfo resource identifier. |
| `Name` | string | ActionInfo resource name. |
| `Parameters` | array | List of operation parameter definitions. |
| `Parameters[].DisallowedInput` | boolean | Whether input is disallowed for this parameter. |
| `Parameters[].AllowablePattern` | string | String pattern allowed for the parameter; an empty string means no pattern restriction. |
| `Parameters[].AllowableValues` | array | Allowed values and structure definitions for a parameter or sub-parameter. |
| `Parameters[].AllowableValues[].DisallowedInput` | boolean | Whether input is disallowed for this parameter. |
| `Parameters[].AllowableValues[].AllowablePattern` | string | String pattern allowed for the parameter; an empty string means no pattern restriction. |
| `Parameters[].AllowableValues[].DataType` | string | Parameter data type. |
| `Parameters[].AllowableValues[].Name` | string | Parameter name. |
| `Parameters[].AllowableValues[].Required` | boolean | Whether the parameter is required. |
| `Parameters[].AllowableValues[].AllowableValues` | array | Allowed values and structure definitions for a parameter or sub-parameter. |
| `Parameters[].AllowableValues[].AllowableValues[].DisallowedInput` | boolean | Whether input is disallowed for this parameter. |
| `Parameters[].AllowableValues[].AllowableValues[].AllowablePattern` | string | String pattern allowed for the parameter; an empty string means no pattern restriction. |
| `Parameters[].AllowableValues[].AllowableValues[].DataType` | string | Parameter data type. |
| `Parameters[].AllowableValues[].AllowableValues[].Name` | string | Parameter name. |
| `Parameters[].AllowableValues[].AllowableValues[].Required` | boolean | Whether the parameter is required. |
| `Parameters[].DataType` | string | Parameter data type. |
| `Parameters[].Name` | string | Parameter name. |
| `Parameters[].Required` | boolean | Whether the parameter is required. |

### 3.17 Querying the Delete Bond ActionInfo

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/bmc/NetworkBondings/Actions/Oem/Firefly/DelConfigureActionInfo` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Actual status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query the Delete Bond ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkBondings/Actions/Oem/Firefly/DelConfigureActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query the Delete Bond ActionInfo with a Token"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkBondings/Actions/Oem/Firefly/DelConfigureActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Path of the ActionInfo resource. |
| `@odata.type` | string | OData type of the ActionInfo or message resource. |
| `Id` | string | ActionInfo resource identifier. |
| `Name` | string | ActionInfo resource name. |
| `Parameters` | array | List of operation parameter definitions. |
| `Parameters[].DisallowedInput` | boolean | Whether input is disallowed for this parameter. |
| `Parameters[].AllowablePattern` | string | String pattern allowed for the parameter; an empty string means no pattern restriction. |
| `Parameters[].DataType` | string | Parameter data type. |
| `Parameters[].Name` | string | Parameter name. |
| `Parameters[].Required` | boolean | Whether the parameter is required. |

### 3.18 Querying the VLAN Configuration ActionInfo

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/bmc/NetworkVLANs/vlan400/Actions/Oem/Firefly/ConfigureActionInfo` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Actual status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query the VLAN Configuration ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkVLANs/vlan400/Actions/Oem/Firefly/ConfigureActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query the VLAN Configuration ActionInfo with a Token"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkVLANs/vlan400/Actions/Oem/Firefly/ConfigureActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Path of the ActionInfo resource. |
| `@odata.type` | string | OData type of the ActionInfo or message resource. |
| `Id` | string | ActionInfo resource identifier. |
| `Name` | string | ActionInfo resource name. |
| `Parameters` | array | List of operation parameter definitions. |
| `Parameters[].DisallowedInput` | boolean | Whether input is disallowed for this parameter. |
| `Parameters[].AllowablePattern` | string | String pattern allowed for the parameter; an empty string means no pattern restriction. |
| `Parameters[].AllowableValues` | array | Allowed values and structure definitions for a parameter or sub-parameter. |
| `Parameters[].AllowableValues[].DisallowedInput` | boolean | Whether input is disallowed for this parameter. |
| `Parameters[].AllowableValues[].AllowablePattern` | string | String pattern allowed for the parameter; an empty string means no pattern restriction. |
| `Parameters[].AllowableValues[].DataType` | string | Parameter data type. |
| `Parameters[].AllowableValues[].Name` | string | Parameter name. |
| `Parameters[].AllowableValues[].Required` | boolean | Whether the parameter is required. |
| `Parameters[].AllowableValues[].AllowableValues` | array | Allowed values and structure definitions for a parameter or sub-parameter. |
| `Parameters[].AllowableValues[].AllowableValues[].DisallowedInput` | boolean | Whether input is disallowed for this parameter. |
| `Parameters[].AllowableValues[].AllowableValues[].AllowablePattern` | string | String pattern allowed for the parameter; an empty string means no pattern restriction. |
| `Parameters[].AllowableValues[].AllowableValues[].DataType` | string | Parameter data type. |
| `Parameters[].AllowableValues[].AllowableValues[].Name` | string | Parameter name. |
| `Parameters[].AllowableValues[].AllowableValues[].Required` | boolean | Whether the parameter is required. |
| `Parameters[].DataType` | string | Parameter data type. |
| `Parameters[].Name` | string | Parameter name. |
| `Parameters[].Required` | boolean | Whether the parameter is required. |

### 3.19 Querying the Add VLAN ActionInfo

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/bmc/NetworkVLANs/Actions/Oem/Firefly/AddConfigureActionInfo` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Actual status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query the Add VLAN ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkVLANs/Actions/Oem/Firefly/AddConfigureActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query the Add VLAN ActionInfo with a Token"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkVLANs/Actions/Oem/Firefly/AddConfigureActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Path of the ActionInfo resource. |
| `@odata.type` | string | OData type of the ActionInfo or message resource. |
| `Id` | string | ActionInfo resource identifier. |
| `Name` | string | ActionInfo resource name. |
| `Parameters` | array | List of operation parameter definitions. |
| `Parameters[].DisallowedInput` | boolean | Whether input is disallowed for this parameter. |
| `Parameters[].AllowablePattern` | string | String pattern allowed for the parameter; an empty string means no pattern restriction. |
| `Parameters[].AllowableValues` | array | Allowed values and structure definitions for a parameter or sub-parameter. |
| `Parameters[].AllowableValues[].DisallowedInput` | boolean | Whether input is disallowed for this parameter. |
| `Parameters[].AllowableValues[].AllowablePattern` | string | String pattern allowed for the parameter; an empty string means no pattern restriction. |
| `Parameters[].AllowableValues[].DataType` | string | Parameter data type. |
| `Parameters[].AllowableValues[].Name` | string | Parameter name. |
| `Parameters[].AllowableValues[].Required` | boolean | Whether the parameter is required. |
| `Parameters[].AllowableValues[].AllowableValues` | array | Allowed values and structure definitions for a parameter or sub-parameter. |
| `Parameters[].AllowableValues[].AllowableValues[].DisallowedInput` | boolean | Whether input is disallowed for this parameter. |
| `Parameters[].AllowableValues[].AllowableValues[].AllowablePattern` | string | String pattern allowed for the parameter; an empty string means no pattern restriction. |
| `Parameters[].AllowableValues[].AllowableValues[].DataType` | string | Parameter data type. |
| `Parameters[].AllowableValues[].AllowableValues[].Name` | string | Parameter name. |
| `Parameters[].AllowableValues[].AllowableValues[].Required` | boolean | Whether the parameter is required. |
| `Parameters[].DataType` | string | Parameter data type. |
| `Parameters[].Name` | string | Parameter name. |
| `Parameters[].Required` | boolean | Whether the parameter is required. |

### 3.20 Querying the Delete VLAN ActionInfo

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/bmc/NetworkVLANs/Actions/Oem/Firefly/DelConfigureActionInfo` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Actual status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query the Delete VLAN ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkVLANs/Actions/Oem/Firefly/DelConfigureActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query the Delete VLAN ActionInfo with a Token"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/NetworkVLANs/Actions/Oem/Firefly/DelConfigureActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Path of the ActionInfo resource. |
| `@odata.type` | string | OData type of the ActionInfo or message resource. |
| `Id` | string | ActionInfo resource identifier. |
| `Name` | string | ActionInfo resource name. |
| `Parameters` | array | List of operation parameter definitions. |
| `Parameters[].DisallowedInput` | boolean | Whether input is disallowed for this parameter. |
| `Parameters[].AllowablePattern` | string | String pattern allowed for the parameter; an empty string means no pattern restriction. |
| `Parameters[].DataType` | string | Parameter data type. |
| `Parameters[].Name` | string | Parameter name. |
| `Parameters[].Required` | boolean | Whether the parameter is required. |

## 4 Time Management Resources

### 4.1 Querying the System Time

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/{SystemId}/Oem/Firefly/DateTime` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Success status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query the System Time"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Oem/Firefly/DateTime'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query the System Time with a Token"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Oem/Firefly/DateTime'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Access path of the system time resource. |
| `@odata.type` | string | OData type of the system time resource. |
| `Id` | string | System time resource identifier. |
| `Name` | string | System time resource name. |
| `Actions` | object | Operations available for the system time. |
| `Actions.#DateTime.Configure` | object | Operation definition for setting the system time. |
| `Actions.#DateTime.Configure.target` | string | POST path for setting the system time. |
| `Actions.#DateTime.Configure.@Redfish.ActionInfo` | string | ActionInfo path for setting the time. |
| `DateTime` | string | Current system date and time. |

### 4.2 Querying the Time Zone

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/{SystemId}/Oem/Firefly/TimeZone` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Request body | None |
| Success status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="Query the Time Zone"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Oem/Firefly/TimeZone'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="Query the Time Zone with a Token"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Oem/Firefly/TimeZone'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

The following is a real `200 OK` response returned by a test device.

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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Path of the time zone resource. |
| `@odata.type` | string | OData type of the time zone resource. |
| `Id` | string | Time zone resource identifier. |
| `Name` | string | Time zone resource name. |
| `Actions` | object | Operations supported by the time zone resource. |
| `Actions.#TimeZone.Configure` | object | Operation definition for setting the time zone. |
| `Actions.#TimeZone.Configure.target` | string | POST path for setting the time zone. |
| `Actions.#TimeZone.Configure.@Redfish.ActionInfo` | string | ActionInfo path for setting the time zone. |
| `TimeZone` | string | Current IANA time zone name. |

### 4.3 Querying the Full Time Service Configuration

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/{SystemId}/Oem/Firefly/TimeServiceSetting` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Request body | None |
| Success status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="Query the Full Time Service Configuration"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Oem/Firefly/TimeServiceSetting'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="Query the Full Time Service Configuration with a Token"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Oem/Firefly/TimeServiceSetting'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

The following is a real `200 OK` response returned by a test device.

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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Path of the full time service configuration resource. |
| `@odata.type` | string | OData type of the time service configuration. |
| `Id` | string | Resource identifier. |
| `Name` | string | Resource name. |
| `Actions` | object | Operation definitions provided directly by the current resource. |
| `manual` | boolean | Whether a manually set time is used. |
| `ntp_config` | object | Simplified synchronization configuration of the time service. |
| `ntp_config.preferred` | string | Preferred time server. |
| `ntp_config.alternative` | string | Alternate time server. |
| `ntp_config.rtc` | boolean | Whether to synchronize the hardware clock. |
| `ntp_config.make_step` | object | Time stepping policy. |
| `ntp_config.make_step.threshold` | string | Offset threshold that allows the time to be corrected directly. |
| `ntp_config.make_step.limit` | string | Number of direct corrections allowed after startup. |
| `ntp_config.server_config` | object | Local time server configuration. |
| `ntp_config.server_config.port` | string | Time service port. |
| `ntp_config.server_config.local_stratum` | string | Local time source stratum. |
| `time` | string | Manual time value; may be empty when not in manual mode. |
| `time_zone` | string | GMT time zone used by the time service, for example `GMT+08:00`. The standalone time zone resource may return the corresponding IANA name. |
| `chrony_config` | object | Complete Chrony configuration. |
| `chrony_config.servers` | array | List of upstream time servers. |
| `chrony_config.servers[].addr` | string | Time server address. |
| `chrony_config.servers[].iburst` | boolean | Whether to send synchronization requests quickly at startup. |
| `chrony_config.servers[].minpoll` | string | Minimum polling interval exponent. |
| `chrony_config.servers[].maxpoll` | string | Maximum polling interval exponent. |
| `chrony_config.servers[].port` | string | Time server port. |
| `chrony_config.servers[].nts` | boolean | Whether Network Time Security is enabled. |
| `chrony_config.servers[].burst` | boolean | Whether burst mode is enabled. |
| `chrony_config.servers[].trust` | boolean | Whether this time source is trusted. |
| `chrony_config.log` | object | Chrony logging category configuration. |
| `chrony_config.log.measurements` | boolean | Whether to log measurements. |
| `chrony_config.log.statistics` | boolean | Whether to log statistics. |
| `chrony_config.log.tracking` | boolean | Whether to log tracking. |
| `chrony_config.log.rtc` | boolean | Whether to log RTC messages. |
| `chrony_config.log.refclocks` | boolean | Whether to log reference clocks. |
| `chrony_config.log.tempcomp` | boolean | Whether to log temperature compensation. |
| `chrony_config.log.rawmeasurements` | boolean | Whether to log raw measurements. |
| `chrony_config.log.selection` | boolean | Whether to log time source selection. |
| `chrony_config.makestep` | object | Chrony time stepping policy. |
| `chrony_config.makestep.threshold` | string | Time step threshold. |
| `chrony_config.makestep.limit` | string | Limit on the number of time steps. |
| `chrony_config.allow` | array | Network segments allowed to access the local time service. |
| `chrony_config.deny` | array | Network segments denied access to the local time service. |
| `chrony_config.port` | string | Local time service port. |
| `chrony_config.binddevice` | string[] \| null | Network interfaces bound to Chrony. |
| `chrony_config.local` | object | Local time source configuration. |
| `chrony_config.local.distance` | string | Local time source distance. |
| `chrony_config.local.stratum` | string | Local time source stratum. |
| `chrony_config.local.orphan` | boolean | Whether orphan mode is enabled. |
| `chrony_config.logdir` | string | Chrony log directory. |
| `chrony_config.driftfile` | string | Clock drift data file. |
| `chrony_config.minsources` | string | Minimum number of time sources required for synchronization. |
| `chrony_config.rtcsync` | boolean | Whether the system time is synchronized to the RTC. |
| `chrony_config.maxupdateskew` | string | Maximum estimated skew for which updating the clock is allowed. |
| `chrony_config.maxdistance` | string | Maximum root distance allowed for a time source. |
| `chrony_config.ntsdumpdir` | string | Directory where NTS data is saved. |
| `chrony_config.ntsservercert` | string | Path of the NTS server certificate. |
| `chrony_config.ntsserverkey` | string | Path of the NTS server private key. |
| `chrony_config.ntstrustedcerts` | string | NTS trusted certificate configuration. |

### 4.4 Setting the System Time

| Item | Content |
| --- | --- |
| Method | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/Oem/Firefly/DateTime/Actions/SetDateTimeActionInfo` |
| Path | `/redfish/v1/Systems/{SystemId}/Oem/Firefly/DateTime/Actions/ComputerSystem.SetDateTime` |
| Request field | `DateTime`, string, required |
| Date format | `YYYY-MM-DD HH:mm:ss` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="Set the System Time"
    curl --request POST \
      --user '<username>:<password>' \
      --header 'Content-Type: application/json' \
      --data '{ "DateTime": "2026-08-04 18:00:00" }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Oem/Firefly/DateTime/Actions/ComputerSystem.SetDateTime'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="Set the System Time with a Token"
    curl --request POST \
      --header 'X-Xsrf-Token: <token>' \
      --header 'Content-Type: application/json' \
      --data '{ "DateTime": "2026-08-04 18:00:00" }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Oem/Firefly/DateTime/Actions/ComputerSystem.SetDateTime'
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
| `Timestamp` | string | Timestamp recorded by the server when generating the response. |
| `MessageId` | string | Message identifier; `Base.1.11.0.Success` on success. |
| `Message` | string | Description of the request processing result. |
| `MessageArgs` | array | List of message formatting arguments; an empty array in this response. |
| `MessageSeverity` | string | Message severity; `OK` on success. |
| `Severity` | string | Severity field kept for compatibility; an empty string in this response. |
| `Oem` | object \| null | OEM extension message; `null` in this response. |
| `RelatedProperties` | array \| null | Property paths related to the message; `null` in this response. |
| `Resolution` | string | Recommended resolution; `None` on success. |

<Callout title="Operation Impact" type="warn">
  Setting the system time affects log ordering, tokens, certificates, scheduled tasks, and cluster synchronization. After the POST returns a success message, query the system time immediately to confirm that the device time has been updated to the expected value.
</Callout>

### 4.5 Setting the Time Zone

| Item | Content |
| --- | --- |
| Method | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/Oem/Firefly/TimeZone/Actions/SetTimeZoneActionInfo` |
| Path | `/redfish/v1/Systems/{SystemId}/Oem/Firefly/TimeZone/Actions/ComputerSystem.SetTimeZone` |
| Request field | `TimeZone`, string, required |
| Example value | `Etc/GMT-8` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="Set the Time Zone"
    curl --request POST \
      --user '<username>:<password>' \
      --header 'Content-Type: application/json' \
      --data '{ "TimeZone": "Etc/GMT-8" }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Oem/Firefly/TimeZone/Actions/ComputerSystem.SetTimeZone'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="Set the Time Zone with a Token"
    curl --request POST \
      --header 'X-Xsrf-Token: <token>' \
      --header 'Content-Type: application/json' \
      --data '{ "TimeZone": "Etc/GMT-8" }' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Oem/Firefly/TimeZone/Actions/ComputerSystem.SetTimeZone'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

The following is the real response returned after writing the current time zone `Etc/GMT-8` back unchanged:

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
| `Timestamp` | string | Timestamp recorded by the server when generating the response. |
| `MessageId` | string | Message identifier; `Base.1.11.0.Success` on success. |
| `Message` | string | Description of the request processing result. |
| `MessageArgs` | array | List of message formatting arguments; an empty array in this response. |
| `MessageSeverity` | string | Message severity; `OK` on success. |
| `Severity` | string | Severity field kept for compatibility; an empty string in this response. |
| `Oem` | object \| null | OEM extension message; `null` in this response. |
| `RelatedProperties` | array \| null | Property paths related to the message; `null` in this response. |
| `Resolution` | string | Recommended resolution; `None` on success. |

### 4.6 Configuring the Time Service

| Item | Content |
| --- | --- |
| Method | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/Oem/Firefly/TimeService/Actions/ConfigureTimeServiceActionInfo` |
| Path | `/redfish/v1/Systems/{SystemId}/Oem/Firefly/TimeService/Actions/ConfigueTimeService` |
| Required fields | `manual`, `time_zone` |

| Request Field | Type | Required | Description |
| --- | --- | --- | --- |
| `manual` | boolean | Yes | Whether a manual time is used. |
| `time` | string | No | Manual time; used when `manual=true`. |
| `time_zone` | string | Yes | GMT time zone, for example `GMT+08:00`. |
| `ntp_config` | object | No | Time synchronization configuration. |
| `ntp_config.preferred` | string | No | Preferred time server. |
| `ntp_config.alternative` | string | No | Alternate time server. |
| `ntp_config.rtc` | boolean | No | Whether to synchronize the hardware clock. |
| `ntp_config.make_step` | object | No | Time stepping policy. |
| `ntp_config.make_step.threshold` | string | No | Time step threshold. |
| `ntp_config.make_step.limit` | string | No | Limit on the number of time steps. |
| `ntp_config.server_config` | object | No | Local time service configuration. |
| `ntp_config.server_config.port` | string | No | Time service port. |
| `ntp_config.server_config.local_stratum` | string | No | Local time source stratum. |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="Configure the Time Service"
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
    ```bash title="Configure the Time Service with a Token"
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

#### Response Example

The following is the real response returned by the device after writing the current time service configuration back unchanged.

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
| `Timestamp` | string | Timestamp recorded by the server when generating the response. |
| `MessageId` | string | Message identifier; `Base.1.11.0.Success` on success. |
| `Message` | string | Description of the request processing result. |
| `MessageArgs` | array | List of message formatting arguments; an empty array in this response. |
| `MessageSeverity` | string | Message severity; `OK` on success. |
| `Severity` | string | Severity field kept for compatibility; an empty string in this response. |
| `Oem` | object \| null | OEM extension message; `null` in this response. |
| `RelatedProperties` | array \| null | Property paths related to the message; `null` in this response. |
| `Resolution` | string | Recommended resolution; `None` on success. |

<Callout title="Verification Results on the Real Device" type="info">
  After the POST returns `200 OK`, a follow-up query confirms that the preferred time source, RTC, stepping parameters, port, and local stratum are all the same as before the operation; `time_zone` in the time service configuration is `GMT+08:00`, and the time zone resource correspondingly returns `Etc/GMT-8`. The system time keeps incrementing normally. You should still perform a GET to verify after a successful response instead of checking only `MessageId`.
</Callout>

### 4.7 Querying the Set System Time ActionInfo

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/bmc/Oem/Firefly/DateTime/Actions/SetDateTimeActionInfo` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Actual status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query the Set System Time ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Oem/Firefly/DateTime/Actions/SetDateTimeActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query the Set System Time ActionInfo with a Token"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Oem/Firefly/DateTime/Actions/SetDateTimeActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Path of the ActionInfo resource. |
| `@odata.type` | string | OData type of the ActionInfo or message resource. |
| `Id` | string | ActionInfo resource identifier. |
| `Name` | string | ActionInfo resource name. |
| `Parameters` | array | List of operation parameter definitions. |
| `Parameters[].DisallowedInput` | boolean | Whether input is disallowed for this parameter. |
| `Parameters[].AllowablePattern` | string | String pattern allowed for the parameter; an empty string means no pattern restriction. |
| `Parameters[].DataType` | string | Parameter data type. |
| `Parameters[].Name` | string | Parameter name. |
| `Parameters[].Required` | boolean | Whether the parameter is required. |

### 4.8 Querying the Set Time Zone ActionInfo

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/bmc/Oem/Firefly/TimeZone/Actions/SetTimeZoneActionInfo` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Actual status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query the Set Time Zone ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Oem/Firefly/TimeZone/Actions/SetTimeZoneActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query the Set Time Zone ActionInfo with a Token"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Oem/Firefly/TimeZone/Actions/SetTimeZoneActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Path of the ActionInfo resource. |
| `@odata.type` | string | OData type of the ActionInfo or message resource. |
| `Id` | string | ActionInfo resource identifier. |
| `Name` | string | ActionInfo resource name. |
| `Parameters` | array | List of operation parameter definitions. |
| `Parameters[].DisallowedInput` | boolean | Whether input is disallowed for this parameter. |
| `Parameters[].AllowablePattern` | string | String pattern allowed for the parameter; an empty string means no pattern restriction. |
| `Parameters[].DataType` | string | Parameter data type. |
| `Parameters[].Name` | string | Parameter name. |
| `Parameters[].Required` | boolean | Whether the parameter is required. |

### 4.9 Querying the Configure Time Service ActionInfo

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/bmc/Oem/Firefly/TimeService/Actions/ConfigureTimeServiceActionInfo` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Actual status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query the Configure Time Service ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Oem/Firefly/TimeService/Actions/ConfigureTimeServiceActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query the Configure Time Service ActionInfo with a Token"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Oem/Firefly/TimeService/Actions/ConfigureTimeServiceActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Path of the ActionInfo resource. |
| `@odata.type` | string | OData type of the ActionInfo or message resource. |
| `Id` | string | ActionInfo resource identifier. |
| `Name` | string | ActionInfo resource name. |
| `Parameters` | array | List of operation parameter definitions. |
| `Parameters[].DisallowedInput` | boolean | Whether input is disallowed for this parameter. |
| `Parameters[].AllowablePattern` | string | String pattern allowed for the parameter; an empty string means no pattern restriction. |
| `Parameters[].DataType` | string | Parameter data type. |
| `Parameters[].Name` | string | Parameter name. |
| `Parameters[].Required` | boolean | Whether the parameter is required. |
| `Parameters[].AllowableValues` | array | Allowed values and structure definitions for a parameter or sub-parameter. |
| `Parameters[].AllowableValues[].DisallowedInput` | boolean | Whether input is disallowed for this parameter. |
| `Parameters[].AllowableValues[].AllowablePattern` | string | String pattern allowed for the parameter; an empty string means no pattern restriction. |
| `Parameters[].AllowableValues[].DataType` | string | Parameter data type. |
| `Parameters[].AllowableValues[].Name` | string | Parameter name. |
| `Parameters[].AllowableValues[].Required` | boolean | Whether the parameter is required. |
| `Parameters[].AllowableValues[].AllowableValues` | array | Allowed values and structure definitions for a parameter or sub-parameter. |
| `Parameters[].AllowableValues[].AllowableValues[].DisallowedInput` | boolean | Whether input is disallowed for this parameter. |
| `Parameters[].AllowableValues[].AllowableValues[].AllowablePattern` | string | String pattern allowed for the parameter; an empty string means no pattern restriction. |
| `Parameters[].AllowableValues[].AllowableValues[].DataType` | string | Parameter data type. |
| `Parameters[].AllowableValues[].AllowableValues[].Name` | string | Parameter name. |
| `Parameters[].AllowableValues[].AllowableValues[].Required` | boolean | Whether the parameter is required. |

## 5 Disk Management Resources

### 5.1 Querying the Block Device Storage Controller

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/{SystemId}/Storages/BlockDevice` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Success status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query the Block Device Storage Controller"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query the Block Device Storage Controller with a Token"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

```json title="200 OK · Excerpt of a Real Response"
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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Access path of the block device storage resource. |
| `@odata.type` | string | OData type of the storage resource. |
| `Oem` | object | OEM extension information of the storage resource. |
| `StorageControllers@odata.count` | number | Number of storage controllers. |
| `StorageControllers` | array | List of storage controllers. |
| `StorageControllers[].@odata.id` | string | Resource path of the storage controller. |
| `StorageControllers[].@odata.type` | string | OData type of the storage controller. |
| `StorageControllers[].Id` | string | Storage controller identifier. |
| `StorageControllers[].Description` | string | Storage controller description. |
| `StorageControllers[].Manufacturer` | string | Storage controller implementer. |
| `Volumes` | object | Entry point of block device volume resources. |
| `Volumes.@odata.id` | string | Path of the block device volume resource collection. |

### 5.2 Querying the Block Device Volume Collection

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/{SystemId}/Storages/BlockDevice/Volumes` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Success status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="Query the Block Device Volume Collection"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="Query the Block Device Volume Collection with a Token"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example


```json title="200 OK · Excerpt of a Real Response"
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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Path of the block device volume collection. |
| `@odata.type` | string | OData type of the volume collection. |
| `Name` | string | Name of the volume collection. |
| `Members@odata.count` | number | Number of root block devices. |
| `Members` | array | List of root block device members. |
| `Members[].@odata.id` | string | Access path of a specific block device. |
| `Oem` | object | OEM extension information. |
| `Oem.Firefly` | object | Firefly storage extension information. |
| `Oem.Firefly.Tree` | object | `lsblk` device tree. |
| `Oem.Firefly.Tree.blockdevices` | array | List of root block devices. |
| `Oem.Firefly.Tree.blockdevices[].name` | string | Linux block device name. |
| `Oem.Firefly.Tree.blockdevices[].path` | string | Device node path. |
| `Oem.Firefly.Tree.blockdevices[].size` | number | Device capacity in bytes. |
| `Oem.Firefly.Tree.blockdevices[].type` | string | Device type. |
| `Oem.Firefly.Tree.blockdevices[].pttype` | string | Partition table type. |
| `Oem.Firefly.Tree.blockdevices[].StorageType` | string | Storage media type. |
| `Oem.Firefly.Tree.blockdevices[].OemEnableCreatePartition` | boolean | Whether creating a new partition is allowed. |
| `Oem.Firefly.Tree.blockdevices[].OemEnableDeletePartition` | boolean | Whether deleting partitions is allowed. |
| `Oem.Firefly.Tree.blockdevices[].children` | array | List of child partitions. |
| `Oem.Firefly.Tree.blockdevices[].children[].name` | string | Partition name. |
| `Oem.Firefly.Tree.blockdevices[].children[].fstype` | string | File system type. |
| `Oem.Firefly.Tree.blockdevices[].children[].mountpoint` | string | Current mount point. |
| `Oem.Firefly.Tree.blockdevices[].children[].size` | number | Partition capacity in bytes. |

### 5.3 Subscribing to Block Device Status

| Item | Content |
| --- | --- |
| Protocol | WebSocket |
| Handshake method | `GET` + `Upgrade: websocket` |
| Path | `/redfish/v1/Systems/{SystemId}/Storages/BlockDevice/Volumes/ws` |
| Authentication | Login privilege |

```text title="WebSocket URL"
<ws-protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/ws
```

After the connection is established, the server first sends the complete block device tree with `ActionType=Action_GetInfo`; it then subscribes to the storage Redis topic and continuously pushes device or task status changes.

<Callout title="Proxy Compatibility" type="warn">
  The current test entry point goes through a Polipo HTTP proxy. A plain GET returns `502 Server dropped connection`, and a manual Upgrade request returns `503 Couldn't parse client headers`, which shows that this proxy cannot forward the WebSocket handshake. The server-side routing and Upgrade implementation exist, but the connection must be verified after bypassing this HTTP proxy or using a reverse proxy that supports WebSocket.
</Callout>

### 5.4 Querying a Specific Block Device

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/{SystemId}/Storages/BlockDevice/Volumes/{BlockDeviceId}` |
| Path parameter | `BlockDeviceId`: obtained from the block device volume collection; the example below uses `mmcblk0` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Success status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="Query mmcblk0"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="Query mmcblk0 with a Token"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

```json title="200 OK · Excerpt of a Real Response"
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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Path of the specified block device resource. |
| `@odata.type` | string | OData type of the block device volume. |
| `CapacityBytes` | string | Device capacity in bytes. |
| `RAIDType` | string | RAID type. |
| `VolumeType` | string | Volume type. |
| `Oem` | object | OEM extension information. |
| `Oem.Firefly` | object | Firefly storage extension information. |
| `Oem.Firefly.Detail` | object | Detailed block device information. |
| `Oem.Firefly.Detail.name` | string | Device name. |
| `Oem.Firefly.Detail.path` | string | Linux device node path. |
| `Oem.Firefly.Detail.serial` | string | Device serial number. |
| `Oem.Firefly.Detail.size` | number | Device capacity in bytes. |
| `Oem.Firefly.Detail.type` | string | Device type. |
| `Oem.Firefly.Detail.pttype` | string | Partition table type. |
| `Oem.Firefly.Detail.StorageType` | string | Storage media type. |
| `Oem.Firefly.Detail.OemEnableCreatePartition` | boolean | Whether creating partitions is allowed. |
| `Oem.Firefly.Detail.OemEnableDeletePartition` | boolean | Whether deleting partitions is allowed. |
| `Oem.Firefly.Detail.OemEnableMakeFS` | boolean | Whether creating a file system is allowed. |
| `Oem.Firefly.Detail.OemEnableMount` | boolean | Whether mounting is allowed. |
| `Oem.Firefly.Detail.OemEnableUnmount` | boolean | Whether unmounting is allowed. |
| `Oem.Firefly.Detail.OemBlockBusy` | boolean | Whether the device is currently occupied by a task. |
| `Links` | object | Linked resources. |
| `Links.Drives@odata.count` | number | Number of associated drives. |
| `Links.Drives` | array | List of associated drives. |
| `Links.Drives[].@odata.id` | string | Path of an associated drive. |

<Callout title="Check the Capability Flags First" type="warn">
  The current `mmcblk0` is the running system disk, and the capability flags for creating/deleting partitions, formatting, mounting, and unmounting are all `false`. Clients must rely on the `OemEnable*` fields in the detail resource to decide whether to display or invoke the corresponding operations.
</Callout>

### 5.5 Querying SMART Information

This API uses POST, but it only reads device health information and does not modify partitions or the file system.

| Item | Content |
| --- | --- |
| Method | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/Storages/BlockDevice/Volumes/{BlockDeviceId}/Actions/Oem/Firefly/SmartInfoActionInfo` |
| Path | `/redfish/v1/Systems/{SystemId}/Storages/BlockDevice/Volumes/{BlockDeviceId}/Actions/Oem/Firefly/SmartInfo` |
| Request body | Empty JSON object `{}` |
| Success status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="Query the SMART Information of mmcblk0"
    curl --request POST \
      --user '<username>:<password>' \
      --header 'Content-Type: application/json' \
      --data '{}' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/SmartInfo'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="Query the SMART Information of mmcblk0 with a Token"
    curl --request POST \
      --header 'X-Xsrf-Token: <token>' \
      --header 'Content-Type: application/json' \
      --data '{}' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/SmartInfo'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

The following is the standard Redfish Message response returned when this POST succeeds. The success message only indicates that the request has been processed; it contains no SMART metric fields.

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
| `Timestamp` | string | Timestamp recorded by the server when generating the response. |
| `MessageId` | string | Message identifier; `Base.1.11.0.Success` on success. |
| `Message` | string | Description of the request processing result. |
| `MessageArgs` | array | List of message formatting arguments; an empty array in this response. |
| `MessageSeverity` | string | Message severity; `OK` on success. |
| `Severity` | string | Severity field kept for compatibility; an empty string in this response. |
| `Oem` | object \| null | OEM extension message; `null` in this response. |
| `RelatedProperties` | array \| null | Property paths related to the message; `null` in this response. |
| `Resolution` | string | Recommended resolution; `None` on success. |

### 5.6 Creating a Partition

| Item | Content |
| --- | --- |
| Method | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/Storages/BlockDevice/Volumes/{BlockDeviceId}/{FreeSpaceId}/Actions/Oem/Firefly/NewPartitionActionInfo` |
| Path | `/redfish/v1/Systems/{SystemId}/Storages/BlockDevice/Volumes/{BlockDeviceId}/{FreeSpaceId}/Actions/Oem/Firefly/NewPartition` |
| Prerequisites | A free area with `FreeSpace=true` exists in the device tree, and `OemEnableCreatePartition=true` |

| Request Field | Type | Required | Description |
| --- | --- | --- | --- |
| `StartOffsetMB` | number | Yes | Offset relative to the start of the free area, in MiB. |
| `SizeMB` | number | Yes | Capacity of the new partition in MiB. |
| `AlignmentByte` | number | No | Alignment bytes: 512, 1024, 2048, 4096, or 8192. |
| `Partlabel` | string | No | GPT partition label. |
| `Label` | string | No | File system volume label. |
| `FsType` | string | No | `ext4`, `exfat`, `ntfs`, `ext2`, `ext3`, `fat32`, or `<None>`. |

```json title="Request Body Example"
{
  "StartOffsetMB": 0,
  "SizeMB": 1024,
  "AlignmentByte": 4096,
  "Partlabel": "data",
  "Label": "data",
  "FsType": "ext4"
}
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
| `@odata.type` | string | OData type of the Redfish message resource. |
| `Timestamp` | string | Timestamp recorded by the server when generating the response. |
| `MessageId` | string | Message identifier; `Base.1.11.0.Success` on success. |
| `Message` | string | Description of the request processing result. |
| `MessageArgs` | array | List of message formatting arguments; an empty array in this response. |
| `MessageSeverity` | string | Message severity; `OK` on success. |
| `Severity` | string | Severity field kept for compatibility; an empty string in this response. |
| `Oem` | object \| null | OEM extension message; `null` in this response. |
| `RelatedProperties` | array \| null | Property paths related to the message; `null` in this response. |
| `Resolution` | string | Recommended resolution; `None` on success. |

### 5.7 Writing a Volume Label

| Item | Content |
| --- | --- |
| Method | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/Storages/BlockDevice/Volumes/{BlockDeviceId}/Actions/Oem/Firefly/WriteLabelActionInfo` |
| Path | `/redfish/v1/Systems/{SystemId}/Storages/BlockDevice/Volumes/{BlockDeviceId}/Actions/Oem/Firefly/WriteLabel` |
| Request field | `Label`, string, required |
| Prerequisites | `OemEnableCreteLabel=true`, and the target is not a system partition in use |

```json title="Request Body Example"
{
  "Label": "data"
}
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
| `@odata.type` | string | OData type of the Redfish message resource. |
| `Timestamp` | string | Timestamp recorded by the server when generating the response. |
| `MessageId` | string | Message identifier; `Base.1.11.0.Success` on success. |
| `Message` | string | Description of the request processing result. |
| `MessageArgs` | array | List of message formatting arguments; an empty array in this response. |
| `MessageSeverity` | string | Message severity; `OK` on success. |
| `Severity` | string | Severity field kept for compatibility; an empty string in this response. |
| `Oem` | object \| null | OEM extension message; `null` in this response. |
| `RelatedProperties` | array \| null | Property paths related to the message; `null` in this response. |
| `Resolution` | string | Recommended resolution; `None` on success. |

### 5.8 Mounting a Block Device

| Item | Content |
| --- | --- |
| Method | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/Storages/BlockDevice/Volumes/{BlockDeviceId}/Actions/Oem/Firefly/MountActionInfo` |
| Path | `/redfish/v1/Systems/{SystemId}/Storages/BlockDevice/Volumes/{BlockDeviceId}/Actions/Oem/Firefly/Mount` |
| Prerequisites | `OemEnableMount=true` |

| Request Field | Type | Required | Description |
| --- | --- | --- | --- |
| `MountPath` | string | Yes | Mount path that is absolute and not the root directory. |
| `MountType` | string | Yes | `forever` for a persistent mount, `tmp` for a temporary mount. |
| `RwModel` | string | No | `rw` or `ro`. |

```json title="Request Body Example"
{
  "MountPath": "/mnt/data",
  "MountType": "tmp",
  "RwModel": "rw"
}
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
| `@odata.type` | string | OData type of the Redfish message resource. |
| `Timestamp` | string | Timestamp recorded by the server when generating the response. |
| `MessageId` | string | Message identifier; `Base.1.11.0.Success` on success. |
| `Message` | string | Description of the request processing result. |
| `MessageArgs` | array | List of message formatting arguments; an empty array in this response. |
| `MessageSeverity` | string | Message severity; `OK` on success. |
| `Severity` | string | Severity field kept for compatibility; an empty string in this response. |
| `Oem` | object \| null | OEM extension message; `null` in this response. |
| `RelatedProperties` | array \| null | Property paths related to the message; `null` in this response. |
| `Resolution` | string | Recommended resolution; `None` on success. |

### 5.9 Unmounting a Block Device

| Item | Content |
| --- | --- |
| Method | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/Storages/BlockDevice/Volumes/{BlockDeviceId}/Actions/Oem/Firefly/UMountActionInfo` |
| Path | `/redfish/v1/Systems/{SystemId}/Storages/BlockDevice/Volumes/{BlockDeviceId}/Actions/Oem/Firefly/UMount` |
| Request field | `MountPath`, string, required |
| Prerequisites | `OemEnableUnmount=true` |

```json title="Request Body Example"
{
  "MountPath": "/mnt/data"
}
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
| `@odata.type` | string | OData type of the Redfish message resource. |
| `Timestamp` | string | Timestamp recorded by the server when generating the response. |
| `MessageId` | string | Message identifier; `Base.1.11.0.Success` on success. |
| `Message` | string | Description of the request processing result. |
| `MessageArgs` | array | List of message formatting arguments; an empty array in this response. |
| `MessageSeverity` | string | Message severity; `OK` on success. |
| `Severity` | string | Severity field kept for compatibility; an empty string in this response. |
| `Oem` | object \| null | OEM extension message; `null` in this response. |
| `RelatedProperties` | array \| null | Property paths related to the message; `null` in this response. |
| `Resolution` | string | Recommended resolution; `None` on success. |

### 5.10 Creating a File System

| Item | Content |
| --- | --- |
| Method | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/Storages/BlockDevice/Volumes/{BlockDeviceId}/Actions/Oem/Firefly/MakeFileSystemActionInfo` |
| Path | `/redfish/v1/Systems/{SystemId}/Storages/BlockDevice/Volumes/{BlockDeviceId}/Actions/Oem/Firefly/MakeFileSystem` |
| Prerequisites | `OemEnableMakeFS=true` |

| Request Field | Type | Required | Description |
| --- | --- | --- | --- |
| `FsType` | string | Yes | `ext4`, `exfat`, `ntfs`, `ext2`, `ext3`, or `fat32`. |
| `Label` | string | No | File system volume label. |

```json title="Request Body Example"
{
  "FsType": "ext4",
  "Label": "data"
}
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
| `@odata.type` | string | OData type of the Redfish message resource. |
| `Timestamp` | string | Timestamp recorded by the server when generating the response. |
| `MessageId` | string | Message identifier; `Base.1.11.0.Success` on success. |
| `Message` | string | Description of the request processing result. |
| `MessageArgs` | array | List of message formatting arguments; an empty array in this response. |
| `MessageSeverity` | string | Message severity; `OK` on success. |
| `Severity` | string | Severity field kept for compatibility; an empty string in this response. |
| `Oem` | object \| null | OEM extension message; `null` in this response. |
| `RelatedProperties` | array \| null | Property paths related to the message; `null` in this response. |
| `Resolution` | string | Recommended resolution; `None` on success. |

### 5.11 Checking or Repairing a Block Device

| Item | Content |
| --- | --- |
| Method | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/Storages/BlockDevice/Volumes/{BlockDeviceId}/Actions/Oem/Firefly/CheckRepairActionInfo` |
| Path | `/redfish/v1/Systems/{SystemId}/Storages/BlockDevice/Volumes/{BlockDeviceId}/Actions/Oem/Firefly/CheckRepair` |
| Request field | `ActionType`, string, required |

`ActionType` allowed values:

- `Action_CheckFS`: check the file system.
- `Action_CheckFSAutoRepair`: check the file system and repair it automatically.
- `Action_CheckSector`: check sectors.

```json title="Request Body Example"
{
  "ActionType": "Action_CheckFS"
}
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
| `@odata.type` | string | OData type of the Redfish message resource. |
| `Timestamp` | string | Timestamp recorded by the server when generating the response. |
| `MessageId` | string | Message identifier; `Base.1.11.0.Success` on success. |
| `Message` | string | Description of the request processing result. |
| `MessageArgs` | array | List of message formatting arguments; an empty array in this response. |
| `MessageSeverity` | string | Message severity; `OK` on success. |
| `Severity` | string | Severity field kept for compatibility; an empty string in this response. |
| `Oem` | object \| null | OEM extension message; `null` in this response. |
| `RelatedProperties` | array \| null | Property paths related to the message; `null` in this response. |
| `Resolution` | string | Recommended resolution; `None` on success. |

### 5.12 Canceling a Storage Task

| Item | Content |
| --- | --- |
| Method | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/Storages/BlockDevice/Volumes/{BlockDeviceId}/Actions/Oem/Firefly/TaskListActionInfo` |
| Path | `/redfish/v1/Systems/{SystemId}/Storages/BlockDevice/Volumes/{BlockDeviceId}/Actions/Oem/Firefly/TaskList` |
| `ActionType` | `Action_CheckFS`, `Action_CheckFSAutoRepair`, `Action_CheckSector`, or `Action_Mkfs` |
| `Model` | Only `Cancel` is allowed |

```json title="Request Body Example"
{
  "ActionType": "Action_CheckFS",
  "Model": "Cancel"
}
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
| `@odata.type` | string | OData type of the Redfish message resource. |
| `Timestamp` | string | Timestamp recorded by the server when generating the response. |
| `MessageId` | string | Message identifier; `Base.1.11.0.Success` on success. |
| `Message` | string | Description of the request processing result. |
| `MessageArgs` | array | List of message formatting arguments; an empty array in this response. |
| `MessageSeverity` | string | Message severity; `OK` on success. |
| `Severity` | string | Severity field kept for compatibility; an empty string in this response. |
| `Oem` | object \| null | OEM extension message; `null` in this response. |
| `RelatedProperties` | array \| null | Property paths related to the message; `null` in this response. |
| `Resolution` | string | Recommended resolution; `None` on success. |

### 5.13 Deleting a Partition

| Item | Content |
| --- | --- |
| Method | `DELETE` |
| Path | `/redfish/v1/Systems/{SystemId}/Storages/BlockDevice/Volumes/{BlockDeviceId}/{PartitionId}` |
| Request body | None |
| Prerequisites | `OemEnableDeletePartition=true`, and the target partition is not mounted and belongs to neither the system nor a RAID |

<Callout title="Irreversible Operation" type="warn">
  Deleting a partition modifies the partition table and makes the data on the partition inaccessible. The deletion capability of the current `mmcblk0` is `false`, so no DELETE was performed.
</Callout>

### 5.14 Querying the Software RAID Storage Controller

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Success status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="Query the Software RAID Storage Controller"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="Query the Software RAID Storage Controller with a Token"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

```json title="200 OK · Excerpt of a Real Response"
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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Path of the software RAID controller resource. |
| `@odata.type` | string | OData type of the storage resource. |
| `StorageControllers@odata.count` | number | Number of software RAID controllers. |
| `StorageControllers` | array | List of controllers. |
| `StorageControllers[].Id` | string | Controller identifier. |
| `StorageControllers[].Description` | string | Controller description. |
| `StorageControllers[].Manufacturer` | string | Controller implementer. |
| `StorageControllers[].Oem` | object | Controller OEM information. |
| `StorageControllers[].Oem.Firefly` | object | Firefly RAID information. |
| `StorageControllers[].Oem.Firefly.FreeResource` | object | Resources available for creating a RAID. |
| `StorageControllers[].Oem.Firefly.FreeResource.Mdname` | string | Suggested array device name. |
| `StorageControllers[].Oem.Firefly.FreeResource.Levels` | array | Supported RAID levels. |
| `StorageControllers[].Oem.Firefly.FreeResource.Chunks` | array | Supported stripe chunk sizes. |
| `StorageControllers[].Oem.Firefly.FreeResource.Layouts` | array | Supported layouts. |
| `StorageControllers[].Oem.Firefly.FreeResource.Blocks` | array | Block devices that can join a RAID. |
| `StorageControllers[].Oem.Firefly.RiskInfo` | object \| null | Result of the most recent risk assessment. |
| `Volumes` | object | Entry point of RAID volumes. |

### 5.15 Querying the Software RAID Volume Collection

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid/Volumes` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Success status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="Query the Software RAID Volume Collection"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="Query the Software RAID Volume Collection with a Token"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Path of the software RAID volume collection. |
| `@odata.type` | string | OData type of the volume collection. |
| `Name` | string | Name of the volume collection. |
| `Members@odata.count` | number | Number of RAID arrays. |
| `Members` | array | List of RAID array members. |
| `Oem` | object | OEM extension information. |
| `Oem.Firefly` | object | Firefly RAID extension information. |
| `Oem.Firefly.All` | object | All RAID data. |
| `Oem.Firefly.All.Raidlist` | array | List of RAID arrays recognized by the backend. |
| `Oem.Firefly.All.Mdadmconf` | object \| null | `mdadm` configuration content. |

### 5.16 Creating a Software RAID Array

| Item | Content |
| --- | --- |
| Method | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid/Actions/Oem/Firefly/CreateRaidArrayActionInfo` |
| Path | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid/Actions/Oem/Firefly/CreateRaidArray` |

| Request Field | Type | Required | Description |
| --- | --- | --- | --- |
| `DevName` | string | Yes | Array device path or device name. |
| `MdName` | string | Yes | RAID array name. |
| `Level` | string | Yes | `Linear`, `Raid0`, `Raid1`, `Raid4`, `Raid5`, `Raid6`, or `Raid10`. |
| `Chunk` | string | No | `64K`, `128K`, `256K`, or `512K`. |
| `Metadata` | string | No | `0.90`, `1.0`, `1.1`, or `1.2`. |
| `Blocks` | string[] | Yes | Block device paths that participate in the array. |

```json title="Request Body Example"
{
  "DevName": "/dev/md0",
  "MdName": "md0",
  "Level": "Raid1",
  "Chunk": "512K",
  "Metadata": "1.2",
  "Blocks": ["/dev/sda", "/dev/sdb"]
}
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
| `@odata.type` | string | OData type of the Redfish message resource. |
| `Timestamp` | string | Timestamp recorded by the server when generating the response. |
| `MessageId` | string | Message identifier; `Base.1.11.0.Success` on success. |
| `Message` | string | Description of the request processing result. |
| `MessageArgs` | array | List of message formatting arguments; an empty array in this response. |
| `MessageSeverity` | string | Message severity; `OK` on success. |
| `Severity` | string | Severity field kept for compatibility; an empty string in this response. |
| `Oem` | object \| null | OEM extension message; `null` in this response. |
| `RelatedProperties` | array \| null | Property paths related to the message; `null` in this response. |
| `Resolution` | string | Recommended resolution; `None` on success. |

### 5.17 Setting RAID Risk Information

| Item | Content |
| --- | --- |
| Method | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid/Actions/Oem/Firefly/SetRiskInfoActionInfo` |
| Path | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid/Actions/Oem/Firefly/SetRiskInfo` |

| Request Field | Type | Required | Description |
| --- | --- | --- | --- |
| `Level` | string | No | Target RAID level. |
| `Ops` | string | Yes | `create`, `expan`, or `change`. |
| `Blocks` | object[] | Yes | List of disks used for the risk assessment. |
| `Blocks[].BlockName` | string | Yes | Block device name. |
| `Blocks[].Size` | number | Yes | Block device capacity. |
| `Blocks[].Rota` | boolean | Yes | Whether the media is rotational. |

```json title="Request Body Example"
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
| `Timestamp` | string | Timestamp recorded by the server when generating the response. |
| `MessageId` | string | Message identifier; `Base.1.11.0.Success` on success. |
| `Message` | string | Description of the request processing result. |
| `MessageArgs` | array | List of message formatting arguments; an empty array in this response. |
| `MessageSeverity` | string | Message severity; `OK` on success. |
| `Severity` | string | Severity field kept for compatibility; an empty string in this response. |
| `Oem` | object \| null | OEM extension message; `null` in this response. |
| `RelatedProperties` | array \| null | Property paths related to the message; `null` in this response. |
| `Resolution` | string | Recommended resolution; `None` on success. |

### 5.18 Querying a Specific Software RAID Array

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid/Volumes/{RaidArrayId}` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="basic-auth">
    ```bash title="Query a Specific Software RAID Array"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/<raid-array-id>'
    ```
  </CodeBlockTab>
  <CodeBlockTab value="token">
    ```bash title="Query a Specific Software RAID Array with a Token"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/<raid-array-id>'
    ```
  </CodeBlockTab>
</CodeBlockTabs>


### 5.19 Removing a Member Disk from a RAID Array

| Item | Content |
| --- | --- |
| Method | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid/Volumes/{RaidArrayId}/Actions/Oem/Firefly/RemoveDiskActionInfo` |
| Path | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid/Volumes/{RaidArrayId}/Actions/Oem/Firefly/RemoveDisk` |
| Request field | `BlocksPath`, string[], required |
| Server-side limitation | Code comments indicate that only RAID1 supports removing member disks |

```json title="Request Body Example"
{
  "BlocksPath": ["/dev/sdb"]
}
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
| `@odata.type` | string | OData type of the Redfish message resource. |
| `Timestamp` | string | Timestamp recorded by the server when generating the response. |
| `MessageId` | string | Message identifier; `Base.1.11.0.Success` on success. |
| `Message` | string | Description of the request processing result. |
| `MessageArgs` | array | List of message formatting arguments; an empty array in this response. |
| `MessageSeverity` | string | Message severity; `OK` on success. |
| `Severity` | string | Severity field kept for compatibility; an empty string in this response. |
| `Oem` | object \| null | OEM extension message; `null` in this response. |
| `RelatedProperties` | array \| null | Property paths related to the message; `null` in this response. |
| `Resolution` | string | Recommended resolution; `None` on success. |

### 5.20 Adding a Member Disk to a RAID Array

| Item | Content |
| --- | --- |
| Method | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid/Volumes/{RaidArrayId}/Actions/Oem/Firefly/AddDiskActionInfo` |
| Path | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid/Volumes/{RaidArrayId}/Actions/Oem/Firefly/AddDisk` |
| Request field | `BlocksPath`, string[], required |

```json title="Request Body Example"
{
  "BlocksPath": ["/dev/sdc"]
}
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
| `@odata.type` | string | OData type of the Redfish message resource. |
| `Timestamp` | string | Timestamp recorded by the server when generating the response. |
| `MessageId` | string | Message identifier; `Base.1.11.0.Success` on success. |
| `Message` | string | Description of the request processing result. |
| `MessageArgs` | array | List of message formatting arguments; an empty array in this response. |
| `MessageSeverity` | string | Message severity; `OK` on success. |
| `Severity` | string | Severity field kept for compatibility; an empty string in this response. |
| `Oem` | object \| null | OEM extension message; `null` in this response. |
| `RelatedProperties` | array \| null | Property paths related to the message; `null` in this response. |
| `Resolution` | string | Recommended resolution; `None` on success. |

### 5.21 Setting the RAID Bitmap

| Item | Content |
| --- | --- |
| Method | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid/Volumes/{RaidArrayId}/Actions/Oem/Firefly/BitmapActionInfo` |
| Path | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid/Volumes/{RaidArrayId}/Actions/Oem/Firefly/Bitmap` |
| Request field | `BitmapFlag`, boolean, required |

```json title="Request Body Example"
{
  "BitmapFlag": true
}
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
| `@odata.type` | string | OData type of the Redfish message resource. |
| `Timestamp` | string | Timestamp recorded by the server when generating the response. |
| `MessageId` | string | Message identifier; `Base.1.11.0.Success` on success. |
| `Message` | string | Description of the request processing result. |
| `MessageArgs` | array | List of message formatting arguments; an empty array in this response. |
| `MessageSeverity` | string | Message severity; `OK` on success. |
| `Severity` | string | Severity field kept for compatibility; an empty string in this response. |
| `Oem` | object \| null | OEM extension message; `null` in this response. |
| `RelatedProperties` | array \| null | Property paths related to the message; `null` in this response. |
| `Resolution` | string | Recommended resolution; `None` on success. |

### 5.22 Replacing a RAID Member Disk

| Item | Content |
| --- | --- |
| Method | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid/Volumes/{RaidArrayId}/Actions/Oem/Firefly/ReplaceDiskActionInfo` |
| Path | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid/Volumes/{RaidArrayId}/Actions/Oem/Firefly/ReplaceDisk` |

| Request Field | Type | Required | Description |
| --- | --- | --- | --- |
| `OldBlocksPath` | string[] | Yes | Paths of the original member disks to be replaced. |
| `NewBlocksPath` | string[] | Yes | Paths of the new member disks. |

```json title="Request Body Example"
{
  "OldBlocksPath": ["/dev/sdb"],
  "NewBlocksPath": ["/dev/sdc"]
}
```

<Callout title="Member Disk Operations Not Performed" type="warn">
  The current RAID volume collection is empty, so no corresponding ActionInfo can be generated and there are no member disks that can be safely operated on. The methods, paths, and parameters above have been verified against the server-side registration code and the request structure, but no POST was performed.
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
| `@odata.type` | string | OData type of the Redfish message resource. |
| `Timestamp` | string | Timestamp recorded by the server when generating the response. |
| `MessageId` | string | Message identifier; `Base.1.11.0.Success` on success. |
| `Message` | string | Description of the request processing result. |
| `MessageArgs` | array | List of message formatting arguments; an empty array in this response. |
| `MessageSeverity` | string | Message severity; `OK` on success. |
| `Severity` | string | Severity field kept for compatibility; an empty string in this response. |
| `Oem` | object \| null | OEM extension message; `null` in this response. |
| `RelatedProperties` | array \| null | Property paths related to the message; `null` in this response. |
| `Resolution` | string | Recommended resolution; `None` on success. |

### 5.23 Getting the RAID Reshape Risk Context

| Item | Content |
| --- | --- |
| Method | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid/Volumes/{RaidArrayId}/Actions/Oem/Firefly/Reshape/SetCurrentRiskInfoActionInfo` |
| Path | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid/Volumes/{RaidArrayId}/Actions/Oem/Firefly/Reshape/SetCurrentRiskInfo` |
| Request field | `Level`, string, required |
| Allowed values | `Linear`, `Raid0`, `Raid1`, `Raid4`, `Raid5`, `Raid6`, `Raid10` |

```json title="Request Body Example"
{
  "Level": "Raid5"
}
```

This API calculates the reshape risk and RAID level capabilities of the current array and saves them to the array runtime state. No array currently exists, so no POST was performed.

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
| `Timestamp` | string | Timestamp recorded by the server when generating the response. |
| `MessageId` | string | Message identifier; `Base.1.11.0.Success` on success. |
| `Message` | string | Description of the request processing result. |
| `MessageArgs` | array | List of message formatting arguments; an empty array in this response. |
| `MessageSeverity` | string | Message severity; `OK` on success. |
| `Severity` | string | Severity field kept for compatibility; an empty string in this response. |
| `Oem` | object \| null | OEM extension message; `null` in this response. |
| `RelatedProperties` | array \| null | Property paths related to the message; `null` in this response. |
| `Resolution` | string | Recommended resolution; `None` on success. |

### 5.24 Comparing RAID Reshape Target Levels

| Item | Content |
| --- | --- |
| Method | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid/Volumes/{RaidArrayId}/Actions/Oem/Firefly/Reshape/CompareActionInfo` |
| Path | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid/Volumes/{RaidArrayId}/Actions/Oem/Firefly/Reshape/Compare` |
| Request field | `Level`, string, required |
| Allowed values | `Linear`, `Raid0`, `Raid1`, `Raid4`, `Raid5`, `Raid6`, `Raid10` |

```json title="Request Body Example"
{
  "Level": "Raid5"
}
```

The server calculates the upgrade or downgrade direction, the target capacity, the number of disks to add or release, and the disk count constraints for RAID10.

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
| `Timestamp` | string | Timestamp recorded by the server when generating the response. |
| `MessageId` | string | Message identifier; `Base.1.11.0.Success` on success. |
| `Message` | string | Description of the request processing result. |
| `MessageArgs` | array | List of message formatting arguments; an empty array in this response. |
| `MessageSeverity` | string | Message severity; `OK` on success. |
| `Severity` | string | Severity field kept for compatibility; an empty string in this response. |
| `Oem` | object \| null | OEM extension message; `null` in this response. |
| `RelatedProperties` | array \| null | Property paths related to the message; `null` in this response. |
| `Resolution` | string | Recommended resolution; `None` on success. |

### 5.25 Calculating RAID Reshape Risk

| Item | Content |
| --- | --- |
| Method | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid/Volumes/{RaidArrayId}/Actions/Oem/Firefly/Reshape/RiskActionInfo` |
| Path | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid/Volumes/{RaidArrayId}/Actions/Oem/Firefly/Reshape/Risk` |

| Request Field | Type | Required | Description |
| --- | --- | --- | --- |
| `Level` | string | No | Target RAID level. |
| `Ops` | string | Yes | `create`, `expan`, or `change`. |
| `Blocks` | object[] | Yes | Disks involved in the risk calculation. |
| `Blocks[].BlockName` | string | Yes | Block device name. |
| `Blocks[].Size` | number | Yes | Block device capacity. |
| `Blocks[].Rota` | boolean | Yes | Whether the media is rotational. |

```json title="Request Body Example"
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
| `Timestamp` | string | Timestamp recorded by the server when generating the response. |
| `MessageId` | string | Message identifier; `Base.1.11.0.Success` on success. |
| `Message` | string | Description of the request processing result. |
| `MessageArgs` | array | List of message formatting arguments; an empty array in this response. |
| `MessageSeverity` | string | Message severity; `OK` on success. |
| `Severity` | string | Severity field kept for compatibility; an empty string in this response. |
| `Oem` | object \| null | OEM extension message; `null` in this response. |
| `RelatedProperties` | array \| null | Property paths related to the message; `null` in this response. |
| `Resolution` | string | Recommended resolution; `None` on success. |

### 5.26 Executing a RAID Reshape

| Item | Content |
| --- | --- |
| Method | `POST` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid/Volumes/{RaidArrayId}/Actions/Oem/Firefly/Reshape/ExecActionInfo` |
| Path | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid/Volumes/{RaidArrayId}/Actions/Oem/Firefly/Reshape/Exec` |
| Request fields | Same as "Calculating RAID Reshape Risk" |

```json title="Request Body Example"
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

<Callout title="Do Not Judge a Reshape Solely by Request Success" type="warn">
  A reshape can run for a long time and degrade the array. Before executing, call the risk context, level comparison, and risk calculation APIs first; afterwards, keep querying the status of the specific RAID array. No RAID array currently exists, so no reshape POST was performed this time.
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
| `@odata.type` | string | OData type of the Redfish message resource. |
| `Timestamp` | string | Timestamp recorded by the server when generating the response. |
| `MessageId` | string | Message identifier; `Base.1.11.0.Success` on success. |
| `Message` | string | Description of the request processing result. |
| `MessageArgs` | array | List of message formatting arguments; an empty array in this response. |
| `MessageSeverity` | string | Message severity; `OK` on success. |
| `Severity` | string | Severity field kept for compatibility; an empty string in this response. |
| `Oem` | object \| null | OEM extension message; `null` in this response. |
| `RelatedProperties` | array \| null | Property paths related to the message; `null` in this response. |
| `Resolution` | string | Recommended resolution; `None` on success. |

### 5.27 Assembling a Software RAID Array

| Item | Content |
| --- | --- |
| Method | `POST` |
| Path | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid/Volumes/{RaidArrayId}/Actions/Assemble` |
| ActionInfo | No separate ActionInfo |
| Request body | None |

This route calls the server-side `AssembleRaid`. No array is currently available to assemble, so no POST was performed.

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
| `Timestamp` | string | Timestamp recorded by the server when generating the response. |
| `MessageId` | string | Message identifier; `Base.1.11.0.Success` on success. |
| `Message` | string | Description of the request processing result. |
| `MessageArgs` | array | List of message formatting arguments; an empty array in this response. |
| `MessageSeverity` | string | Message severity; `OK` on success. |
| `Severity` | string | Severity field kept for compatibility; an empty string in this response. |
| `Oem` | object \| null | OEM extension message; `null` in this response. |
| `RelatedProperties` | array \| null | Property paths related to the message; `null` in this response. |
| `Resolution` | string | Recommended resolution; `None` on success. |

### 5.28 Deleting a Software RAID Array

| Item | Content |
| --- | --- |
| Method | `DELETE` |
| Path | `/redfish/v1/Systems/{SystemId}/Storages/Mdraid/Volumes/{RaidArrayId}` |
| Request body | None |

<Callout title="RAID Deletion Risk" type="warn">
  The server calls `DeleteRaid` to delete the array. This operation may erase the RAID metadata and make the data inaccessible; no RAID array currently exists, so no DELETE was performed.
</Callout>

### 5.29 Querying the SMART Operation ActionInfo

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/SmartInfoActionInfo` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Actual status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query the SMART Operation ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/SmartInfoActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query the SMART Operation ActionInfo with a Token"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/SmartInfoActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

```json title="200 OK"
{
  "@odata.id": "/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/SmartInfoActionInfo",
  "@odata.type": "#ActionInfo.v1_1_2.ActionInfo",
  "Id": "ActionInfo",
  "Name": "Action Info",
  "Parameters": null
}
```

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Path of the ActionInfo resource. |
| `@odata.type` | string | OData type of the ActionInfo or message resource. |
| `Id` | string | ActionInfo resource identifier. |
| `Name` | string | ActionInfo resource name. |
| `Parameters` | null | List of operation parameter definitions. |

### 5.30 Querying the Create Partition ActionInfo

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/free0/Actions/Oem/Firefly/NewPartitionActionInfo` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Actual status code | `405 Method Not Allowed` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query the Create Partition ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/free0/Actions/Oem/Firefly/NewPartitionActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query the Create Partition ActionInfo with a Token"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/free0/Actions/Oem/Firefly/NewPartitionActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `error` | object | Redfish error object. |
| `error.@Message.ExtendedInfo` | array | List of error details. |
| `error.@Message.ExtendedInfo[].@odata.type` | string | OData type of the ActionInfo or message resource. |
| `error.@Message.ExtendedInfo[].Timestamp` | string | Timestamp when the message was generated. |
| `error.@Message.ExtendedInfo[].MessageId` | string | Redfish message identifier. |
| `error.@Message.ExtendedInfo[].Message` | string | Specific error message. |
| `error.@Message.ExtendedInfo[].MessageArgs` | array | List of message formatting arguments. |
| `error.@Message.ExtendedInfo[].MessageSeverity` | string | Message severity. |
| `error.@Message.ExtendedInfo[].Severity` | string | Severity field kept for compatibility. |
| `error.@Message.ExtendedInfo[].Oem` | null | OEM extension message. |
| `error.@Message.ExtendedInfo[].RelatedProperties` | null | Property paths related to the message. |
| `error.@Message.ExtendedInfo[].Resolution` | string | Recommended resolution. |
| `error.code` | string | Redfish error code. |
| `error.message` | string | Generic Redfish error description. |

<Callout title="No Unallocated Space on the Current Device" type="info">
  The current system disk has no unallocated area available for creating a new partition, so querying with `free0` actually returns `405 Method Not Allowed`. Once a valid free-space resource appears, replace `free0` in the path with the actual free-space identifier returned by the query.
</Callout>

### 5.31 Querying the Write Volume Label ActionInfo

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/WriteLabelActionInfo` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Actual status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query the Write Volume Label ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/WriteLabelActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query the Write Volume Label ActionInfo with a Token"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/WriteLabelActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Path of the ActionInfo resource. |
| `@odata.type` | string | OData type of the ActionInfo or message resource. |
| `Id` | string | ActionInfo resource identifier. |
| `Name` | string | ActionInfo resource name. |
| `Parameters` | array | List of operation parameter definitions. |
| `Parameters[].DisallowedInput` | boolean | Whether input is disallowed for this parameter. |
| `Parameters[].AllowablePattern` | string | String pattern allowed for the parameter; an empty string means no pattern restriction. |
| `Parameters[].DataType` | string | Parameter data type. |
| `Parameters[].Name` | string | Parameter name. |
| `Parameters[].Required` | boolean | Whether the parameter is required. |

### 5.32 Querying the Mount Block Device ActionInfo

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/MountActionInfo` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Actual status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query the Mount Block Device ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/MountActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query the Mount Block Device ActionInfo with a Token"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/MountActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Path of the ActionInfo resource. |
| `@odata.type` | string | OData type of the ActionInfo or message resource. |
| `Id` | string | ActionInfo resource identifier. |
| `Name` | string | ActionInfo resource name. |
| `Parameters` | array | List of operation parameter definitions. |
| `Parameters[].DisallowedInput` | boolean | Whether input is disallowed for this parameter. |
| `Parameters[].AllowablePattern` | string | String pattern allowed for the parameter; an empty string means no pattern restriction. |
| `Parameters[].DataType` | string | Parameter data type. |
| `Parameters[].Name` | string | Parameter name. |
| `Parameters[].Required` | boolean | Whether the parameter is required. |
| `Parameters[].AllowableValues` | array | Allowed values and structure definitions for a parameter or sub-parameter. |

### 5.33 Querying the Unmount Block Device ActionInfo

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/UMountActionInfo` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Actual status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query the Unmount Block Device ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/UMountActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query the Unmount Block Device ActionInfo with a Token"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/UMountActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Path of the ActionInfo resource. |
| `@odata.type` | string | OData type of the ActionInfo or message resource. |
| `Id` | string | ActionInfo resource identifier. |
| `Name` | string | ActionInfo resource name. |
| `Parameters` | array | List of operation parameter definitions. |
| `Parameters[].DisallowedInput` | boolean | Whether input is disallowed for this parameter. |
| `Parameters[].AllowablePattern` | string | String pattern allowed for the parameter; an empty string means no pattern restriction. |
| `Parameters[].DataType` | string | Parameter data type. |
| `Parameters[].Name` | string | Parameter name. |
| `Parameters[].Required` | boolean | Whether the parameter is required. |

### 5.34 Querying the Create File System ActionInfo

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/MakeFileSystemActionInfo` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Actual status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query the Create File System ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/MakeFileSystemActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query the Create File System ActionInfo with a Token"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/MakeFileSystemActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Path of the ActionInfo resource. |
| `@odata.type` | string | OData type of the ActionInfo or message resource. |
| `Id` | string | ActionInfo resource identifier. |
| `Name` | string | ActionInfo resource name. |
| `Parameters` | array | List of operation parameter definitions. |
| `Parameters[].DisallowedInput` | boolean | Whether input is disallowed for this parameter. |
| `Parameters[].AllowablePattern` | string | String pattern allowed for the parameter; an empty string means no pattern restriction. |
| `Parameters[].AllowableValues` | array | Allowed values and structure definitions for a parameter or sub-parameter. |
| `Parameters[].DataType` | string | Parameter data type. |
| `Parameters[].Name` | string | Parameter name. |
| `Parameters[].Required` | boolean | Whether the parameter is required. |

### 5.35 Querying the Check-and-Repair ActionInfo

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/CheckRepairActionInfo` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Actual status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query the Check-and-Repair ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/CheckRepairActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query the Check-and-Repair ActionInfo with a Token"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/CheckRepairActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Path of the ActionInfo resource. |
| `@odata.type` | string | OData type of the ActionInfo or message resource. |
| `Id` | string | ActionInfo resource identifier. |
| `Name` | string | ActionInfo resource name. |
| `Parameters` | array | List of operation parameter definitions. |
| `Parameters[].DisallowedInput` | boolean | Whether input is disallowed for this parameter. |
| `Parameters[].AllowablePattern` | string | String pattern allowed for the parameter; an empty string means no pattern restriction. |
| `Parameters[].AllowableValues` | array | Allowed values and structure definitions for a parameter or sub-parameter. |
| `Parameters[].DataType` | string | Parameter data type. |
| `Parameters[].Name` | string | Parameter name. |
| `Parameters[].Required` | boolean | Whether the parameter is required. |

### 5.36 Querying the Cancel Storage Task ActionInfo

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/TaskListActionInfo` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Actual status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query the Cancel Storage Task ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/TaskListActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query the Cancel Storage Task ActionInfo with a Token"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/BlockDevice/Volumes/mmcblk0/Actions/Oem/Firefly/TaskListActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Path of the ActionInfo resource. |
| `@odata.type` | string | OData type of the ActionInfo or message resource. |
| `Id` | string | ActionInfo resource identifier. |
| `Name` | string | ActionInfo resource name. |
| `Parameters` | array | List of operation parameter definitions. |
| `Parameters[].DisallowedInput` | boolean | Whether input is disallowed for this parameter. |
| `Parameters[].AllowablePattern` | string | String pattern allowed for the parameter; an empty string means no pattern restriction. |
| `Parameters[].AllowableValues` | array | Allowed values and structure definitions for a parameter or sub-parameter. |
| `Parameters[].DataType` | string | Parameter data type. |
| `Parameters[].Name` | string | Parameter name. |
| `Parameters[].Required` | boolean | Whether the parameter is required. |

### 5.37 Querying the Create Software RAID ActionInfo

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/bmc/Storages/Mdraid/Actions/Oem/Firefly/CreateRaidArrayActionInfo` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Actual status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query the Create Software RAID ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid/Actions/Oem/Firefly/CreateRaidArrayActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query the Create Software RAID ActionInfo with a Token"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid/Actions/Oem/Firefly/CreateRaidArrayActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Path of the ActionInfo resource. |
| `@odata.type` | string | OData type of the ActionInfo or message resource. |
| `Id` | string | ActionInfo resource identifier. |
| `Name` | string | ActionInfo resource name. |
| `Parameters` | array | List of operation parameter definitions. |
| `Parameters[].DisallowedInput` | boolean | Whether input is disallowed for this parameter. |
| `Parameters[].AllowablePattern` | string | String pattern allowed for the parameter; an empty string means no pattern restriction. |
| `Parameters[].DataType` | string | Parameter data type. |
| `Parameters[].Name` | string | Parameter name. |
| `Parameters[].Required` | boolean | Whether the parameter is required. |
| `Parameters[].AllowableValues` | array | Allowed values and structure definitions for a parameter or sub-parameter. |

### 5.38 Querying the Set RAID Risk Information ActionInfo

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/bmc/Storages/Mdraid/Actions/Oem/Firefly/SetRiskInfoActionInfo` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Actual status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query the Set RAID Risk Information ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid/Actions/Oem/Firefly/SetRiskInfoActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query the Set RAID Risk Information ActionInfo with a Token"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid/Actions/Oem/Firefly/SetRiskInfoActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Path of the ActionInfo resource. |
| `@odata.type` | string | OData type of the ActionInfo or message resource. |
| `Id` | string | ActionInfo resource identifier. |
| `Name` | string | ActionInfo resource name. |
| `Parameters` | array | List of operation parameter definitions. |
| `Parameters[].DisallowedInput` | boolean | Whether input is disallowed for this parameter. |
| `Parameters[].AllowablePattern` | string | String pattern allowed for the parameter; an empty string means no pattern restriction. |
| `Parameters[].AllowableValues` | array | Allowed values and structure definitions for a parameter or sub-parameter. |
| `Parameters[].DataType` | string | Parameter data type. |
| `Parameters[].Name` | string | Parameter name. |
| `Parameters[].Required` | boolean | Whether the parameter is required. |
| `Parameters[].AllowableValues[].DisallowedInput` | boolean | Whether input is disallowed for this parameter. |
| `Parameters[].AllowableValues[].AllowablePattern` | string | String pattern allowed for the parameter; an empty string means no pattern restriction. |
| `Parameters[].AllowableValues[].DataType` | string | Parameter data type. |
| `Parameters[].AllowableValues[].Name` | string | Parameter name. |
| `Parameters[].AllowableValues[].Required` | boolean | Whether the parameter is required. |

### 5.39 Querying the Remove RAID Member Disk ActionInfo

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/RemoveDiskActionInfo` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Actual status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query the Remove RAID Member Disk ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/RemoveDiskActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query the Remove RAID Member Disk ActionInfo with a Token"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/RemoveDiskActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Path of the ActionInfo resource. |
| `@odata.type` | string | OData type of the ActionInfo or message resource. |
| `Id` | string | ActionInfo resource identifier. |
| `Name` | string | ActionInfo resource name. |
| `Parameters` | array | List of operation parameter definitions. |
| `Parameters[].DisallowedInput` | boolean | Whether input is disallowed for this parameter. |
| `Parameters[].AllowablePattern` | string | String pattern allowed for the parameter; an empty string means no pattern restriction. |
| `Parameters[].DataType` | string | Parameter data type. |
| `Parameters[].Name` | string | Parameter name. |
| `Parameters[].Required` | boolean | Whether the parameter is required. |

### 5.40 Querying the Add RAID Member Disk ActionInfo

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/AddDiskActionInfo` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Actual status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query the Add RAID Member Disk ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/AddDiskActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query the Add RAID Member Disk ActionInfo with a Token"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/AddDiskActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Path of the ActionInfo resource. |
| `@odata.type` | string | OData type of the ActionInfo or message resource. |
| `Id` | string | ActionInfo resource identifier. |
| `Name` | string | ActionInfo resource name. |
| `Parameters` | array | List of operation parameter definitions. |
| `Parameters[].DisallowedInput` | boolean | Whether input is disallowed for this parameter. |
| `Parameters[].AllowablePattern` | string | String pattern allowed for the parameter; an empty string means no pattern restriction. |
| `Parameters[].DataType` | string | Parameter data type. |
| `Parameters[].Name` | string | Parameter name. |
| `Parameters[].Required` | boolean | Whether the parameter is required. |

### 5.41 Querying the RAID Bitmap ActionInfo

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/BitmapActionInfo` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Actual status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query the RAID Bitmap ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/BitmapActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query the RAID Bitmap ActionInfo with a Token"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/BitmapActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Path of the ActionInfo resource. |
| `@odata.type` | string | OData type of the ActionInfo or message resource. |
| `Id` | string | ActionInfo resource identifier. |
| `Name` | string | ActionInfo resource name. |
| `Parameters` | array | List of operation parameter definitions. |
| `Parameters[].DisallowedInput` | boolean | Whether input is disallowed for this parameter. |
| `Parameters[].AllowablePattern` | string | String pattern allowed for the parameter; an empty string means no pattern restriction. |
| `Parameters[].AllowableValues` | array | Allowed values and structure definitions for a parameter or sub-parameter. |
| `Parameters[].DataType` | string | Parameter data type. |
| `Parameters[].Name` | string | Parameter name. |
| `Parameters[].Required` | boolean | Whether the parameter is required. |

### 5.42 Querying the Replace RAID Member Disk ActionInfo

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/ReplaceDiskActionInfo` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Actual status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query the Replace RAID Member Disk ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/ReplaceDiskActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query the Replace RAID Member Disk ActionInfo with a Token"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/ReplaceDiskActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Path of the ActionInfo resource. |
| `@odata.type` | string | OData type of the ActionInfo or message resource. |
| `Id` | string | ActionInfo resource identifier. |
| `Name` | string | ActionInfo resource name. |
| `Parameters` | array | List of operation parameter definitions. |
| `Parameters[].DisallowedInput` | boolean | Whether input is disallowed for this parameter. |
| `Parameters[].AllowablePattern` | string | String pattern allowed for the parameter; an empty string means no pattern restriction. |
| `Parameters[].DataType` | string | Parameter data type. |
| `Parameters[].Name` | string | Parameter name. |
| `Parameters[].Required` | boolean | Whether the parameter is required. |

### 5.43 Querying the RAID Reshape Current Risk ActionInfo

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/Reshape/SetCurrentRiskInfoActionInfo` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Actual status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query the RAID Reshape Current Risk ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/Reshape/SetCurrentRiskInfoActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query the RAID Reshape Current Risk ActionInfo with a Token"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/Reshape/SetCurrentRiskInfoActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Path of the ActionInfo resource. |
| `@odata.type` | string | OData type of the ActionInfo or message resource. |
| `Id` | string | ActionInfo resource identifier. |
| `Name` | string | ActionInfo resource name. |
| `Parameters` | array | List of operation parameter definitions. |
| `Parameters[].DisallowedInput` | boolean | Whether input is disallowed for this parameter. |
| `Parameters[].AllowablePattern` | string | String pattern allowed for the parameter; an empty string means no pattern restriction. |
| `Parameters[].AllowableValues` | array | Allowed values and structure definitions for a parameter or sub-parameter. |
| `Parameters[].DataType` | string | Parameter data type. |
| `Parameters[].Name` | string | Parameter name. |
| `Parameters[].Required` | boolean | Whether the parameter is required. |

### 5.44 Querying the RAID Reshape Compare ActionInfo

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/Reshape/CompareActionInfo` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Actual status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query the RAID Reshape Compare ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/Reshape/CompareActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query the RAID Reshape Compare ActionInfo with a Token"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/Reshape/CompareActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Path of the ActionInfo resource. |
| `@odata.type` | string | OData type of the ActionInfo or message resource. |
| `Id` | string | ActionInfo resource identifier. |
| `Name` | string | ActionInfo resource name. |
| `Parameters` | array | List of operation parameter definitions. |
| `Parameters[].DisallowedInput` | boolean | Whether input is disallowed for this parameter. |
| `Parameters[].AllowablePattern` | string | String pattern allowed for the parameter; an empty string means no pattern restriction. |
| `Parameters[].AllowableValues` | array | Allowed values and structure definitions for a parameter or sub-parameter. |
| `Parameters[].DataType` | string | Parameter data type. |
| `Parameters[].Name` | string | Parameter name. |
| `Parameters[].Required` | boolean | Whether the parameter is required. |

### 5.45 Querying the RAID Reshape Risk ActionInfo

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/Reshape/RiskActionInfo` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Actual status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query the RAID Reshape Risk ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/Reshape/RiskActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query the RAID Reshape Risk ActionInfo with a Token"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/Reshape/RiskActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Path of the ActionInfo resource. |
| `@odata.type` | string | OData type of the ActionInfo or message resource. |
| `Id` | string | ActionInfo resource identifier. |
| `Name` | string | ActionInfo resource name. |
| `Parameters` | array | List of operation parameter definitions. |
| `Parameters[].DisallowedInput` | boolean | Whether input is disallowed for this parameter. |
| `Parameters[].AllowablePattern` | string | String pattern allowed for the parameter; an empty string means no pattern restriction. |
| `Parameters[].AllowableValues` | array | Allowed values and structure definitions for a parameter or sub-parameter. |
| `Parameters[].DataType` | string | Parameter data type. |
| `Parameters[].Name` | string | Parameter name. |
| `Parameters[].Required` | boolean | Whether the parameter is required. |
| `Parameters[].AllowableValues[].DisallowedInput` | boolean | Whether input is disallowed for this parameter. |
| `Parameters[].AllowableValues[].AllowablePattern` | string | String pattern allowed for the parameter; an empty string means no pattern restriction. |
| `Parameters[].AllowableValues[].DataType` | string | Parameter data type. |
| `Parameters[].AllowableValues[].Name` | string | Parameter name. |
| `Parameters[].AllowableValues[].Required` | boolean | Whether the parameter is required. |

### 5.46 Querying the Execute RAID Reshape ActionInfo

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/Reshape/ExecActionInfo` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Actual status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query the Execute RAID Reshape ActionInfo"
    curl --user "<username>:<password>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/Reshape/ExecActionInfo"
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query the Execute RAID Reshape ActionInfo with a Token"
    curl --header "X-Xsrf-Token: <token>" \
      --header "Accept: application/json" \
      "<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Storages/Mdraid/Volumes/md0/Actions/Oem/Firefly/Reshape/ExecActionInfo"
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Path of the ActionInfo resource. |
| `@odata.type` | string | OData type of the ActionInfo or message resource. |
| `Id` | string | ActionInfo resource identifier. |
| `Name` | string | ActionInfo resource name. |
| `Parameters` | array | List of operation parameter definitions. |
| `Parameters[].DisallowedInput` | boolean | Whether input is disallowed for this parameter. |
| `Parameters[].AllowablePattern` | string | String pattern allowed for the parameter; an empty string means no pattern restriction. |
| `Parameters[].AllowableValues` | array | Allowed values and structure definitions for a parameter or sub-parameter. |
| `Parameters[].DataType` | string | Parameter data type. |
| `Parameters[].Name` | string | Parameter name. |
| `Parameters[].Required` | boolean | Whether the parameter is required. |
| `Parameters[].AllowableValues[].DisallowedInput` | boolean | Whether input is disallowed for this parameter. |
| `Parameters[].AllowableValues[].AllowablePattern` | string | String pattern allowed for the parameter; an empty string means no pattern restriction. |
| `Parameters[].AllowableValues[].DataType` | string | Parameter data type. |
| `Parameters[].AllowableValues[].Name` | string | Parameter name. |
| `Parameters[].AllowableValues[].Required` | boolean | Whether the parameter is required. |

## 6 Power Management Resources

### 6.1 Querying the Power Mode Parameters

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/{SystemId}/Actions/PowerModeSettingActionInfo` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Success status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query the Power Mode Parameters"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Actions/PowerModeSettingActionInfo'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query the Power Mode Parameters with a Token"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Actions/PowerModeSettingActionInfo'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Path of the power mode ActionInfo resource. |
| `@odata.type` | string | OData type of the ActionInfo resource. |
| `Id` | string | ActionInfo resource identifier. |
| `Name` | string | ActionInfo resource name. |
| `Parameters` | array | List of power mode parameter definitions. |
| `Parameters[].DisallowedInput` | boolean | Whether input is disallowed for this parameter. |
| `Parameters[].AllowablePattern` | string | Allowed string pattern. |
| `Parameters[].AllowableValues` | array | List of allowed power modes. |
| `Parameters[].DataType` | string | Parameter data type. |
| `Parameters[].Name` | string | Parameter name, currently `PowerMode`. |
| `Parameters[].Required` | boolean | Whether the parameter is required. |

### 6.2 Setting the Power Mode

Sets the CPU frequency scaling mode of the specified system. Before submitting, use the "Query the Power Mode Parameters" API to obtain the values supported by the current service.

| Item | Content |
| --- | --- |
| Method | `POST` |
| Path | `/redfish/v1/Systems/{SystemId}/Actions/ComputerSystem.PowerMode.Setting` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/Actions/PowerModeSettingActionInfo` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Required privilege | `OemPowerControl` |
| Success status code | `200 OK` |

#### Request Parameters

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `PowerMode` | string | Yes | Power mode. Allowed values are `MaximumPerformance`, `BalancedPerformance`, `PowerSaving`, `Static`, and `OSControlled`. |

| Value | Description |
| --- | --- |
| `MaximumPerformance` | Maximum performance mode. The server maps it to the CPU `Performance` governor. |
| `BalancedPerformance` | Balanced performance mode. The server maps it to the CPU `Interactive` governor. |
| `PowerSaving` | Power-saving mode. The server maps it to the CPU `Powersave` governor. |
| `Static` | Static mode. The server maps it to the CPU `Userspace` governor. |
| `OSControlled` | Operating-system-controlled mode. The server maps it to the CPU `Ondemand` governor. |

<Callout title="Running State Impact" type="warn">
  This API immediately changes the CPU frequency scaling policy of the target system and may affect performance, power consumption, and temperature. Confirm the service workload first and use the supported values returned by the ActionInfo.
</Callout>

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Set the Power Mode"
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
    ```bash title="Set the Power Mode with a Token"
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
| `Timestamp` | string | Timestamp recorded by the server when generating the response. |
| `MessageId` | string | Message identifier; `Base.1.11.0.Success` on success. |
| `Message` | string | Description of the request processing result. |
| `MessageArgs` | array | List of message formatting arguments; an empty array in this response. |
| `MessageSeverity` | string | Message severity; `OK` on success. |
| `Severity` | string | Severity field kept for compatibility; an empty string in this response. |
| `Oem` | object \| null | OEM extension message; `null` in this response. |
| `RelatedProperties` | array \| null | Property paths related to the message; `null` in this response. |
| `Resolution` | string | Recommended resolution; `None` on success. |


### 6.3 Querying the System Reset Parameters

Queries the reset operation types supported by the specified system. Different systems may return different parameter definitions, so query the ActionInfo of the target system before performing a reset.

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/{SystemId}/Actions/ResetActionInfo` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Success status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query the System Reset Parameters"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/sub01/Actions/ResetActionInfo'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query the System Reset Parameters with a Token"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/sub01/Actions/ResetActionInfo'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Path of the system reset ActionInfo resource. |
| `@odata.type` | string | OData type of the ActionInfo resource. |
| `Id` | string | ActionInfo resource identifier. |
| `Name` | string | ActionInfo resource name. |
| `Parameters` | array | List of reset parameter definitions. |
| `Parameters[].DisallowedInput` | boolean | Whether input is disallowed for this parameter. |
| `Parameters[].AllowablePattern` | string | Allowed string pattern; an empty string means no pattern restriction. |
| `Parameters[].AllowableValues` | array | List of reset types supported by the target system. |
| `Parameters[].DataType` | string | Parameter data type. |
| `Parameters[].Name` | string | Request field name, currently `ResetType` for sub-board systems. |
| `Parameters[].Required` | boolean | Whether the parameter is required. |

### 6.4 Resetting the System

Performs a power-on, shutdown, restart, or power cycle operation on the specified system according to `ResetType`.

| Item | Content |
| --- | --- |
| Method | `POST` |
| Path | `/redfish/v1/Systems/{SystemId}/Actions/ComputerSystem.Reset` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/Actions/ResetActionInfo` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Required privilege | `OemPowerControl` |
| Success status code | `200 OK` |

#### Request Parameters

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `ResetType` | string | Yes | Reset type for sub-board systems. The actual allowed values are subject to the Reset ActionInfo of the target system. |

| Value | Description |
| --- | --- |
| `On` | Powers on a system that is off; when the system is already on, it usually stays in the current state. |
| `ForceOn` | Forces the system power on. |
| `ForceOff` | Forces the system power off, which may cause unsaved data to be lost. |
| `ForceRestart` | Forces a system restart. |
| `GracefulRestart` | Requests a graceful operating system restart. |
| `GracefulShutdown` | Requests a graceful operating system shutdown. |
| `PowerCycle` | Powers off and then powers on again. |
| `LoaderByHardware` | Enters the Loader through hardware. |

<Callout title="Service Interruption Risk" type="warn">
  `ForceOff`, `ForceRestart`, `GracefulRestart`, `GracefulShutdown`, `PowerCycle`, and `LoaderByHardware` may interrupt services. Before executing, confirm the target `SystemId`, save service data, and reserve a recovery window.
</Callout>

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Power On the System"
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
    ```bash title="Power On the System with a Token"
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
| `Timestamp` | string | Timestamp recorded by the server when generating the response. |
| `MessageId` | string | Message identifier; `Base.1.11.0.Success` on success. |
| `Message` | string | Description of the request processing result. |
| `MessageArgs` | array | List of message formatting arguments; an empty array in this response. |
| `MessageSeverity` | string | Message severity; `OK` on success. |
| `Severity` | string | Severity field kept for compatibility; an empty string in this response. |
| `Oem` | object \| null | OEM extension message; `null` in this response. |
| `RelatedProperties` | array \| null | Property paths related to the message; `null` in this response. |
| `Resolution` | string | Recommended resolution; `None` on success. |

<Callout title="Parameter Differences for the BMC Target" type="info">
  The parameter name in the Reset ActionInfo that the current server returns for `bmc` is `GracefulRestart`, not the `ResetType` used by sub-boards. When calling `bmc`, follow its ActionInfo and submit `{"GracefulRestart":"GracefulRestart"}`; the server returns a success message, but the implementation does not actually restart the BMC. Submitting `ResetType` directly to `bmc` returns `400 PropertyUnknown`.
</Callout>


### 6.5 Querying the Power-On Policy Parameters

Queries the request parameters, required status, and allowed values of the set power-on policy API.

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/{SystemId}/Actions/SetPowerConfig.ActionInfo` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Success status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query the Power-On Policy Parameters"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/sub01/Actions/SetPowerConfig.ActionInfo'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query the Power-On Policy Parameters with a Token"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/sub01/Actions/SetPowerConfig.ActionInfo'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Path of the power-on policy ActionInfo resource. |
| `@odata.type` | string | OData type of the ActionInfo resource. |
| `Id` | string | ActionInfo resource identifier. |
| `Name` | string | ActionInfo resource name. |
| `Parameters` | array | List of power-on policy parameter definitions. |
| `Parameters[].DisallowedInput` | boolean | Whether input is disallowed for this parameter. |
| `Parameters[].AllowablePattern` | string | Allowed string pattern; an empty string means no pattern restriction. |
| `Parameters[].AllowableValues` | array | List of allowed values for a string parameter; only `PowerRestorePolicy` returns this field. |
| `Parameters[].DataType` | string | Parameter data type, which can be `Number` or `String`. |
| `Parameters[].Name` | string | Request field name. |
| `Parameters[].Required` | boolean | Whether the parameter is required. |

### 6.6 Setting the Power-On Policy

Sets the startup policy and power-on delay for the sub-board after power is restored.

| Item | Content |
| --- | --- |
| Method | `POST` |
| Path | `/redfish/v1/Systems/{SystemId}/Actions/SetPowerConfig` |
| ActionInfo | `/redfish/v1/Systems/{SystemId}/Actions/SetPowerConfig.ActionInfo` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Required privilege | `OemPowerControl` |
| Success status code | `200 OK` |

#### Request Parameters

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `PowerRestorePolicy` | string | Yes | Power restore policy. Allowed values are `AlwaysOn`, `AlwaysOff`, and `LastState`. |
| `PowerOnDelaySeconds` | integer | No | Number of seconds to delay power-on after power is restored. If omitted, the Go server treats it as the integer zero value `0`. |

| `PowerRestorePolicy` Value | Description |
| --- | --- |
| `AlwaysOn` | Automatically starts the sub-board after power is restored. |
| `AlwaysOff` | Stays powered off after power is restored. |
| `LastState` | Restores the power state from before the power loss after power is restored. |

<Callout title="Configuration Impact" type="warn">
  This API writes the sub-board runtime power configuration and affects subsequent power-loss recovery behavior. It is recommended to first query `PowerRestorePolicy` and `PowerOnDelaySeconds` in the system resources and confirm the target `SystemId` before submitting. `bmc` does not support this operation.
</Callout>

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Set the Power-On Policy"
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
    ```bash title="Set the Power-On Policy with a Token"
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
| `Timestamp` | string | Timestamp recorded by the server when generating the response. |
| `MessageId` | string | Message identifier; `Base.1.11.0.Success` on success. |
| `Message` | string | Description of the request processing result. |
| `MessageArgs` | array | List of message formatting arguments; an empty array in this response. |
| `MessageSeverity` | string | Message severity; `OK` on success. |
| `Severity` | string | Severity field kept for compatibility; an empty string in this response. |
| `Oem` | object \| null | OEM extension message; `null` in this response. |
| `RelatedProperties` | array \| null | Property paths related to the message; `null` in this response. |
| `Resolution` | string | Recommended resolution; `None` on success. |


## 7 Firewall Management Resources

This section describes the OEM port mapping resources of the BMC. Port mapping supports port redirection, port forwarding, and IP mapping, and provides a connectivity testing operation.

<Callout title="BMC Only" type="info">
  The APIs in this section uniformly use the system identifier `bmc`. The server-side implementation of the port mapping testing operation also rejects non-BMC systems.
</Callout>

### 7.1 Querying Port Mappings

Queries all port mapping rules currently saved on the BMC, as well as the entry points of the configuration and testing operations.

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/bmc/Oem/Firefly/PortMappings` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Success status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query Port Mappings"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Oem/Firefly/PortMappings'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query Port Mappings with a Token"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Oem/Firefly/PortMappings'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Path of the port mapping resource. |
| `@odata.type` | string | OData type of the port mapping resource. |
| `Id` | string | Resource identifier. |
| `Name` | string | Resource name. |
| `Actions` | object | Set of operations supported by the resource. |
| `Actions.#PortMappings.Configure` | object | Port mapping configuration operation. |
| `Actions.#PortMappings.Configure.target` | string | POST path of the configuration operation. |
| `Actions.#PortMappings.Configure.@Redfish.ActionInfo` | string | ActionInfo path of the configuration operation. |
| `Actions.#PortMappings.Testing` | object | Port mapping testing operation. |
| `Actions.#PortMappings.Testing.target` | string | POST path of the testing operation. |
| `Actions.#PortMappings.Testing.@Redfish.ActionInfo` | string | ActionInfo path of the testing operation. |
| `Oem` | object | OEM extension object. |
| `Oem.Firefly` | object | Firefly OEM extension object. |
| `Oem.Firefly.PortMappings` | array | List of port mapping rules currently saved. |
| `Oem.Firefly.PortMappings[].external_ip` | string | External IP; may be empty in port mapping or local redirect scenarios. |
| `Oem.Firefly.PortMappings[].external_port` | integer | External port, in the range `0` to `65535`. |
| `Oem.Firefly.PortMappings[].protocol` | string | Protocol type: `tcp`, `udp`, or `all` in IP mapping scenarios. |
| `Oem.Firefly.PortMappings[].internal_ip` | string | Internal target IP; may be empty in local port redirect scenarios. |
| `Oem.Firefly.PortMappings[].internal_port` | integer | Internal target port, in the range `0` to `65535`. |
| `Oem.Firefly.PortMappings[].service_name` | string | Rule name, which is also the rule identifier. |
| `Oem.Firefly.PortMappings[].status` | boolean | Whether the rule is enabled. `false` means the rule is saved but no forwarding rule is generated. |
| `Oem.Firefly.PortMappings[].RedirectUrl` | string | Optional redirect URL field. The first letter of the field name is uppercase. |

### 7.2 Querying the Port Mapping Configuration Parameters

Queries the request structure, field types, required status, and protocol allowed values of the configuration operation.

| Item | Content |
| --- | --- |
| Method | `GET` |
| Path | `/redfish/v1/Systems/bmc/Oem/Firefly/PortMappings/Actions/ConfigureActionInfo` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Success status code | `200 OK` |

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Query the Port Mapping Configuration Parameters"
    curl --user '<username>:<password>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Oem/Firefly/PortMappings/Actions/ConfigureActionInfo'
    ```
  </CodeBlockTab>

  <CodeBlockTab value="token">
    ```bash title="Query the Port Mapping Configuration Parameters with a Token"
    curl --header 'X-Xsrf-Token: <token>' \
      --header 'Accept: application/json' \
      '<protocol>://<device-ip>:<port>/redfish/v1/Systems/bmc/Oem/Firefly/PortMappings/Actions/ConfigureActionInfo'
    ```
  </CodeBlockTab>
</CodeBlockTabs>

#### Response Example

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

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `@odata.id` | string | Path of the configuration operation ActionInfo resource. |
| `@odata.type` | string | OData type of the ActionInfo resource. |
| `Id` | string | ActionInfo resource identifier. |
| `Name` | string | ActionInfo resource name. |
| `Parameters` | array | List of operation parameter definitions. |
| `Parameters[].DisallowedInput` | boolean | Whether input is disallowed for the main parameter. |
| `Parameters[].AllowablePattern` | string | Input pattern allowed for the main parameter. |
| `Parameters[].AllowableValues` | array | List of field definitions for elements of the `PortMappings` array. |
| `Parameters[].AllowableValues[].DisallowedInput` | boolean | Whether input is disallowed for the sub-field. |
| `Parameters[].AllowableValues[].AllowablePattern` | string | Input pattern allowed for the sub-field. |
| `Parameters[].AllowableValues[].AllowableValues` | array | Allowed values for the sub-field; currently only `protocol` returns `tcp`, `udp`, and `all`. |
| `Parameters[].AllowableValues[].DataType` | string | Sub-field data type. |
| `Parameters[].AllowableValues[].Name` | string | Sub-field name. |
| `Parameters[].AllowableValues[].Required` | boolean | Whether the sub-field is required. |
| `Parameters[].DataType` | string | Data type of the main parameter, currently the object array `ObjectArray`. |
| `Parameters[].Name` | string | Name of the main parameter, currently `PortMappings`. |
| `Parameters[].Required` | boolean | Whether the main parameter is required. |

### 7.3 Configuring Port Mappings

Replaces the current port mapping configuration of the BMC with the complete `PortMappings` array in the request.

| Item | Content |
| --- | --- |
| Method | `POST` |
| Path | `/redfish/v1/Systems/bmc/Oem/Firefly/PortMappings/Actions/Configure` |
| ActionInfo | `/redfish/v1/Systems/bmc/Oem/Firefly/PortMappings/Actions/ConfigureActionInfo` |
| Authentication | HTTP Basic Auth or `X-Xsrf-Token` |
| Required privilege | `ConfigureComponents` |
| Success status code | `200 OK` |

#### Request Parameters

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `PortMappings` | array | Yes | Complete list of port mapping rules to save. This array replaces the existing list. |
| `PortMappings[].external_ip` | string | No | External IP. An empty string can be passed in port forwarding and port redirect scenarios. |
| `PortMappings[].external_port` | integer | No | External port, in the range `0` to `65535`. Use `0` in IP mapping scenarios. |
| `PortMappings[].protocol` | string | Yes | Protocol. Port mapping uses `tcp` or `udp`; only pure IP mapping can use `all`. |
| `PortMappings[].internal_ip` | string | No | Internal target IP. Pass an empty string in local port redirect scenarios. |
| `PortMappings[].internal_port` | integer | No | Internal target port, in the range `0` to `65535`. Use `0` in IP mapping scenarios. |
| `PortMappings[].service_name` | string | Yes | Non-empty rule name. |
| `PortMappings[].status` | boolean | Yes | Whether the rule is enabled. |
| `PortMappings[].RedirectUrl` | string | No | Optional redirect URL. Note that the first letter of the field name is uppercase. |

<Callout title="Overwriting Configuration" type="warn">
  This API does not append a single rule; it replaces the entire port mapping list. Before submitting, query the existing `Oem.Firefly.PortMappings` and include the rules to keep in the request. Submitting an empty array clears all port mapping configuration.
</Callout>

<CodeBlockTabs defaultValue="basic-auth">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="basic-auth">
    ```bash title="Configure Port Mappings"
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
    ```bash title="Configure Port Mappings with a Token"
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
| `Timestamp` | string | Timestamp recorded by the server when generating the response. |
| `MessageId` | string | Message identifier; `Base.1.11.0.Success` on success. |
| `Message` | string | Description of the request processing result. |
| `MessageArgs` | array | List of message formatting arguments; an empty array in this response. |
| `MessageSeverity` | string | Message severity; `OK` on success. |
| `Severity` | string | Severity field kept for compatibility; an empty string in this response. |
| `Oem` | object \| null | OEM extension message; `null` in this response. |
| `RelatedProperties` | array \| null | Property paths related to the message; `null` in this response. |
| `Resolution` | string | Recommended resolution; `None` on success. |
