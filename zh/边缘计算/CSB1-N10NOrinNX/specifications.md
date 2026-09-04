# 产品规格和组件

## 服务器规格

| 指标项 | 规格 |
| :--- | :--- |
| **服务器形态** | 1U 机架式服务器 |
| **计算节点型号** | 支持 10 路分布式计算节点 |
| **显示接口** | 1080P VGA 接口 |
| **USB** | 2 个 USB 3.0 接口 |
| **网络接口** | - 2 个 10Gbps SFP+ 共享网口<br/>- 2 个 10/100/1000Mbps RJ45 共享网口 |
| **扩展硬盘** | 3.5 英寸/2.5英寸 SATA3.0 SSD 硬盘位 × 1（支持热插拔；BMC可直接操作硬盘，计算子节点可通过BMC提供的网络共享方式，间接访问硬盘） |
| **串行接口** | - 1 个 RS232 DB9 接口<br/>- 1 个 RS485 DB9 接口 |
| **Console 接口** | 1 个用于调试的 RJ45 Console 接口 |
| **灯和按键** | - 1 个 UID 按键灯<br/>- 1 个 POWER 按键灯<br/>- 10 个计算节点系统指示灯<br/>- 2 个 SFP+ 指示灯<br/>- 1 个交换机指示灯<br/>- 1 个 BMC 系统指示灯 |
| **风扇** | 7 个冗余风扇 |
| **系统管理** | - 适配 aBMC 管理系统（支持 Redfish、VNC、NTP、监控高级及虚拟媒体）<br/>- 1 个 10/100/1000Mbps RJ45 管理网口 |
| **安全特性** | - 管理员密码<br/>- 故障告警<br/>- 应急 recovery 模式 |

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
| **尺寸（高×宽×深）** | 机箱：44.4mm (1U) × 440.5mm × 494.0mm |
| **安装尺寸要求** | 可安装在满足 IEC 297 标准的通用机柜中：<br/>- 宽 19 英寸<br/>- 深 800 mm 及以上<br/><br/>滑道的安装要求如下：<br/>- 可伸缩滑道：机柜前后方孔条的距离范围为 543.5 mm ～ 848.5 mm |
| **满配重量** | - 净重：8.1kg<br/>- 包装材料重量：10.3kg |
| **能耗** | 整机因搭载的计算单元数量和种类不同，能耗存在差异。以下为参考值：<br/>- 待机能耗：150 W<br/>- 最大能耗：450 W |
| **电源** | - 电源模块不支持热插拔<br/>- 服务器连接的外部电源空气开关电流规格推荐如下：<br/>&nbsp;&nbsp;- 交流电源：32 A<br/>&nbsp;&nbsp;- 直流电源：63 A<br/>- 电源功率应大于最大能耗，并保留不少于 50 W 的余量 |



### 网络规格

#### 网络拓扑图
| 符号 | 名称 | 说明 |
| :--- | :--- | :--- |
| nic0_5G | 以太网卡（速度 5 Gbps） | 支持 VLAN 划分 |
| Layer 3 switch | 内部三层交换机 | 支持 VLAN 划分、网络聚合和 QoS 等功能 |



![perspective view](../../../servers_img/CSB1-N10NOrinNX/hw_logical_topology.png)

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
## 组件
### 前面板按键及指示灯

| 标识 | 指示灯/按钮 | 状态说明 |
| :--- | :--- | :--- |
| 1-10 | 计算节点状态指示灯 | - 绿色（常亮）：表示计算节点已经正常开机。<br/>- 熄灭：表示计算节点未上电。 |
| | 电源按钮/指示灯 | **电源指示灯说明：**<br/>- 黄色（常亮）：表示服务器处于待机（Standby）状态。<br/>- 黄色（闪烁）：表示 BMC 管理系统正在启动。<br/>- 绿色（常亮）：表示服务器已经开机。<br/>- 熄灭：表示服务器未上电。<br/><br/>**电源按钮说明：**<br/>- 上电状态下短按该按钮，可以正常关闭子板 OS。<br/><br/>**详细关机过程：**<br/>- 第 1至5 秒：绿色（闪烁）——通知子计算单元需要在 5 秒内有序关闭业务程序。<br/>- 第 5至20 秒：黄绿（交替闪烁）——子计算单元正式开始关机。<br/>- 第 20 秒：黄色（常亮）——各个计算单元下电（除 BMC 外均下电）。<br/>- 待机状态下短按该按钮，可以进行上电。 |
| | UID 按键与指示灯 | UID 按钮/指示灯用于定位待操作的服务器。<br/><br/>**UID 指示灯说明：**<br/>- 熄灭：服务器未被定位。<br/>- 黄色闪烁（闪烁 255 秒）：服务器被重点定位。 |
| BS | 健康状态指示灯（Health Status Indicator） | - 红色（常亮）：表示系统有严重告警。 |
| ES | 交换机系统状态指示灯（Ethernet Switch Indicator） | - 绿色慢闪（1 Hz）：交换机正在启动。<br/>- 绿色快闪：交换机已开机。<br/>- 熄灭：交换机未上电或未完成启动。 |
| S1/S2 | 光模块在位指示灯（1、2） | - 1、2：1 代表 SFP1；2 代表 SFP2。<br/>- 绿色（常亮）：表示 SFP 在位，可以被正常识别。<br/>- 熄灭：表示 SFP 不在位或故障。 |
| | 重置按键 | - **重启 BMC**：短按。<br/>- **重置密码**：长按 5 秒，至 BS 灯慢闪。<br/>- **恢复出厂**：长按 10 秒，至 BS 灯快闪。<br/>- **误按恢复**：按住不松开（约 15 秒），至 BS 灯恢复常亮。 |
| | OTG USB（下方） | 可使用 U 盘对 BMC 进行 OTG 升级。 |

### 网络接口

| 标识 | 接口名称 | 说明 |
| :--- | :--- | :--- |
| SFP+1 / SFP+2 | SFP+ 端口 | - 万兆光口默认速率为 10 Gbps。<br/>- 如果是千兆网络的环境下，需手动切换到 1 Gbps。 |
| GE1 / GE2 | RJ45 | - 千兆以太网口，1000/100/10 Mbps 自适应。 |
| MGMT | RJ45 | - 用作 BMC 管理网络。<br/>- 1000/100/10 Mbps 自适应。 |
### 调试接口

| 标识 | 接口名称 | 说明 |
| :--- | :--- | :--- |
| Console | RJ45 | - BMC 调试串口<br/>- 波特率为 115200 |
### 显示接口

| 标识 | 接口名称 | 说明 |
| :--- | :--- | :--- |
| VGA | VGA 接口 | - BMC 直出 VGA。<br/>- 分辨率为 1080P。 |
### 硬盘及指示灯

#### 硬盘位置
![perspective view](../../../servers_img/CSB1-N10NOrinNX/harddisk_location.png)
#### 硬盘配置

<table border="1" cellPadding="8" cellSpacing="0" width="100%">
  <thead>
    <tr>
      <th>配置</th>
      <th>最大硬盘数量（个）</th>
      <th>普通硬盘管理方式</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>
        3.5英寸（或2.5英寸）硬盘直通配置
        <ul style="margin: 6px 0; padding-left: 20px;">
          <li>配置标准3.5英寸硬盘托盘，可兼容2.5硬盘。</li>
          <li>支持SATA III协议。</li>
          <li>硬盘与BMC直通，BMC直接操作硬盘，可支持高达16TB或更大容量的硬盘。</li>
          <li>计算子节点可通过BMC提供的网络共享方式，间接的访问硬盘。</li>
        </ul>
      </td>
      <td>1（SATA硬盘）</td>
      <td>BMC直出SATA</td>
    </tr>
  </tbody>
</table>



#### SATA硬盘指示灯

![SATA Hard Disk Indicator Location Diagram](../../../servers_img/CSB1-N10NOrinNX/sata_hdd_indicator.png)

<table border="1" cellPadding="8" cellSpacing="0" width="100%">
  <thead>
    <tr>
      <th>硬件Active指示灯(绿色指示灯)</th>
      <th>硬件Fault指示灯(黄色指示灯)</th>
      <th>普通硬盘管理方式</th>
      <th>处理步骤及说明</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>常亮</td>
      <td rowSpan="2">熄灭</td>
      <td>硬盘在位</td>
      <td rowSpan="4">无需任何操作</td>
    </tr>
    <tr>
      <td>闪烁（4Hz）</td>
      <td>硬盘处于正常读写状态或重构主盘状态</td>
    </tr>
    <tr>
      <td>常亮</td>
      <td rowSpan="2">闪烁（1Hz）</td>
      <td>硬盘被BMC定位</td>
    </tr>
    <tr>
      <td>闪烁（1Hz）</td>
      <td>硬盘处于重构从盘状态</td>
    </tr>
    <tr>
      <td>熄灭</td>
      <td rowSpan="2">常亮</td>
      <td>硬盘被拔出</td>
      <td rowSpan="2">
        1. 检查硬盘是否存在<br/>
        2. 检查硬盘是否故障
      </td>
    </tr>
    <tr>
      <td>常亮</td>
      <td>硬盘故障</td>
    </tr>
  </tbody>
</table>
### 后面板接口

| 标识 | 接口名称 | 说明 |
| :--- | :--- | :--- |
| 232 | DB9 | - 波特率为 115200 |
| 485 | DB9 | - 波特率为 115200 |