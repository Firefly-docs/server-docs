# Redfish API Overview

The Redfish API is the resource management interface exposed by the BMC. Clients access unified resource paths over HTTP or HTTPS, use JSON to read device status and query resource relationships, and perform configuration, control, and maintenance operations.

This document describes the interfaces currently implemented by the Firefly BMC backend. Interface paths, fields, and behaviors are subject to the backend implementation of the corresponding version and the actual device responses.

<Callout title="Recommended Reading Order" type="info">
  When integrating for the first time, it is recommended to read "General Interface Information", "Usage", and "Basic Resources" in order before moving on to the specific resource chapters.
</Callout>

## 1 Resource Model

Redfish organizes BMC capabilities as discoverable resources. Each resource is identified by a fixed URL, and the `@odata.id` in responses points to the current resource or related resources.

### 1.1 Service Entry Points

Clients typically start resource discovery from the following two entry points:

| Path | Purpose |
| --- | --- |
| `/redfish` | Queries the entry point for the major Redfish versions supported by the service. |
| `/redfish/v1` | Queries the Service Root, as well as entry points for systems, management, users, OEM, and other resources. |

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

<Callout title="Follow the Actual Entry Points" type="info">
  Resource entry points in the Service Root are registered by the backend according to device capabilities. Different products, board states, or software versions may return different entry points. Clients should follow the `@odata.id` values in responses first and should not construct resource paths that were not returned.
</Callout>

### 1.2 Resources, Collections, and Actions

The interfaces fall into three main categories:

| Type | Description | Common Forms |
| --- | --- | --- |
| Resource collection | Returns the member list and count of resources of the same type. | `Members`, `Members@odata.count` |
| Single resource | Returns the complete information of a specified device, service, or configuration object. | `/Systems/{id}` |
| Action interface | Performs configuration, start/stop, restart, deletion, or other actions. | `Actions`, `ActionInfo`, POST |

The `Actions` in a resource response describe the available operations, and `@Redfish.ActionInfo` points to the parameter definition of an operation. Before calling a write operation, review the request fields, allowed values, and scope of impact in the corresponding chapter.

## 2 Document Scope

### 2.1 Resource Chapters

This manual organizes interfaces by business resource:

| Chapter | Content |
| --- | --- |
| [Basic Resources](basicResources.md) | Redfish version entry point and Service Root. |
| [Chassis Resources](chassisResources.md) | Chassis status, fans, UID, and overall power management. |
| [Systems Resources](systemResources.md) | System status, network, time, disks, power, port mapping, and service management. |
| [Manage Resources](managerResources.md) | BMC management resources and log resources. |
| [OEM Resources](oemResources.md) | Firefly extension capabilities such as remote management and alerts. |
| [Users Resources](accountResources.md) | Roles, accounts, and user service management. |
| [Switchs Resources](switchResources.md) | Switch Layer 2, Layer 3, VLAN, and aggregation management. |
| [Upgrades Management Resources](upgradesResources.md) | Firmware query, upgrade status, and upgrade operations. |

### 2.2 Relationship Between Implementation and Standard

The current interfaces follow the Redfish resource, OData metadata, and Message response structures, while also including Firefly OEM fields and product capability extensions. Therefore:

- The existence of an interface should not be inferred solely from the Redfish standard version.
- Implementation information in `Oem`, `Actions`, or `ActionInfo` should not be ignored.
- The identifiers, quantities, statuses, and optional parameters of the same interface may differ across devices.
- The response examples in this document illustrate the real structure and do not mean that all devices will return the same business values.

## 3 Integration Principles

### 3.1 Authentication and Transport

Protected interfaces support HTTP Basic Auth or session tokens. The token is carried in the `X-Xsrf-Token` request header and is not placed in query parameters or business request bodies.

The service can be configured for HTTP or HTTPS. This document uniformly uses the following address format:

```text
<protocol>://<device-ip>:<port>
```

where `<protocol>` is `http` or `https` depending on the device configuration. The port number alone cannot be used to determine the protocol.

### 3.2 Operational Safety

Before performing write operations such as power, network, account, upgrade, or deletion operations, first query the current resource and the corresponding `ActionInfo` to confirm the target resource, required fields, and allowed values. Network configuration may interrupt the current connection, and upgrade and power operations may affect business operation; they should be performed during a maintenance window.

<Callout title="Do Not Store Credentials in Documents or Logs" type="warn">
  The usernames, passwords, and tokens in the examples are placeholders. Real passwords and tokens should not be written into documents, command history, logs, or version control repositories.
</Callout>
