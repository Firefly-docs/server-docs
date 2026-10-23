# One NIC for Multiple Subnets (VLAN Segmentation + Internal L3 Switch)

## Overview

This chapter describes how the server uses the VLAN features of its internal Layer 3 switch to make one physical NIC serve as two physical NICs.

## Intended Audience

This case is intended for:

- **Network administrators**: plan the VLAN IDs, subnets, and gateways, and permit the corresponding VLANs on the upstream devices.
- **Developers and test engineers who need to split subnets for services**: understand how "two interfaces on one NIC" is implemented and what its limits are.

## Application Scenario

A single NIC has to carry traffic in two directions at the same time:

- **Video input**: cameras, NVRs, or streaming servers push multiple video streams to the server. The bandwidth is high and continuous, and the traffic is sensitive to packet loss and jitter.
- **Result output**: analysis results, alarms, and snapshots are sent back to result servers or clients. The volume is low, but the traffic must be stable and traceable.

Both subnets are provided by the external network and enter through other ports of the internal L3 switch, for example through two upstream switches, or through one trunk that carries both VLANs. The internal L3 switch only performs Layer 2 forwarding here and delivers each subnet to its own VLAN.

The traditional approach is to add a NIC: one NIC per subnet, with isolation achieved by physical separation, which also doubles the number of cables, switch ports, and upstream ports. However, the main board has no second service NIC available, so both services have to share one subnet, and isolation is impossible.

**This solution requires no additional router**: both gateways reside on the upstream devices. The server only needs to pass the VLANs through to `nic0_5G` and terminate both subnets on two VLAN devices (802.1Q sub-interfaces).

> **On the switch, set the port connected to `nic0_5G` as a trunk that permits both VLANs, and assign the ingress port of each subnet to the matching VLAN. On the aBMC side, create two VLAN devices on `nic0_5G` (for example, `vlan20` and `vlan30`), each with an address inside the upstream subnet and its gateway pointing to the upstream device.**

From the perspective of the system, these are two interfaces, each with its own IP address. The result is identical to having two NICs on two different subnets: video input goes through one interface and result output through the other.

## How It Works

A single NIC can carry two subnets because of Layer 2 isolation plus two sub-interfaces. No Layer 3 forwarding takes place on the server:

| Layer | Method | Effect |
| --- | --- | --- |
| Layer 2 | Switch ports are assigned to different broadcast domains by VLAN | The two subnets are invisible to each other inside the switch |
| Layer 2 | The port connected to `nic0_5G` is set as a trunk that permits both VLANs | One NIC sends and receives traffic for both subnets |
| Layer 2 | Two VLAN devices (sub-interfaces) are created on `nic0_5G` | The system sees two independent interfaces, each with one subnet address |
| Layer 3 | Gateways point to the upstream devices; no VLAN interface or routing is configured on the server | Outbound and inter-subnet access are handled by the upstream network |

Equivalent setup:

| Traditional multi-NIC approach (two subnets) | This approach (one NIC, two subnets) | Why they are equivalent |
| --- | --- | --- |
| NIC 1 + cable 1 + upstream port A ← subnet A | `vlan20` (VLAN Link = `nic0_5G`) + VLAN 20 tag | The tag separates broadcast domains, so neither subnet sees the other at Layer 2 |
| NIC 2 + cable 2 + upstream port B ← subnet B | `vlan30` (VLAN Link = `nic0_5G`) + VLAN 30 tag | As above; equivalent to a second physical network |
| Each subnet has its own upstream gateway | Each VLAN device uses the upstream gateway address | The gateway stays upstream; the server only separates traffic by VLAN |

The overall topology is shown below:

![One NIC connecting to two subnets: both subnets enter through different ports of the internal L3 switch and terminate on two VLAN sub-interfaces of nic0_5G](../../../servers_img/common/one_nic_two_subnets_vlan_en.svg)

*Figure: the two subnets enter VLAN 20 and VLAN 30 through ingress ports A and B of the switch; the switch works at Layer 2 only and converges the VLANs onto a trunk; on the right, the single trunk on `nic0_5G` splits into `vlan20` and `vlan30`, and both gateways still point upstream.*

## Procedure [step]

The VLAN IDs on the switch and on the BMC must match. Configure the switch first and the BMC second, so that you never end up in the intermediate state where a VLAN exists but the link does not work.

### Step 1: Internal L3 switch

Open the aBMC management page and go to **Device Management** → **Switch Management Settings**, then configure the VLANs and ports as listed below.

| Item | Value | Purpose |
| --- | --- | --- |
| VLANs | Create VLAN 20 and VLAN 30 | Create two broadcast domains |
| Port connected to `nic0_5G` | **Trunk**, permitting VLAN 20 and VLAN 30 | Let one NIC carry both subnets |
| Ingress port of subnet 1 | Access, VLAN 20 | Deliver the video input subnet into VLAN 20 |
| Ingress port of subnet 2 | Access, VLAN 30 (same as above) | Deliver the result output subnet into VLAN 30 |

### Step 2: Create VLAN devices on the BMC

Open **System → Network → VLAN Manage** in aBMC and add two VLAN devices on `nic0_5G`, one per subnet:

| VLAN device | VLAN ID | VLAN Link | IPv4 address | Gateway | Gateway priority |
| --- | --- | --- | --- | --- | --- |
| `vlan20` | 20 | `nic0_5G` | 192.168.20.10/24 | 192.168.20.1 | 100 |
| `vlan30` | 30 | `nic0_5G` | 192.168.30.10/24 | 192.168.30.1 | 200 |

Enter the address of the **upstream device** in that subnet as the gateway. The VLAN ID and the VLAN link cannot be changed after creation; delete and recreate the device if either is wrong. For field descriptions, screenshots, and the rules for creating, modifying, and deleting VLANs, see the "Managing BMC VLANs" section of the [aBMC Web User Manual](/docs/server/bmc-software/aBMC/bmcNetwork).

### Step 3: Verify connectivity

From the BMC command line, ping the gateway of each subnet to verify that every VLAN device can reach its subnet:

```bash
ping -c 4 192.168.20.1   # gateway of vlan20
ping -c 4 192.168.30.1   # gateway of vlan30
```

| Interface | Gateway | Expected result |
| --- | --- | --- |
| `vlan20` | 192.168.20.1 | Replies received, 0% packet loss |
| `vlan30` | 192.168.30.1 | Replies received, 0% packet loss |

If both gateways respond, the VLAN segmentation, trunk configuration, and addressing are correct. If one subnet is unreachable, check the following in order:

1. Whether the VLAN exists on the switch and is permitted on the port connected to `nic0_5G`.
2. Whether the ingress port of that subnet is assigned to the correct VLAN.
3. Whether **VLAN Link** of the VLAN device is set to `nic0_5G`.
4. Whether the upstream gateway is online and in the same subnet as the VLAN device address.