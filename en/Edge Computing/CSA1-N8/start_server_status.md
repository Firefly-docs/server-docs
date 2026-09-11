# Server Status Identification

After the server is connected to the power outlet, the whole machine is powered on automatically.

Before using the server, check the indicators on the server to determine its current operating status.

![Front view](../../../servers_img/CSA1-N8/front_view.png)

Viewed from the front of the server, there are two rows of indicators. Each column of indicators corresponds to one core board: silkscreen 1 to 8 corresponds to sub-boards 1 to 8, and M corresponds to the BMC.

- **Upper row (yellow, network)**: Indicates the network data exchange status of the corresponding core board. When a core board is exchanging network data, the corresponding indicator blinks.
- **Lower row (green, system)**: Indicates the system running status of the corresponding core board. After a core board enters the system normally, the corresponding indicator stays steadily on; if a core board fails and cannot enter the system, the corresponding indicator is off.