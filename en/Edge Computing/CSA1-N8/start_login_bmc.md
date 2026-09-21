# Accessing the BMC
## Login Notes
aBMC ships with a set of preset default parameters for initial commissioning. The table below summarizes the default login, network, and serial port configuration.


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
      <td rowSpan="2">aBMC Management Network Port IPv4 Address<br/>● MGMT or GM network port</td>
      <td rowSpan="2">Management Network Port IP and Subnet Mask</td>
      <td>Default IP Address: 192.168.100.100</td>
    </tr>
    <tr>
      <td>Default Subnet Mask: 255.255.255.0</td>
    </tr>
    <tr>
      <td rowSpan="2">BMC Console Serial Port</td>
      <td>Baud Rate</td>
      <td>115200</td>
    </tr>
    <tr>
      <td>Credentials</td>
      <td>No account or password required; press Enter to enter the BMC Linux system</td>
    </tr>
    <tr>
      <td rowSpan="2">BMC Linux User Data<br/>● Used for SSH login</td>
      <td>Login Username</td>
      <td>bmc or firefly</td>
    </tr>
    <tr>
      <td>Login Password</td>
      <td>Same as the username (that is, bmc/bmc or firefly/firefly)</td>
    </tr>
  </tbody>
</table>

<Callout title="Security Tip" type="warn">
Web and SSH share the same default username and password. Change them immediately after your first login and rotate passwords regularly to reduce the risk of device intrusion.
</Callout>

## Login Methods [step]

Each method has its own use case: Web is recommended for routine operations, and the other two serve as fallbacks:

- **Web**: Once the management network port is reachable from the operation PC, use the browser for system monitoring, firmware upgrades, and other operations;
- **Console**: When the network is unavailable, the system has not booted, or there is no display output, use the serial port to check low-level boot logs;
- **SSH**: When you prefer the command line, or need batch or scripted operations.

Web and SSH login depend on the aBMC management IP, so complete the "Preparation" below first; Console login needs no network, so you can go straight to the corresponding tab.

### Preparation
#### Server Network Cabling
Before logging in, connect the aBMC management network port to the LAN, and make sure the operation PC can reach the BMC management IP at Layer 3.
![PC-Switch-Server Basic Network Connection Topology Diagram](../../../servers_img/common/pc_switch_server_basic_network_topology.png)

Two types of management network ports are supported, choose as needed:
- **Shared network port**: Reuses the server's service NIC, carrying both service traffic and BMC management traffic;
- **Dedicated MGMT network port**: An independent hardware port that carries only BMC management commands, isolating the service network.

![MGMT Management Port Wiring Diagram](../../../servers_img/common/mgmt_port_cable_connection.png)

#### Query the aBMC Management IP
Web and SSH login both require the aBMC management IP. Run the `ip` / `ifconfig` commands in the server's local Linux system to read the MGMT network port IP address.
![MGMT Port IP Query Command Output Screenshot](../../../servers_img/common/mgnt_ip_query_terminal_screenshot.png)

If you cannot access the server system to query it yet, use the default IP in the "Login Notes" table to log in first, then verify or change it.

The steps for each login method are described below.

<CodeBlockTabs defaultValue="Web_login">
    <CodeBlockTabsList>
        <CodeBlockTabsTrigger value="Web_login">Web</CodeBlockTabsTrigger>
        <CodeBlockTabsTrigger value="Console_login">Console</CodeBlockTabsTrigger>
        <CodeBlockTabsTrigger value="Ssh_login">SSH</CodeBlockTabsTrigger>
    </CodeBlockTabsList>

    <CodeBlockTab value="Web_login">
      ### Web Remote Console Login
      aBMC provides a visual Web management interface for server-wide monitoring, hardware operations and maintenance, firmware upgrades, and other operations.

      #### Web Client Environment Requirements
      The browser compatibility and resolution standards are as follows:
      | Browser | Minimum Version | Resolution Requirement |
      | :--- | :--- | :--- |
      | Google Chrome | 48.0 or later | ≥1366×768, 1600×900 or later recommended |
      | Mozilla Firefox | 50.0 or later | ≥1366×768, 1600×900 or later recommended |
      | Internet Explorer | 11 or later | ≥1366×768, 1600×900 or later recommended |
      | Microsoft Edge | 97 or later | ≥1366×768, 1600×900 or later recommended |

      #### Web Page Login Steps
      Using the Chrome browser as an example:
      1. Enter `https://<aBMC management IP>` in the browser address bar; the first visit shows a certificate security warning.
          <Callout title="Login Reminder" type="info">
          The aBMC Web management interface uses **HTTPS** by default. Using the wrong protocol will cause Web login failure.
          </Callout>
          ![aBMC Certificate Warning Operation Schematic Diagram](../../../servers_img/common/abmc_chrome_cert_warning_schematic.png)
      2. Click `Advanced` on the page.
      3. Select `Proceed to (site) (unsafe)` to ignore the warning and go to the login page.
          ![aBMC Login Page Schematic Diagram](../../../servers_img/common/abmc_login_page.png)
      4. Enter the default account and password from the "Login Notes" table (`admin`/`admin`) to log in and enter the system overview dashboard:
          - Device panel: View the hardware running status of the ARM compute units and run underlying Shell commands;
          ![aBMC dashboard View](../../../servers_img/common/abmc_device_list.png)
          - Firmware upgrade page: Batch update the firmware of each compute unit;
          ![Add Firmware Upgrade Popup Schematic Diagram](../../../servers_img/common/abmc_fw_upgrade_popup.png)
          ![Firmware Upgrade Task Monitoring Page Schematic Diagram](../../../servers_img/common/abmc_fw_upgrade_monitor_page.png)
    </CodeBlockTab>

    <CodeBlockTab value="Console_login">
      ### Console Serial Login
      For cable selection and terminal connection, see [Server Serial Port](start_server_serial.md).
      1. Use an RJ45 serial cable to connect the server's Console port (the debug serial port) to the debug terminal;
      2. Set the terminal software parameters as follows:
          - Baud rate: 115200
          - Data bits: 8
          - Parity: None
          - Stop bits: 1
          - Flow control: None
      3. After the connection is established, press **Enter** to wake up the terminal and log in to the BMC Linux system (serial login requires no account or password);
      4. After logging in, you can run underlying system query commands.
        ![BMC OS Release Query Command Line Schematic Diagram](../../../servers_img/common/cmd_os_release_info.png)
    </CodeBlockTab>

    <CodeBlockTab value="Ssh_login">
      ### SSH Remote Login
      1. Use the system's built-in `ssh` tool or a terminal application such as MobaXterm locally;
      2. Enter the aBMC management IP to log in; the BMC Linux account is `bmc`/`bmc` or `firefly`/`firefly` (the password is the same as the username).

      For example:

      ```bash
      ssh bmc@<aBMC management IP>
      ```

      > The default account depends on the device version: try `bmc`/`bmc` first, and if that fails, use `firefly`/`firefly`.
    </CodeBlockTab>
</CodeBlockTabs>

## FAQ [step]
### Q: Where can I get the user manual? [step]
For full feature descriptions, refer to [aBMC Web User Manual](/docs/server/bmc-software/aBMC/preface).

### Q: What should I do if I cannot log in to the aBMC Web UI? [step]
If the access fails, refer to [Troubleshooting](/docs/server/bmc-software/aBMC/op_issues_troubleshooting).