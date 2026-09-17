# Network Wiring

This chapter describes the general network wiring methods of the server. The wiring methods below cover most usage scenarios. For special requirements, or if you need the internal network topology of the server, contact sales for technical support.

Before wiring, learn the role of each network port:

- **MGMT port**: Directly connected to the BMC. It is the out-of-band management port of the server.
- **SFP+1 to SFP+4 ports**: The service network ports of the server. They are all connected to the internal switch.

Out-of-band management is a remote management channel that does not rely on the operating system or the service network. As long as the server is powered, you can access the BMC through the MGMT port to power the server on or off, monitor hardware, upgrade firmware, and so on, even when the server is not booted or the system fails. For details, see the "Accessing the BMC" chapter.

**There are two wiring methods: out-of-band management and in-band management. Either of them gives the whole server access to the external network; out-of-band management is recommended.**

<CodeBlockTabs defaultValue="OutOfBand">
    <CodeBlockTabsList>
        <CodeBlockTabsTrigger value="OutOfBand">Out-of-Band Management</CodeBlockTabsTrigger>
        <CodeBlockTabsTrigger value="InBand">In-Band Management</CodeBlockTabsTrigger>
    </CodeBlockTabsList>

    <CodeBlockTab value="OutOfBand">
      ![Out-of-band management wiring](../../../servers_img/CSC2-N48/start_server_network/out_of_band_management_wiring.png)

      **Tools**

      - 10G SFP+ to RJ45 module × 1
      - Network cables × 2 (one for the MGMT port, one for the module in the external-network port)

      **Wiring steps**

      1. Connect one network cable to the MGMT port of the server, and the other end to the upstream switch (Upstream Switch).
      2. Insert the 10G SFP+ to RJ45 module into any one of the SFP+1 to SFP+4 ports of the server (SFP+1 as an example), connect one end of the other network cable to the module, and the other end to the same upstream switch (Upstream Switch).

      In this method, the out-of-band management (MGMT port) and the service network (SFP+ ports) are connected to the upstream switch independently, without affecting each other. The upstream switch then connects to endpoints such as a personal computer (Personal PC), so that the server can communicate with the external network.
    </CodeBlockTab>

    <CodeBlockTab value="InBand">
      ![In-band management wiring](../../../servers_img/CSC2-N48/start_server_network/in_band_management_wiring.png)

      **Tools**

      - 10G SFP+ to RJ45 module × 1
      - Network cable × 1 (for the upstream switch)

      **Wiring steps**

      1. Insert the 10G SFP+ to RJ45 module into the SFP+4 port of the server, connect one end of the network cable to the module, and the other end to the upstream switch (Upstream Switch).

      Note: The BMC management port of the CSC2 is connected to the internal switch, on the same service network as the SFP+1 to SFP+4 ports. Therefore, a single network cable on the SFP+4 port is enough for the BMC management traffic to reach the external network over the service network, without interconnecting the MGMT port with a service port. The MGMT port can still be used for out-of-band management as described in the previous section. The upstream switch then connects to endpoints such as a personal computer (Personal PC).
    </CodeBlockTab>
</CodeBlockTabs>