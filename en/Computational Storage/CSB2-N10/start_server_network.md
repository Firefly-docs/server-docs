# Network Wiring

This chapter describes the general network wiring methods of the server. The wiring methods below cover most usage scenarios. For special requirements, or if you need the internal network topology of the server, contact sales for technical support.

Before wiring, learn the role of each network port:

- **MGMT port**: Directly connected to the BMC. It is the out-of-band management port of the server.
- **SFP+1, SFP+2, GE1, and GE2 ports**: The service network ports of the server. They are all connected to the internal Layer 3 switch, which in turn connects to the sub-boards.

Out-of-band management is a remote management channel that does not rely on the operating system or the service network. As long as the server is powered, you can access the BMC through the MGMT port to power the server on or off, monitor hardware, upgrade firmware, and so on, even when the server is not booted or the system fails. For details, see the "Accessing the BMC" chapter.

**There are two wiring methods: out-of-band management and in-band management. Either of them gives the whole server access to the external network; out-of-band management is recommended.**

<CodeBlockTabs defaultValue="OutOfBand">
    <CodeBlockTabsList>
        <CodeBlockTabsTrigger value="OutOfBand">Out-of-Band Management</CodeBlockTabsTrigger>
        <CodeBlockTabsTrigger value="InBand">In-Band Management</CodeBlockTabsTrigger>
    </CodeBlockTabsList>

    <CodeBlockTab value="OutOfBand">
      ![Out-of-band management wiring](../../../servers_img/CSB2-N10/start_server_network/out_of_band_management_wiring.png)

      **Tools**

      - Network cables × 2 (one for the MGMT port, one for the GE1 port)

      **Wiring steps**

      1. Connect one network cable to the MGMT port of the server, and the other end to the upstream switch (Upstream Switch).
      2. Connect the other network cable to the GE1 port, and the other end to the same upstream switch (Upstream Switch).

      In this method, the out-of-band management (MGMT port) and the service network (GE1 port) are connected to the upstream switch independently, without affecting each other. The upstream switch then connects to endpoints such as a personal computer (Personal PC), so that the server can communicate with the external network.
    </CodeBlockTab>

    <CodeBlockTab value="InBand">
      ![In-band management wiring](../../../servers_img/CSB2-N10/start_server_network/in_band_management_wiring.png)

      **Tools**

      - Network cable × 1 (for the upstream switch)

      **Wiring steps**

      1. Connect the network cable to the GE1 port of the server, and the other end to the upstream switch (Upstream Switch).

      Note: In this method, a single network cable on the GE1 port is enough. Management traffic reaches the external network over the service network through the internal switch, sharing the same external cable on the GE1 port as service traffic. The upstream switch then connects to endpoints such as a personal computer (Personal PC).
    </CodeBlockTab>
</CodeBlockTabs>