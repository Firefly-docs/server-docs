# Accessing the BMC
## Login Notes
aBMC ships with a set of preset default parameters for initial commissioning. The table below summarizes the default login and network configuration.


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
      <td rowSpan="2">aBMC Management Network Port IPv4 Address<br/>● GEM network port</td>
      <td rowSpan="2">Management Network Port IP and Subnet Mask</td>
      <td>Default IP Address: 192.168.100.100</td>
    </tr>
    <tr>
      <td>Default Subnet Mask: 255.255.255.0</td>
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

This model does not provide a Console debug serial port. Web and SSH each have their own use case, and Web is recommended for routine operations:

- **Web**: Once the management network port is reachable from the operation PC, use the browser for system monitoring, firmware upgrades, and other operations;
- **SSH**: When you prefer the command line, or need batch or scripted operations.

Web and SSH login depend on the aBMC management IP, so complete the "Preparation" below first.

### Preparation
#### Server Network Cabling
Before logging in, connect the aBMC management network port to the LAN, and make sure the operation PC can reach the BMC management IP at Layer 3.
![PC-Switch-Server Basic Network Connection Topology Diagram](../../../servers_img/common/pc_switch_server_basic_network_topology.png)

Two types of management network ports are supported, choose as needed:
- **Shared network port**: Reuses the server's service NIC, carrying both service traffic and BMC management traffic;
- **Dedicated GEM network port**: An independent hardware port that carries only BMC management commands, isolating the service network.

#### Query the aBMC Management IP
Web and SSH login both require the aBMC management IP. Run the `ip` / `ifconfig` commands in the server's local Linux system to read the GEM network port IP address.
![GEM Port IP Query Command Output Screenshot](../../../servers_img/CSA1-N8/CSA1-Nx/eth0_ip_query_terminal_screenshot.png)

If you cannot access the server system to query it yet, use the default IP in the "Login Notes" table to log in first, then verify or change it.

The steps for each login method are described below.

<CodeBlockTabs defaultValue="Web_login">
    <CodeBlockTabsList>
        <CodeBlockTabsTrigger value="Web_login">Web</CodeBlockTabsTrigger>
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

      #### Open the BMC Shell Terminal
      To run commands directly on the BMC, open the BMC Shell from the aBMC Web page:
      1. After logging in to the aBMC Web page, select **Home** in the left navigation bar.
      2. Find **Remote console** in the **Quick access** area, confirm that the console type shows **BMC SHELL**, and click **Launch**.
          ![Open Remote console on the home page](../../../servers_img/common/abmc_home_remote_console_en.png)
      3. In the **Open Debugging** window, select **Shell** in **Debug Mode** and click **Confirm** to open the BMC terminal in a new browser window.
          ![Select the Shell debug mode](../../../servers_img/common/abmc_open_bmc_shell_en.png)
      4. When the terminal shows a prompt similar to `root@bmc:~#`, you are connected to the BMC management controller.

          ![aBMC Shell terminal](../../../servers_img/common/abmc_bmc_shell_terminal_en.png)

      <Callout title="Target of the Operations" type="warn">
        The BMC Shell operates on the BMC management controller, not the compute sub-nodes. Commands here change the BMC system directly, so confirm what a command does before running it; for sub-node login, see [Sub-node Login](start_login_sub.md).
      </Callout>
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