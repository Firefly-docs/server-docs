# Network Wiring

This chapter first explains how the network inside the server is connected, and then introduces the common wiring methods. The wiring methods below cover most usage scenarios. For special requirements, contact sales for technical support.

Before wiring, learn the role of each network port:

- **GEM port**: The MGMT port directly connected to the BMC. It is the out-of-band management port of the server.
- **SFP+1, SFP+2, and GE ports**: The service network ports of the server. They are all connected to the internal switch, which in turn connects to the sub-boards and the BMC.

Out-of-band management is a remote management channel that does not rely on the operating system or the service network. As long as the server is powered, you can access the BMC through the GEM port to power the server on or off, monitor hardware, upgrade firmware, and so on, even when the server is not booted or the system fails. For details, see the "Accessing the BMC" chapter.

## Internal Network Topology [step]

![Server internal network topology](../../../servers_img/CSA1-N8/network_topo.png)

Inside the server, these ports fall into two parts: the service channel and the management channel.

- **Service channel**: The SFP+1, SFP+2, and GE ports on the chassis are connected to the internal switch chip SWITCH1, which connects the PHY1 ports of the sub-boards. The BMC is also connected to SWITCH1 through pci_net.
- **Management channel**: The BMC is connected to another switch chip, SWITCH0, through usb_net, and SWITCH0 connects the PHY0 ports of the sub-boards. The MGMT port of the BMC is routed directly to the GEM port on the chassis, without going through any switch chip.

In other words, each sub-board (SUB01–SUB08) has two network ports, PHY1 and PHY0, attached to SWITCH1 and SWITCH0 respectively, and the GEM port is a direct outlet of the BMC. Because out-of-band management runs on a dedicated channel, it can be uplinked to the external network with its own cable, as described in the out-of-band management method below.

## Wiring Methods [step]

**There are two wiring methods: out-of-band management and in-band management. Either of them gives the whole server access to the external network; out-of-band management is recommended.**

<CodeBlockTabs defaultValue="OutOfBand">
    <CodeBlockTabsList>
        <CodeBlockTabsTrigger value="OutOfBand">Out-of-Band Management</CodeBlockTabsTrigger>
        <CodeBlockTabsTrigger value="InBand">In-Band Management</CodeBlockTabsTrigger>
    </CodeBlockTabsList>

    <CodeBlockTab value="OutOfBand">
      ![Out-of-band management wiring](../../../servers_img/CSA1-N8/start_server_network/out_of_band_management_wiring.png)

      **Tools**

      - Network cables × 2 (one for the GEM port, one for the GE port)

      **Wiring steps**

      1. Connect one network cable to the GEM port of the server, and the other end to the external network.
      2. Connect the other network cable to the GE port, and the other end to the external network.

      In this method, the out-of-band management (GEM port) and the service network (GE port) are connected to the external network independently, without affecting each other.
    </CodeBlockTab>

    <CodeBlockTab value="InBand">
      ![In-band management wiring](../../../servers_img/CSA1-N8/start_server_network/in_band_management_wiring.png)

      **Tools**

      - Network cable × 1 (for connecting the GE port to the external network)

      **Wiring steps**

      1. Connect the network cable to the GE port of the server, and the other end to the external network.

      Note: Both the GE port and the BMC are connected to the internal switch. With the GE port uplinked to the external network, management traffic and service traffic share the same network channel, so the BMC can be accessed through the service network without connecting the GEM port.
    </CodeBlockTab>
</CodeBlockTabs>