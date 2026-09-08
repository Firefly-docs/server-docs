# Redfish API

The Redfish API is the resource management interface exposed by the BMC. Clients access unified resource paths over HTTP or HTTPS, use JSON to read device status, query resource relationships, and perform configuration, control, and maintenance operations.

This document describes the interfaces currently implemented by the Firefly BMC backend. The interface paths, fields, and behavior are subject to the backend implementation of the corresponding version and the actual device responses.

<Callout title="Recommended Reading Order" type="info">
  For first-time integration, it is recommended to read "General Interface Notes", "Usage", and "Basic Resources" in order, and then proceed to the specific resource chapters.
</Callout>

## 1 Resource Model

Redfish organizes BMC capabilities into discoverable resources. Each resource is identified by a fixed URL, and the `@odata.id` in the response points to the current resource or a related resource.

### 1.1 Service Entry Points

Clients usually start discovering resources from the following two entry points:

| Path | Purpose |
| --- | --- |
| `/redfish` | Query the entry point for the major Redfish version supported by the service. |
| `/redfish/v1` | Query the Service Root and the entry points for resources such as systems, managers, users, and OEM. |

A typical access sequence is as follows:

```text
/redfish
└── /redfish/v1
    ├── /redfish/v1/Systems
    ├── /redfish/v1/Managers
    ├── /redfish/v1/AccountService
    ├── /redfish/v1/Oem
    ├── /redfish/v1/Switch
    └── /redfish/v1/UpdateFwService
```

<Callout title="Subject to the Actual Entry Points" type="info">
  The resource entry points in the Service Root are registered by the backend according to the device capabilities. Different products, board states, or software versions may return different entry points. Clients should prefer to follow the `@odata.id` in the response and should not construct resource paths that were not returned.
</Callout>

### 1.2 Resources, Collections, and Operations

The interfaces are mainly divided into three categories:

| Type | Description | Common Forms |
| --- | --- | --- |
| Resource collection | Returns the member list and count of resources of the same type. | `Members`, `Members@odata.count` |
| Single resource | Returns the complete information of a specified device, service, or configuration object. | `/Systems/{id}` |
| Operation interface | Performs configuration, start/stop, restart, deletion, or other actions. | `Actions`, `ActionInfo`, POST |

The `Actions` in a resource response describes the executable operations, and `@Redfish.ActionInfo` points to the parameter definition of the operation. Before calling a write operation, check the request fields, allowed values, and impact scope in the corresponding chapter.

## 2 Document Scope

### 2.1 Resource Chapters

This manual organizes the interfaces by business resource:

| Chapter | Contents |
| --- | --- |
| [Basic Resources](basicResources.md) | Redfish version entry point and Service Root. |
| [Chassis Resources](chassisResources.md) | Chassis status, fans, UID, and total power management. |
| [Systems Resources](systemResources.md) | System status, network, time, disks, power, port mapping, and service management. |
| [Manager Resources](managerResources.md) | BMC management resources and log resources. |
| [OEM Resources](oemResources.md) | Firefly extension capabilities such as remote management and alarms. |
| [Users Resources](accountResources.md) | Roles, accounts, and user service management. |
| [Switch Resources](switchResources.md) | Switch Layer 2, Layer 3, VLAN, and aggregation management. |
| [Upgrade Management Resources](upgradesResources.md) | Firmware query, upgrade status, and upgrade operations. |

### 2.2 Relationship Between the Implementation and the Standard

The current interfaces follow the Redfish resource, OData metadata, and Message response structures, while also including Firefly OEM fields and product capability extensions. Therefore:

- Do not assume that a certain interface definitely exists based only on the Redfish standard version.
- Do not ignore the implementation information in `Oem`, `Actions`, or `ActionInfo`.
- The same interface may have different identifiers, quantities, states, and optional parameters on different devices.
- The response examples in the documentation are used to illustrate the actual structure and do not mean that all devices will return the same business values.

## 3 Integration Principles

### 3.1 Authentication and Transport

Protected interfaces support HTTP Basic Auth or session tokens. The token is carried in the `X-Xsrf-Token` request header and must not be placed in query parameters or business request bodies.

The service can be configured to use HTTP or HTTPS. This document uniformly uses the following address format:

```text
<protocol>://<device-ip>:<port>
```

Where `<protocol>` is `http` or `https` depending on the device configuration. The port number alone cannot be used to determine the protocol.

### 3.2 Operation Safety

Before performing write operations such as power, network, account, upgrade, and deletion, first query the current resource and the corresponding `ActionInfo` to confirm the target resource, required fields, and allowed values. Network configuration changes may interrupt the current connection, and upgrade and power operations may affect business operation; they should be performed during a maintenance window.

<Callout title="Do Not Store Credentials in Documents or Logs" type="warn">
  The usernames, passwords, and tokens in the examples are all placeholders. Real passwords and tokens must not be written into documents, command history, logs, or version control repositories.
</Callout>
## Detailed Documentation

For the resource paths, request parameters, response structures, and call examples of the Redfish API, please refer to the [aBMC Redfish API User Manual](/docs/server/bmc-software/RedfishAPI/oiverview).