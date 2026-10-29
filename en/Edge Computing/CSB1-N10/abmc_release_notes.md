# aBMC Release Notes

Records the main changes in each aBMC release, newest first.

## Version Overview

| Version | Highlights | Package |
| --- | --- | --- |
| [v3.2.3](#v323) | Web session auto-renewal; switch serial terminal tolerance | [Baidu Pan](https://pan.baidu.com/s/15kfthwxS8cbRJxC_tXggCA?pwd=jctu) (code: `jctu`) |
| [v3.2.2](#v322) | Sub-board chip platform detection; mount point remount; port speed auto-negotiation | [Baidu Pan](https://pan.baidu.com/s/1mFNqm3gXe9CeEJ3ujO3mhw?pwd=1kci) (code: `1kci`) |
| [v3.2.1](#v321) | Standalone SSH user management removed; unified Web terminal; upgrade required | [Baidu Pan](https://pan.baidu.com/s/1F1mJ6wmn9B6z0UKj45rj6Q?pwd=5m2m) (code: `5m2m`) |
| [v3.2.0](#v320) | Core board communication management; ADB stability; QCOM8550 support | [Baidu Pan](https://pan.baidu.com/s/15WEKF-Dg_fqMqY25EMcBug?pwd=pr2s) (code: `pr2s`) |
| [v3.1.4](#v314) | New L3 switch management; microservice self-healing | [Baidu Pan](https://pan.baidu.com/s/198pJA1OSX4pzWlbGNeX46Q?pwd=bjv5) (code: `bjv5`) |
| [v3.1.3](#v313) | Fixes for C2, CSA1, and NVIDIA Orin | [Baidu Pan](https://pan.baidu.com/s/1w_4FhcD-6h2f9KiBc5vpGQ?pwd=9j2t) (code: `9j2t`) |
| [v3.1.2](#v312) | IPv6 support; root account removed; time management fixes | [Baidu Pan](https://pan.baidu.com/s/1mgV_QLyKxWD9n-YOuOJ5cw?pwd=q27j) (code: `q27j`) |
| [v1.0](#v10) | First aBMC release with a Web UI and basic out-of-band management | — |

## v3.2.3

Web session auto-renewal; switch serial terminal tolerance

**Added**

- Web session auto-renewal: the Access Token is valid for 2 hours and the Refresh Token for 12 hours. When less than 10 minutes remain, a new token is issued with a business response, so users can keep working without logging in again.

**Fixed**

- The L3 switch serial terminal now fails only when the serial port is inaccessible; other abnormal states still allow entering the terminal for troubleshooting.

**API**

- Added the token refresh endpoint `POST /redfish/v1/token/refresh`; the login response now includes `accessToken` and `refreshToken`.

## v3.2.2

Sub-board chip platform detection; mount point remount; port speed auto-negotiation

**Added**

- Automatic detection and manual specification of the sub-board chip platform (the sub-board may reboot briefly during detection).
- File share mount points can be remounted without deleting and recreating them.
- Switch ports and trunks support `auto` speed negotiation.

**Fixed**

- The first UID button press after BMC startup had no effect; the BM1684X switch serial terminal could not be opened.
- NVIDIA sub-board firmware upgrade failed on GS1-N2 (RK3576); the BM1684X NIC naming rule contained an error.

**API**

- Added endpoints for chip type query, probe, and manual specification, plus mount point remount.

## v3.2.1

**Upgrade required** · Standalone SSH user management removed; ShareDisk default mount removed

<Callout title="Incompatible changes" type="warn">
  This release removes the standalone SSH user management system and disables the default ShareDisk mount/NFS export. Confirm that related services are adjusted before upgrading.
</Callout>

**Incompatible**

- Removed the standalone SSH user management system. SSH credentials are now provided by the core board communication configuration; all previous SSH user APIs have been removed.
- ShareDisk is no longer mounted to the sub-board `/media/NFS` by default and is no longer exported through NFS by default. Existing mount records fail once on the first restore after the upgrade and can be deleted manually.

**Improved**

- Unified Web terminal entry: ADB, SSH, and Shell terminal credentials are taken from the current communication configuration, and terminal types are calculated from the actual state.

**Fixed**

- Sub-board status was not refreshed after a forced power-off; the CSC2 switch port topology mapping was incorrect.

## v3.2.0

**Major release** · Core board communication management; ADB stability; QCOM8550 support

**Added**

- Core board communication management: custom SSH, connectivity testing, switching back to the default method at any time, and automatic SSH reconnection.
- Improved Android sub-board ADB stability (adbd watchdog); added temperature collection for the Qualcomm QCOM8550 platform.

**Fixed**

- Sub-board offline status was not refreshed; SoC model misidentification; Bx sub-board default gateway error.
- 5G NIC MAC addresses were unstable after reboot; the time management NTP default value was not retained.

**API**

- Added query, create, test, enable, and delete endpoints for core board communication configurations.

## v3.1.4

**Major release** · New L3 switch management; microservice self-healing

**Added**

- New L3 switch management: topology, ports, VLAN/SVI, trunk (link aggregation), and a Web console.
- Microservice self-healing: core board microservices can recover automatically after an abnormal exit.

**Fixed**

- 4Kn drive partition errors, occasional crashes during storage information refresh, and persistent mount recovery failures.
- Sophgo memory information collection and CS_B1 serial expansion chip binding.

**API**

- Added switch topology, port, VLAN, trunk, and Web terminal endpoints.

## v3.1.3

Fixes for C2, CSA1, and NVIDIA Orin

**Fixed**

- The C2 power LED incorrectly turned red at boot.
- The C2 front panel sub-board status mapping (online, offline, upgrading).
- SATA drive LED compatibility for CSA1 v1.1 hardware.
- NVIDIA Orin series storage capacity is now read from the actual NVMe device.

## v3.1.2

IPv6 support; root account removed; time management fixes

**Added**

- Full IPv6 support for the BMC network: physical interfaces, VLANs, and bonds; dual-stack or IPv6-only; DHCPv6 or static addresses, gateways, routes, and DNS.

**Changed**

- Removed the default root account; only the admin account remains. The admin account is no longer a protected reserved account.

**Fixed**

- The default time zone was incorrect; time zone save/restore failed; time service configuration was not reapplied after a restart.
- The C2 front panel screen refresh interval was incorrect.

**API**

- Added IPv6 fields to network configuration and query responses; generic responses no longer include `Id` or `Name`.

## v1.0

**First release** · aBMC first release with a Web UI and basic out-of-band management

**Added**

- aBMC first release, providing a Web management interface and basic out-of-band management capabilities.