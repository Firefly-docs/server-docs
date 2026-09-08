# Accessing the Server (BMC)
## Login Notes
aBMC presets default parameters at the factory for initial commissioning. The following table lists the default login, network, and serial port configuration. For device security, be sure to change the default account password on first login and rotate it regularly.


<table border="1" cellPadding="8" cellSpacing="0" width="100%">
  <thead>
    <tr>
      <th>Category</th>
      <th>Item</th>
      <th>Default Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td rowSpan="2">aBMC Management System Data</td>
      <td>Login Username</td>
      <td>admin</td>
    </tr>
    <tr>
      <td>Login Password</td>
      <td>admin</td>
    </tr>
    <tr>
      <td rowSpan="2">aBMC Management Network Port IPv4 Address<br/> GE M network port</td>
      <td rowSpan="2">Management Network Port IP and Subnet Mask</td>
      <td>Default IP Address: 192.168.100.100</td>
    </tr>
    <tr>
      <td>Default Subnet Mask: 255.255.255.0</td>
    </tr>
    <tr>
      <td rowSpan="2">BMC Linux User Data</td>
      <td>Login Username</td>
      <td>bmc</td>
    </tr>
    <tr>
      <td>Login Password</td>
      <td>bmc</td>
    </tr>
  </tbody>
</table>

## 1 Web Remote Console Login
aBMC provides a visual Web management interface for server-wide monitoring, hardware operations and maintenance, firmware upgrades, and other operations.

### 1.1 Environment Preparation
#### 1.1.1 Server Network Cabling
Before logging in, connect the aBMC management network port to the LAN and ensure connectivity between the operation PC and the BMC management IP.
![PC-Switch-Server Basic Network Connection Topology Diagram](../../../servers_img/common/pc_switch_server_basic_network_topology.png)

Two types of management network ports are supported; choose as needed:
- **Shared network port**: Reuses the server's service NIC, carrying both service traffic and BMC management traffic;
- **Dedicated eth0 network port**: An independent hardware network port that carries only BMC management commands, isolating the service network.

#### 1.1.2 Query the aBMC Management IP
You can run the `ip` / `ifconfig` commands in the server's local Linux system to read the eth0 network port IP address.
![ETH0 Port IP Query Command Output Screenshot](../../../servers_img/CSA1-N8/CSA1-Nx/eth0_ip_query_terminal_screenshot.png)

### 1.2 Web Client Environment Requirements
The browser compatibility and resolution standards are as follows:
| Browser | Minimum Version | Resolution Requirement |
| :--- | :--- | :--- |
| Google Chrome | 48.0 or later | ≥1366*768, 1600*900 or later recommended |
| Mozilla Firefox | 50.0 or later | ≥1366*768, 1600*900 or later recommended |
| Internet Explorer | 11 or later | ≥1366*768, 1600*900 or later recommended |
| Microsoft Edge | 97 or later | ≥1366*768, 1600*900 or later recommended |

### 1.3 Web Page Login Steps
Using the Chrome browser as an example:
1. Enter `https://aBMC-management-IP` in the browser address bar; a certificate security warning will pop up when accessing.
    ![aBMC Certificate Warning Operation Schematic Diagram](../../../servers_img/common/abmc_chrome_cert_warning_schematic.png)
2. Click `Advanced` on the page;
3. Select `Proceed to (site) (unsafe)` to ignore the certificate warning and jump to the login page.
    ![aBMC Login Page Schematic Diagram](../../../servers_img/common/abmc_login_page.png)
4. Enter the default account and password to log in and enter the system overview dashboard:
    - Device panel: View the hardware running status of the ARM compute units and execute underlying Shell commands;
    ![aBMC dashboard View](../../../servers_img/common/abmc_device_list.png)
    - Firmware upgrade page: Batch update the firmware of each compute unit;
    ![Add Firmware Upgrade Popup Schematic Diagram](../../../servers_img/common/abmc_fw_upgrade_popup.png)
    ![Firmware Upgrade Task Monitoring Page Schematic Diagram](../../../servers_img/common/abmc_fw_upgrade_monitor_page.png)

> Security tip: On first login, change the default account password immediately and update it regularly to reduce the risk of device intrusion.
> For complete feature descriptions, refer to the accompanying "aBMC User Guide".

## 2 SSH Remote Login
1. Use the system's built-in `ssh` tool or a terminal application such as MobaXterm locally;
2. Enter the aBMC management IP and the default account and password to complete the login.