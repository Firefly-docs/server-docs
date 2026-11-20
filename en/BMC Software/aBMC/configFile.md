# Configuration File Reference

## Introduction

`config.ini` is the main configuration file of the aBMC service. It centralizes startup configuration such as web service listening, log output, firmware upgrade, the sub-board private network segment, and the Redis connection. The configuration is loaded once and strictly validated when the aBMC service starts; changes take effect only after the service is restarted.

The configuration file is divided into six sections by function:

| Section | Description |
| --- | --- |
| `[Server]` | Listening configuration for the main web management / Redfish API service and the webhook service |
| `[Log]` | Log level and console output configuration |
| `[Update]` | Firmware upgrade directory, timeout, and platform switches |
| `[CSA1]` | Private network segment used for communication between the BMC and SOPHGO sub-boards |
| `[Redis]` | Connection configuration for the Redis dependency |
| `[ADB]` | Deprecated; its configuration items have no effect and are not documented in detail |

<Callout title="Configuration file location and permissions" type="info">
The configuration file is located at `/var/firefly-aBMC/config/config.ini` with permissions `0600` (readable and writable only by root). Use `sudo` when viewing or modifying it with a non-root account such as bmc.
</Callout>

## Configuration File Management

### The configuration is automatically maintained by aBMC [step]

The configuration file header is marked `This file is managed by aBMC.`, indicating that the file is automatically maintained by aBMC at startup:

1. If the configuration file does not exist, aBMC creates it automatically with built-in default values.
2. Missing sections and configuration items are added: items that are not explicitly configured are shown as comments with their default values; uncomment an item to modify its value manually.
3. Unrecognized sections and configuration items are removed, including configuration that was removed in earlier versions.
4. Legacy syntax is normalized: colon-prefixed port values from earlier versions (for example `HttpPort = :443`) are rewritten to `443`.
5. File permissions are tightened to `0600`, and the configuration directory to `0750`.

### Configuration value syntax [step]

| Format | Description |
| --- | --- |
| `key = value` | White space around the equals sign is optional; comments start with `;` or `#`. |
| Boolean | Only lowercase `true` or `false` is accepted; any other spelling causes startup failure. |
| Numeric | Parsed as a base-10 integer. |
| String | Quotes are usually unnecessary; when the value contains leading or trailing spaces, quotes, or other special characters, wrap it in double quotes (supports `\` escapes) or single quotes (only handles `\'`). |
| Empty value | Leaving the right side empty indicates an explicit empty value, for example `Password =` for an empty password. |

Duplicate sections or duplicate keys are not allowed within the same file.

### Applying configuration changes [step]

1. Modify the configuration and save it.
2. Restart the aBMC service:

    ```bash
    sudo systemctl restart aBMC
    ```

## Configuration Items

### [Server] Service Configuration

| Item | Default | Description |
| --- | --- | --- |
| Host | `0.0.0.0` | Listening address for the main service and the webhook service; accepts an IP address or a domain name. |
| HttpPort | `443` | Listening port of the main service, which serves the web management page and the Redfish API; when HTTPS is enabled, this port is the HTTPS port. Valid range `1–65535`. |
| HttpsMode | `true` | Whether the main service is provided over HTTPS. |
| WebHookPort | `23456` | Listening port of the webhook service. This service is independent of the main service and receives Prometheus Alertmanager alerts, then forwards them as WeChat Work and DingTalk robot messages. |
| WebShow | `true` | Whether the web management frontend is provided. When set to `false`, visiting the home page returns a blank page while the Redfish API is unaffected; suitable for API-only integration scenarios. |

### [Log] Configuration

| Item | Default | Description |
| --- | --- | --- |
| EnableConsole | `true` | Whether internal event logs are also written to the console. |
| MinLevel | `info` | Minimum log level; one of `debug`, `info`, `warn`, or `error`. |

<Callout title="Note" type="info">
A lower log level produces more verbose output. `debug` significantly increases the log volume and is generally enabled only temporarily for troubleshooting.
</Callout>

### [Update] Firmware Upgrade Configuration

| Item | Default | Description |
| --- | --- | --- |
| UpdateImgPath | `/userdata/aBMC/Firmware` | Directory for uploading and storing firmware images; images uploaded through the web/API are stored here. |
| UpdateTimeoutMinute | `20` | Timeout for a single firmware upgrade, in minutes. |
| SupportRockchip | `true` | Controls whether Rockchip is offered in the selectable platform list of the upgrade interface. |
| SupportSophgo | `true` | Controls whether Sophgo is offered in the selectable platform list of the upgrade interface. |
| SupportNovauto | `true` | Controls whether Novauto is offered in the selectable platform list of the upgrade interface. |
| SupportQualcomm | `true` | Controls whether Qualcomm is offered in the selectable platform list of the upgrade interface. |
| SupportNvidia | `true` | Controls whether NVIDIA is offered in the selectable platform list of the upgrade interface. |
| SupportSpacemit | `true` | Controls whether Spacemit is offered in the selectable platform list of the upgrade interface. |

### [CSA1] Sub-Board Private Network

| Item | Default | Description |
| --- | --- | --- |
| PrivateIpv4CIDR | `192.168.150.100/24` | Private network segment used for communication between the BMC and sub-boards of SOPHGO models such as CSA1 and B1/B2_BM1684X. |

Usage rules for this configuration:

1. The value must be a valid IPv4 CIDR in host-address form, for example `192.168.150.100/24`, rather than the network-address form `192.168.150.0/24`.
2. The BMC-side private address is the host address within the CIDR, `192.168.150.100` by default; sub-board private addresses are derived from the core index as `index + 20`, so `sub01` maps to `192.168.150.21` by default.
3. This network segment is used for the BMC private interface configuration, sub-board private IP allocation, and SSH communication.
4. The GS1-N2 model does not use this configuration; its BMC communicates with sub-boards over the fixed `172.22.250.0/16` network.

### [Redis] Configuration

aBMC uses Redis for internal event publish/subscribe and data caching; a Redis instance is built into the system by default.

| Item | Default | Description |
| --- | --- | --- |
| Host | `localhost` | Redis server address; accepts an IP address or a domain name. |
| Port | `6379` | Redis service port. Valid range `1–65535`. |
| Password | empty | Redis authentication password; leave it empty (`Password =`) when there is no password. |
| DB | `14` | Redis database number to use. Valid range `0–15`. |

<Callout title="Warning" type="warn">
These are the client parameters aBMC uses to connect to Redis. When changing the port, password, or database number, make sure the Redis server is updated with the same configuration; otherwise aBMC fails to connect at startup or during runtime.
</Callout>

## FAQ

### 1. aBMC fails to start after the configuration is modified

The configuration is strictly validated at startup. Any invalid value (out-of-range port, misspelled boolean, invalid log level, malformed IPv4 CIDR, and so on) causes startup failure.

### 2. Custom configuration items disappear after a restart

The main configuration file is managed by aBMC. Sections and configuration items that aBMC does not recognize are removed automatically at startup.

### 3. The service malfunctions after changing the Redis port or password

The `[Redis]` configuration holds the client parameters aBMC uses to connect to Redis and must match the actual Redis server configuration. After making changes, confirm that the Redis server uses the same port and password, that the database number is within `0–15`, and then restart aBMC.

### 4. Why are some configuration items commented out

Items that are not explicitly configured are shown as comments with their default values. To change such an item, uncomment it first and then modify its value.
