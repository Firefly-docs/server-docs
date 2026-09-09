# Product Specifications and Components

## Server Specifications

| Item | Specification |
| :--- | :--- |
| **Server Form Factor** | 2U rack-mount computing server |
| **Number of Nodes** | 12 compute blades (96 distributed compute nodes in total) and 1 control node; each compute blade contains 8 compute nodes |
| **Control Node** | Octa-core 64-bit Rockchip RK3588 processor, up to 2.4 GHz, up to 6 TOPS |
| **Display Interface** | 1 VGA interface, up to 1080P, used for BMC management display |
| **USB** | - 3 USB 3.0 ports<br/>- 1 Type-C OTG port |
| **Network Interfaces** | - 8 × 10 Gbps SFP+ ports, with an aggregated peak bandwidth of up to 80 Gbps<br/>- 1 × 10/100/1000 Mbps RJ45 management network port for the BMC management network |
| **Console Interface** | 1 × RJ45 Console interface for BMC debugging, baud rate 115200 |
| **Buttons** | 1 power button, 1 UID button, 1 Recovery button, 1 Reset button |
| **Expansion Hard Drive** | 3 × 3.5-inch/2.5-inch SATA3.0 SSD drive bays (hot-swappable; the BMC can operate the drives directly, and compute sub-nodes can indirectly access the drives through the network sharing provided by the BMC) |
| **Fans** | 14 high-speed cooling fans |
| **System Management** | Integrated BMC management system based on a Web management interface, supporting Redfish, VNC, NTP, advanced monitoring, and virtual media, and supporting secondary development |
| **Network Management** | Supports dynamic network isolation between compute nodes, network traffic control, bandwidth scheduling, and Layer 3 network policy configuration |



## Environmental Specifications

| Item | Specification |
| :--- | :--- |
| **Temperature** | - Operating temperature: 5℃ to 40℃ (41℉ to 104℉)<br/>-  Storage temperature (24H): -40℃ to +65℃<br/>-  Storage temperature (within 3 months): -30℃ to +60℃ (-22℉ to +140℉)<br/>-  Storage temperature (within 6 months): -15℃ to +45℃ (5℉ to 113℉)<br/>-  Storage temperature (within 1 year): -10℃ to +35℃ (14℉ to 95℉)<br/>-  Maximum temperature change rate: 20℃ (36℉)/hour, 5℃ (9℉)/15 minutes |
| **Relative Humidity (RH, non-condensing)** | - Operating humidity: 8% to 90%<br/>-  Storage humidity (within 3 months): 8% to 85%<br/>-  Storage humidity (within 6 months): 8% to 80%<br/>-  Storage humidity (within 1 year): 20% to 75%<br/>-  Maximum humidity change rate: 20%/hour |


<Callout title="Hard Drive Storage Time Requirements" type="info">
Due to the limitations of the storage principles of SSD drives and mechanical drives (including NL-SAS, SAS, and SATA), they cannot be stored for long periods in a powered-off state. Exceeding the maximum storage time may cause data loss or drive failure. Under the condition that the storage temperature and storage humidity requirements are met, the drive storage time requirements are as follows:
* Maximum storage time for SSD drives:
    * Powered-off state with no data stored: 12 months
    * Powered-off state with data stored: 3 months
* Maximum storage time for mechanical drives:
    * Unopened packaging, or opened packaging in a powered-off state: 6 months
The maximum storage times are determined based on the powered-off storage time specifications provided by the drive manufacturers.
</Callout>

## Physical Specifications

### Product Specifications

| Item | Specification |
| :--- | :--- |
| **Dimensions (H×W×D)** | Chassis: 88.80mm (2U) × 495.60 × 928.51mm |
| **Installation Dimension Requirements** | Can be installed in a general-purpose cabinet that complies with the IEC 297 standard:<br/>- Width 19 inches<br/>- Depth 800 mm or more<br/><br/>The rail installation requirements are as follows:<br/>- Telescopic rails: the distance between the front and rear square-hole rails of the cabinet ranges from 543.5 mm to 848.5 mm |
| **Weight (Fully Configured)** | - Net weight: ?kg<br/>- Packaging material weight: ?kg |
| **Power Consumption** | The power consumption of the whole server varies depending on the quantity and types of the compute units installed.|
| **Power Supply** | - The power module does not support hot swapping<br/>- The recommended current ratings of the external power air circuit breaker connected to the server are as follows:<br/>&nbsp;&nbsp;- AC power: 32 A<br/>&nbsp;&nbsp;- DC power: 63 A<br/>- The power supply wattage should be greater than the maximum power consumption, with a margin of no less than 50 W |

### Network Specifications

#### Network Topology Diagram
| Symbol | Name | Description |
| :--- | :--- | :--- |
| nic0_5G | Ethernet NIC (speed 5 Gbps) | Supports VLAN division |
| Layer 3 switch | Internal Layer 3 switch | Supports VLAN division, link aggregation, QoS, and other features |



![perspective view](../../../servers_img/CSD2-N128/hw_logical_topology.png)

According to the hardware logical topology diagram, the ARM core boards integrated in the array server are interconnected with the BMC at high speed through a Layer 3 switch. This Layer 3 switch supports VLAN division and link aggregation, which allows users to flexibly configure network isolation policies according to actual business needs. **Contact an engineer to obtain the specific network topology diagram.**

#### Switch Performance Specifications

| Features | L3 Switch |
| ---- | ---- |
| Switching Capacity | 216 Gbps (full‑duplex) |
| Packet Forwarding Rate | 162 Mpps |
| **Port** | |
| Port Shutdown | Support |
| Port Speed | Support autonegotiate, full‑10000, full‑2500, full‑1000, full‑100, half‑100, full‑10, half‑10 |
| Flow Control | support full‑duplex IEEE 802.3x, half‑duplex back pressure |
| Storm Control | Supports rate limit for broadcast, multicast, and DLF packets |
| Storm Constrain | Support the detection of broadcast packets, multicast packets, or unicast packets on the port, shutdown the port if the rate is over the threshold. |
| Port Mirror | Support |
| Port Rate Limit | Support port ingress and egress rate limit |
| Link Aggregation | Support manual link aggregation<br>Support LACP dynamic link aggregation<br>Supports up to 32 aggregation groups, each group up to 8 ports<br>Support source MAC, destination MAC, source destination MAC, source IP, destination IP, source destination IP routing strategy |
| Port Isolate | Support |
| Jumbo Frame | Support up to 12KB packet |
| Redundant Port | Support |
| The DDM of fiber ports | Support |
| **MAC** | |
| MAC Table Capacity | 930x: 16K  931x:32K |
| MAC Table Management | Support |
| Forwarding mode | Support IVL forwarding mode |
| Static MAC Address | Support |
| MAC Address Binding | Support |
| MAC Address Filtering | Support |
| MAC Learning Control | Control the MAC learning based on port |
| **VLAN** | |
| Number of VLANs | 4K |
| 802.1q‑based VLAN | Support |
| MAC‑based VLAN | Support |
| IP‑based VLAN | Support |
| Protocol‑based VLAN | Support |
| PVLAN | Support |
| Voice VLAN | Support |
| VLAN Mapping | Support 1:1 mapping |
| QinQ | Support basic QinQ<br>Support flexible QinQ |
| GVRP | Support |
| **Reliability** | |
| Spanning Tree Protocol | Support STP/RSTP/MSTP |
| Port Loop Detection | Support |
| EAPS | Support RFC3619 |
| ERPS | Support G.8032/Y.1344 |
| LLDP | Support LLDP & LLDP‑MED |
| UDLD | Fully compatible with CISCO's UDLD protocol |
| VLLP (VRRP Layer‑2 Loop Protection) | Support, Only used with VRRP |
| **L3** | |
| ARP | Support static and dynamic ARP |
| Static Route | Support static route based on IPv4 and IPv6 |
| VLAN Interface | Support 32 VLAN interfaces |
| RIP | Support RIP v1/v2 & IPv6 RIPng |
| OSPF | Support OSPFv2 & IPv6 OSPFv3 |
| BGP | Support BGP4 & IPv6 BGP4+ |
| Policy Route | Support |
| VRRP | Support |
| **Multicast** | |
| Static Multicast MAC Address | Support |
| IGMP SNOOPING | Support IGMP SNOOPING v1/v2/v3<br>Support IGMP Querier<br>Support IGMP SNOOPING Filter |
| MVR | Support |
| GMRP | Support |
| IGMP | Support IGMP v1/v2/v3 |
| PIM‑SM | Support |
| **ACL** | |
| Standard IP‑based ACL | Support |
| Extended IP based ACL | Support |
| MAC IP‑based ACL | Support |
| MAC ARP‑based ACL | Support |
| ACL Port Filtering | Support |
| Time‑based ACL | Support |
| **QoS** | |
| Port Queue Number | 8 |
| Port Queue Scheduling Mode | Support WRR，WFQ，SP |
| Port‑based Classification | Support |
| 802.1p‑based Classification | Support |
| DSCP‑based Classification | Support |
| ACL‑based Classification | Support |
| QoS Policy | Support packets mapping to queue<br>Support COS or DSCP Remarking<br>Support rate limits of data flow<br>Support data flow statistics<br>Support mirroring of data flow |
| **DHCP** | |
| DHCP Client | Support |
| DHCP Snooping | Support |
| DHCP Relay | Support |
| DHCP Server | Support |
| DHCP option 82 | Support |
| **Management** | |
| CLI Management | Support Console, Telnet and SSH<br>Support multiple TELNET connections based on IPv4 and IPv6<br>Support multiple SSH connections based on IPv4 and IPv6 |
| WEB Management | Support HTTP based on IPv4 and IPv6<br>Support HTTPS based on IPv4 and IPv6 |
| SNMP Management | Support SNMP v1, v2c, v3<br>Support SNMP TRAP<br>Support lots of standard and private MIBs<br>Support SNMP and TRAP based on IPv4 and IPv6 |
| User Management | Support multiple user management |
| TACACS+ | support switch authentication via TACACS+ server remote username and password<br>Support password encryption in PAP and CHAP mode<br>Support TACACS+ server to authorize the switch’s commands<br>Support TACACS+ based on IPv4 and IPv6 |
| Log Management | Support local log management<br>Support SYSLOG |
| RMON | Support RMON 1, 2, 3 and 9 groups |
| Cluster Management | Support NDP<br>Support NTDP<br>Support manual and automatic joining of cluster groups<br>Support cluster unified management |
| Configuration File | Support uploading and downloading configuration file<br>Support TFTP transmission based IPv4 and IPv6 |
| Upgrade software | Support TFTP transmission based IPv4 and IPv6 |
| Clock Management | Support local clock management<br>Support SNTP |
| **Security** | |
| Switch Management Security | Support enabling and disabling TELNET、SSH、HTTP、HTTPS and SNMP services<br>Support TELNET、SSH、HTTP、HTTPS and SNMP services to bind to standard IP ACLs<br>Support for limiting the number of TELNET and SSH connections |
| CPU Protection | The switch's own security protection prevents large data streams from attacking the switch itself. |
| AAA | Support 802.1x<br>Support RADIUS<br>Supports authentication, authorization, and accounting through RADIUS server<br>Support port‑based and MAC‑based 802.1x<br>Support 802.1x guest VLAN |
| IP MAC Binding | Support static configuration of IP, MAC and port binding |
| DHCP SNOOPING | Support dynamic ARP binding to prevent ARP spoofing<br>Support dynamic IP, MAC and port binding<br>Support fixed port to connect to DHCP server to prevent private connection to DHCP server |
| Prevent ARP Spoofing | Support manually configuring MAC ARP‑based ACL rules to prevent ARP spoofing.<br>Support the DHCP SNOOPING function. During the process of obtaining an IP address by DHCP, the switch dynamically binds ARP to the port to prevent ARP spoofing. |
| **POE** | |
| Supported PoE chips | MAX5980,LTC4259,LTC4271,TPS23851,TPS23861,TPS23880,TPS23881,IP808,PD69100/69108,PD69200/69208 and hasivo poe, etc. |
| Switch Control | Support POE powering of ports on and off |
| Power Control | Support setting total power |
| Other Advanced Features | Support POE scheduling policy and PD online query, etc. |
| **IPv6** | |
| IPv4/IPv6 Dual Protocol Stack | Support |
| IPv6 Address | Support manual address configuration and stateless address auto‑configuration |
| IPv6 Neighbor Discovery | Support |
| ICMPv6 | Support |
| IPv6 Path MTU Discovery | Support |
| **Debugging** | |
| PING | Support |
| PING6 | Support |
| TRACEROUTE | Support |
| TELNET Client | Support TELNET client based IPv4 and IPv6 |
| SSH Client | Support SSH client based IPv4 and IPv6 |
#### Network Topology Diagram
According to the hardware logical topology diagram, the ARM core boards integrated in the array server are interconnected with the BMC at high speed through a Layer 3 switch. This Layer 3 switch supports VLAN division and link aggregation, which allows users to flexibly configure network isolation policies according to actual business needs. **Contact an engineer to obtain the specific network topology diagram.**

## Components

### Front Panel Buttons and Interfaces

| Marking | Interface/Button | Description |
| :--- | :--- | :--- |
| | Power button/indicator | **Power indicator description:**<br/>- Yellow (steady on): the server is in Standby state.<br/>- Yellow (blinking): the BMC management system is starting up.<br/>- Green (steady on): the server has powered on.<br/>- Off: the server is not powered on.<br/><br/>**Power button description:**<br/>- In the powered-on state, briefly pressing this button can shut down the sub-board OS normally.<br/><br/>**Detailed shutdown process:**<br/>- Seconds 1 to 5: Green (blinking) — notifies the sub-compute units to shut down business programs in an orderly manner within 5 seconds.<br/>- Seconds 5 to 20: Yellow/green (alternating blinking) — the sub-compute units formally begin shutting down.<br/>- Second 20: Yellow (steady on) — each compute unit is powered off (all units except the BMC are powered off).<br/>- In the Standby state, briefly pressing this button powers the server on. |
| | UID button and indicator | The UID button/indicator is used to locate the server to be operated.<br/><br/>**UID indicator description:**<br/>- Off: the server is not being located.<br/>- Yellow blinking (blinks for 255 seconds): the server is being specifically located. |
| USB 3.0 | USB 3.0 interface | One on each side of the front panel, for connecting devices such as a keyboard, mouse, or USB drive |

### Display Interface

| Marking | Interface Name | Description |
| :--- | :--- | :--- |
| VGA | VGA interface | Used for BMC management display, up to 1080P |


### Rear Panel Interfaces and Buttons

| Marking | Interface/Button | Description |
| :--- | :--- | :--- |
| USB 3.0 | USB 3.0 interface | For connecting devices such as a keyboard, mouse, or USB drive |
| Type-C | Type-C interface | Used for the OTG function |
| Recovery | Recovery button | Used to enter Recovery mode |
| Reset | Reset button | Used to reset the system |
### Network Interfaces

| Marking | Interface Name | Description |
| :--- | :--- | :--- |
| SFP+1～SFP+8 | SFP+ ports | 8 × 10 Gbps ports; the four optical ports on each side belong to two different Layer 3 switches |
| MGMT | RJ45 | - Used as the BMC management network<br/>- 1000/100/10 Mbps auto-negotiation |
### Debug Interface

| Marking | Interface Name | Description |
| :--- | :--- | :--- |
| Console | RJ45 | - BMC debug serial port<br/>- Baud rate 115200 |