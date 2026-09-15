# Server Network Wiring

This chapter describes the general network wiring methods of the server. The wiring methods below cover most usage scenarios. For special requirements, or if you need the internal network topology of the server, contact sales for technical support.

Before wiring, learn the role of each network port:

- **GEM port**: The MGMT port directly connected to the BMC.
- **SFP+1, SFP+2, and GE ports**: All connected to the internal switch, which in turn connects to the sub-boards and the BMC.

Either of the following two wiring methods allows the whole server to communicate with the external network.

## Connect One RJ45 Cable as the External Network Cable

**Tools**

- 10G SFP+ to RJ45 module × 1 ([Purchase link](https://item.taobao.com/item.htm?id=615471664761&skuId=4338443096690))
- Network cables × 2 (one for the external network, one for interconnecting the GEM and GE ports)

**Wiring steps**

1. Insert the 10G SFP+ to RJ45 module into the SFP+1 port of the server (1 in the figure), and connect the external network cable to the module.
2. Interconnect the GEM port and the GE port with a network cable (2 in the figure).

Note: The GEM port is directly connected to the BMC. After it is interconnected with the GE port, the BMC management port can communicate with the external network through the internal switch.

The wiring locations are shown below:

![Single external network cable wiring](../../../servers_img/CSA1-N8/start_server_network/single_external_network_wiring.png)

## Connect Two RJ45 Cables as the External Network Cables

**Tools**

- Network cables × 2 (one for the GEM port, one for the GE port)

**Wiring steps**

1. Connect one network cable to the GEM port of the server (1 in the figure), and the other end to the external network.
2. Connect the other network cable to the GE port (2 in the figure), and the other end to the external network.

The wiring locations are shown below:

![Two external network cables wiring](../../../servers_img/CSA1-N8/start_server_network/dual_external_network_wiring.png)