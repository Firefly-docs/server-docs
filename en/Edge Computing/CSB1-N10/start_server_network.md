# Network Wiring

This chapter describes the general network wiring methods of the server. The wiring methods below cover most usage scenarios. For special requirements, or if you need the internal network topology of the server, contact sales for technical support.

Before wiring, learn the role of each network port:

- **MGMT port**: Directly connected to the BMC. It is the out-of-band management port of the server.
- **SFP+1, SFP+2, GE1, and GE2 ports**: The service network ports of the server. They are all connected to the internal Layer 3 switch, which in turn connects to the sub-boards.

Out-of-band management is a remote management channel that does not rely on the operating system or the service network. As long as the server is powered, you can access the BMC through the MGMT port to power the server on or off, monitor hardware, upgrade firmware, and so on, even when the server is not booted or the system fails. For details, see the "Accessing the BMC" chapter.

**Either of the following two wiring methods allows the whole server to communicate with the external network; the difference is whether the out-of-band management channel shares the same external cable with the service network.**

## One Cable: Shared Uplink for Service and Out-of-Band Management

**Tools**

- Network cables × 2 (one for the external network, one for interconnecting the MGMT and GE2 ports)

**Wiring steps**

1. Connect one network cable to the GE1 port of the server (1 in the figure), and the other end to the external network.
2. Interconnect the MGMT port and the GE2 port with the other network cable (2 in the figure).

Note: The MGMT port is directly connected to the BMC. After it is interconnected with the GE2 port, the BMC management port can communicate with the external network through the internal switch. In this method, out-of-band management and the service network share the same external cable on the GE1 port.

The wiring locations are shown below:

![Single external network cable wiring](../../../servers_img/CSB1-N10/start_server_network/single_external_network_wiring.png)

## Two Cables: Independent Uplinks for Service and Out-of-Band Management

**Tools**

- Network cables × 2 (one for the MGMT port, one for the GE1 port)

**Wiring steps**

1. Connect one network cable to the MGMT port of the server (1 in the figure), and the other end to the external network.
2. Connect the other network cable to the GE1 port (2 in the figure), and the other end to the external network.

In this method, the out-of-band management (MGMT port) and the service network (GE1 port) are connected to the external network independently, without affecting each other.

The wiring locations are shown below:

![Two external network cables wiring](../../../servers_img/CSB1-N10/start_server_network/dual_external_network_wiring.png)