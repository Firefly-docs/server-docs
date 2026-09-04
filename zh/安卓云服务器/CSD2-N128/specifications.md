# 产品规格和组件

## 服务器规格

| 指标项 | 规格 |
| :--- | :--- |
| **服务器形态** | 2U 机架式算力服务器 |
| **节点数** | 12 个计算刀片（共 96 个分布式计算节点）和 1 个控制节点；每个计算刀片包含 8 个计算节点 |
| **控制节点** | 八核 64 位 Rockchip RK3588 处理器，最高主频 2.4 GHz，最高算力 6 TOPS |
| **显示接口** | 1 个 VGA 接口，最高分辨率为 1080P，用于 BMC 管理显示 |
| **USB** | - 3 个 USB 3.0 接口<br/>- 1 个 Type-C OTG 接口 |
| **网络接口** | - 8 个 10 Gbps SFP+ 万兆网口，聚合峰值带宽可达 80 Gbps<br/>- 1 个 10/100/1000 Mbps RJ45 管理网口，用于 BMC 管理网络 |
| **Console 接口** | 1 个用于 BMC 调试的 RJ45 Console 接口，波特率为 115200 |
| **按键** | 1 个电源键、1 个 UID 键、1 个 Recovery 键、1 个 Reset 键 |
| **扩展硬盘** | 3.5 英寸/2.5英寸 SATA3.0 SSD 硬盘位 × 3（支持热插拔；BMC可直接操作硬盘，计算子节点可通过BMC提供的网络共享方式，间接访问硬盘） |
| **风扇** | 14 个高速散热风扇 |
| **系统管理** | 集成基于 Web 管理界面的 BMC 管理系统，支持 Redfish、VNC、NTP、高级监控及虚拟媒体，并支持二次开发 |
| **网络管理** | 支持计算节点间动态网络隔离、网络流量控制、带宽调度及三层网络策略配置 |



## 环境规格

| 指标项 | 规格 |
| :--- | :--- |
| **温度** | - 工作温度：5℃～40℃（41℉～104℉）<br/>-  存储温度（24H）：-40℃～+65℃<br/>-  存储温度（3个月以内）：-30℃～+60℃（-22℉～+140℉）<br/>-  存储温度（6个月以内）：-15℃～+45℃（5℉～113℉）<br/>-  存储温度（1年以内）：-10℃～+35℃（14℉～95℉）<br/>-  最大温度变化率：20℃（36℉）/小时、5℃（9℉）/15分钟 |
| **相对湿度（RH，无冷凝）** | - 工作湿度：8%～90%<br/>-  存储湿度（3个月以内）：8%～85%<br/>-  存储湿度（6个月以内）：8%～80%<br/>-  存储湿度（1年以内）：20%～75%<br/>-  最大湿度变化率：20%/小时 |


<Callout title="硬盘存储时间要求" type="info">
由于SSD硬盘和机械硬盘（包括NL-SAS、SAS、SATA）存储原理的限制，不能在下电状态下长期保存，若超过最长存储时间，可能导致数据丢失或者硬盘故障。在满足存储温度与存储湿度的条件下，硬盘的存储时间要求如下：
* SSD硬盘最长存储时间：
    * 下电状态且未存储数据：12个月
    * 下电状态且已存储数据：3个月
* 机械硬盘最长存储时间：
    * 未打开包装或已打开包装且为下电状态：6个月
最长存储时间是依据硬盘厂商提供的硬盘下电存放时间规格确定的。
</Callout>

## 物理规格

### 产品规格

| 指标项 | 规格 |
| :--- | :--- |
| **尺寸（高×宽×深）** | 机箱：88.80mm (2U) × 495.60 × 928.51mm |
| **安装尺寸要求** | 可安装在满足 IEC 297 标准的通用机柜中：<br/>- 宽 19 英寸<br/>- 深 800 mm 及以上<br/><br/>滑道的安装要求如下：<br/>- 可伸缩滑道：机柜前后方孔条的距离范围为 543.5 mm ～ 848.5 mm |
| **满配重量** | - 净重：?kg<br/>- 包装材料重量：?kg |
| **能耗** | 整机因搭载的计算单元数量和种类不同，能耗存在差异。|
| **电源** | - 电源模块不支持热插拔<br/>- 服务器连接的外部电源空气开关电流规格推荐如下：<br/>&nbsp;&nbsp;- 交流电源：32 A<br/>&nbsp;&nbsp;- 直流电源：63 A<br/>- 电源功率应大于最大能耗，并保留不少于 50 W 的余量 |

### 网络规格

#### 网络拓扑图
| 符号 | 名称 | 说明 |
| :--- | :--- | :--- |
| nic0_5G | 以太网卡（速度 5 Gbps） | 支持 VLAN 划分 |
| Layer 3 switch | 内部三层交换机 | 支持 VLAN 划分、网络聚合和 QoS 等功能 |



![perspective view](../../../servers_img/CSD2-N128/hw_logical_topology.png)

根据硬件结构逻辑图可知，阵列式服务器中集成的ARM核心板与BMC是通过一个三层交换机实现告诉网络互联的，该三层交换机支持VLAN划分和网络聚合，这就可以方便用户根据实际业务需求灵活配置网络隔离策略，**具体的网络拓扑图联系工程师获取**。

#### 交换机性能指标

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
#### 网络拓扑图
根据硬件结构逻辑图可知，阵列式服务器中集成的ARM核心板与BMC是通过一个三层交换机实现告诉网络互联的，该三层交换机支持VLAN划分和网络聚合，这就可以方便用户根据实际业务需求灵活配置网络隔离策略，**具体的网络拓扑图联系工程师获取**。

## 组件

### 前面板按键及接口

| 标识 | 接口/按键 | 说明 |
| :--- | :--- | :--- |
| | 电源按钮/指示灯 | **电源指示灯说明：**<br/>- 黄色（常亮）：表示服务器处于待机（Standby）状态。<br/>- 黄色（闪烁）：表示 BMC 管理系统正在启动。<br/>- 绿色（常亮）：表示服务器已经开机。<br/>- 熄灭：表示服务器未上电。<br/><br/>**电源按钮说明：**<br/>- 上电状态下短按该按钮，可以正常关闭子板 OS。<br/><br/>**详细关机过程：**<br/>- 第 1至5 秒：绿色（闪烁）——通知子计算单元需要在 5 秒内有序关闭业务程序。<br/>- 第 5至20 秒：黄绿（交替闪烁）——子计算单元正式开始关机。<br/>- 第 20 秒：黄色（常亮）——各个计算单元下电（除 BMC 外均下电）。<br/>- 待机状态下短按该按钮，可以进行上电。 |
| | UID 按键与指示灯 | UID 按钮/指示灯用于定位待操作的服务器。<br/><br/>**UID 指示灯说明：**<br/>- 熄灭：服务器未被定位。<br/>- 黄色闪烁（闪烁 255 秒）：服务器被重点定位。 |
| USB 3.0 | USB 3.0 接口 | 前面板左右两侧各 1 个，可连接键盘、鼠标或 U 盘等设备 |

### 显示接口

| 标识 | 接口名称 | 说明 |
| :--- | :--- | :--- |
| VGA | VGA 接口 | 用于 BMC 管理显示，最高分辨率为 1080P |


###  后面板接口及按键

| 标识 | 接口/按键 | 说明 |
| :--- | :--- | :--- |
| USB 3.0 | USB 3.0 接口 | 可连接键盘、鼠标或 U 盘等设备 |
| Type-C | Type-C 接口 | 用于 OTG 功能 |
| Recovery | Recovery 键 | 用于进入 Recovery 模式 |
| Reset | Reset 键 | 用于复位系统 |
### 网络接口

| 标识 | 接口名称 | 说明 |
| :--- | :--- | :--- |
| SFP+1～SFP+8 | SFP+ 端口 | 8 个 10 Gbps 万兆网口,左右各四个光口分属不同两个不同的三层交换机 |
| MGMT | RJ45 | - 用作 BMC 管理网络<br/>- 1000/100/10 Mbps 自适应 |
### 调试接口

| 标识 | 接口名称 | 说明 |
| :--- | :--- | :--- |
| Console | RJ45 | - BMC 调试串口<br/>- 波特率为 115200 |