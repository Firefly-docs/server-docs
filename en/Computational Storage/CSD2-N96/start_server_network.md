# Network Wiring

This chapter describes the general network wiring methods of the server. The wiring methods below cover most usage scenarios. For special requirements, or if you need the internal network topology of the server, contact sales for technical support.

Before wiring, learn the role of each network port:

- **MGMT port**: Directly connected to the BMC. It is the out-of-band management port of the server.
- **SFP+1 to SFP+8 ports**: The service network ports of the server. SFP+1 to SFP+4 and SFP+5 to SFP+8 belong to two different internal Layer 3 switches.

Out-of-band management is a remote management channel that does not rely on the operating system or the service network. As long as the server is powered, you can access the BMC through the MGMT port to power the server on or off, monitor hardware, upgrade firmware, and so on, even when the server is not booted or the system fails. For details, see the "Accessing the BMC" chapter.

**There are two wiring methods: out-of-band management and in-band management. Either of them gives the whole server access to the external network; out-of-band management is recommended.**

<CodeBlockTabs defaultValue="OutOfBand">
    <CodeBlockTabsList>
        <CodeBlockTabsTrigger value="OutOfBand">Out-of-Band Management</CodeBlockTabsTrigger>
        <CodeBlockTabsTrigger value="InBand">In-Band Management</CodeBlockTabsTrigger>
    </CodeBlockTabsList>

    <CodeBlockTab value="OutOfBand">
      ## Out-of-Band Management: Dedicated MGMT Uplink [step]

      ![Two network cables wiring](../../../servers_img/CSD2-Nx/start_server_network/dual_external_network_wiring.png)

      **Tools**

      - 10G SFP+ to RJ45 module × 1 ([Purchase link](https://item.taobao.com/item.htm?id=615471664761&skuId=4338443096690))
      - 10G optical modules (LC) × 2 and one fiber patch cord (for interconnecting the SFP+4 and SFP+5 ports)
      - Network cables × 2 (one for the MGMT port, one for the module in the SFP+6 port)

      **Wiring steps**

      1. Connect one network cable to the MGMT port of the server, and the other end to the upstream switch (Upstream Switch).
      2. Insert the 10G optical modules into the SFP+4 and SFP+5 ports respectively, and interconnect the two ports with the fiber patch cord to bridge the two internal switches.
      3. Insert the 10G SFP+ to RJ45 module into the SFP+6 port of the server, connect one end of the other network cable to the module, and the other end to the upstream switch (Upstream Switch).

      In this method, the out-of-band management (MGMT port) and the service network (SFP+ ports) are connected to the upstream switch independently, without affecting each other. The upstream switch then connects to endpoints such as a personal computer (Personal PC), so that the server can communicate with the external network.

      **Optional: three cables (separate uplinks for the two internal switches)**

      ![Three network cables wiring](../../../servers_img/CSD2-Nx/start_server_network/triple_external_network_wiring.png)

      1. Connect one network cable to the MGMT port of the server, and the other end to the upstream switch (Upstream Switch).
      2. Insert one 10G SFP+ to RJ45 module into the SFP+4 port of the server, connect one end of a network cable to the module, and the other end to the upstream switch (Upstream Switch).
      3. Insert the other 10G SFP+ to RJ45 module into the SFP+5 port of the server, connect one end of the last network cable to the module, and the other end to the upstream switch (Upstream Switch).

      In this way, the two internal switches are uplinked to the external network separately through SFP+4 and SFP+5, and no jumper between the switches is needed; the MGMT port (BMC) is also uplinked directly. Compared with the previous method, the service traffic of the left switch no longer needs to be forwarded by the right switch, which reduces the load on the right switch.
    </CodeBlockTab>

    <CodeBlockTab value="InBand">
      ## In-Band Management: Management Traffic over the Service Network [step]

      ![Single external network cable wiring](../../../servers_img/CSD2-Nx/start_server_network/single_external_network_wiring.png)

      **Tools**

      - 10G SFP+ to RJ45 modules × 2 ([Purchase link](https://item.taobao.com/item.htm?id=615471664761&skuId=4338443096690))
      - 10G optical modules (LC) × 2 and one fiber patch cord (for interconnecting the SFP+4 and SFP+5 ports)
      - Network cables × 2 (one for interconnecting the MGMT and SFP+2 ports, one for the external network)

      **Wiring steps**

      1. Insert one 10G SFP+ to RJ45 module into the SFP+2 port of the server, and interconnect the MGMT port and the SFP+2 port with a network cable.
      2. Insert the 10G optical modules into the SFP+4 and SFP+5 ports respectively, and interconnect the two ports with the fiber patch cord to bridge the two internal switches.
      3. Insert the other 10G SFP+ to RJ45 module into the SFP+6 port of the server, connect one end of the external network cable to the module, and the other end to the upstream switch (Upstream Switch).

      Note: The MGMT port is directly connected to the BMC. After it is interconnected with the SFP+2 port, the BMC management port joins the internal switch of SFP+1 to SFP+4. The fiber patch cord between SFP+4 and SFP+5 bridges the two internal switches, so that the BMC and service traffic can communicate with the external network through the module in the SFP+6 port. The MGMT port is a gigabit copper port, and the module in the SFP+2 port negotiates to 1 Gbps with it. In this method, out-of-band management traffic goes over the service network and shares the same external cable. The upstream switch then connects to endpoints such as a personal computer (Personal PC).

      CAUTION: The fiber patch cord between SFP+4 and SFP+5 bridges the two internal switches and must not be omitted. Otherwise, the BMC cannot communicate with the SFP+5 to SFP+8 ports.
    </CodeBlockTab>
</CodeBlockTabs>