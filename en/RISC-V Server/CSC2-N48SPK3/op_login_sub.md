# Sub-nodes

The mainstream ways to access a sub-node are:
1. Web login provided by aBMC
2. Login via the CLI command tool provided by aBMC
3. Login via other methods


## Web Login [step]

<CodeBlockTabs defaultValue="Shell">
    <CodeBlockTabsList>
        <CodeBlockTabsTrigger value="Shell">Shell</CodeBlockTabsTrigger>
        <CodeBlockTabsTrigger value="Serial">Serial</CodeBlockTabsTrigger>
        <CodeBlockTabsTrigger value="ADB">ADB</CodeBlockTabsTrigger>
    </CodeBlockTabsList>

    <CodeBlockTab value="Shell">
      ### Web Shell [step]
      #### Open the aBMC Shell Entry [step]

      1. After logging in to the aBMC Web page, select **Home** in the left navigation bar.
      2. Find **Remote console** in the **Quick access** area on the right.
      3. Confirm that the console type shows **BMC SHELL**, and click **Launch**.

      ![Open Remote console on the home page](../../../servers_img/common/abmc_home_remote_console_en.png)

      #### Select the Shell Debug Mode [step]

      1. Confirm that the **Open Debugging** window has opened.
      2. Select **Shell** in **Debug Mode**.
      3. Click **Confirm** to open the BMC terminal in a new browser window.

      ![Select the Shell debug mode](../../../servers_img/common/abmc_open_bmc_shell_en.png)

      #### Confirm the Shell Connection [step]

      When the terminal shows a prompt similar to `root@bmc:~#`, it means you are connected to the BMC management controller and can execute the required BMC system maintenance commands.

      ![aBMC Shell terminal](../../../servers_img/common/abmc_bmc_shell_terminal_en.png)

      <Callout title="Target of the Operations" type="warn">
        The aBMC Shell operates on the BMC management controller, not the compute sub-nodes. Commands executed in the terminal directly affect the BMC system; confirm the purpose and impact scope of a command before executing it.
      </Callout>
    </CodeBlockTab>

    <CodeBlockTab value="Serial">

      ### Web Serial Login [step]

      #### Open the Sub-node Debug Window [step]

      1. Select **Devices** in the left navigation bar.
      2. Select **General** in the device menu, and find the sub-node to access in the device list. It is recommended to select a node whose status is **Online** or **Ready**.
      3. Click the first terminal icon in the **Shortcuts** column of the target node, i.e., **Open Shell Command**. If the page is not wide enough, scroll the device list horizontally to the far right first.

      ![Open the sub-node debug window](../../../servers_img/common/abmc_open_subnode_debug_en.png)

      #### Select the Serial Debug Mode [step]

      1. Expand **Debug Mode** in the **Open Debugging** window.
      2. Select **Serial**.
      3. Click **Confirm** to open the serial terminal of the sub-node in a new browser window.

      ![Select the Serial debug mode](../../../servers_img/common/abmc_select_serial_debug_mode_en.png)

      #### Confirm the Serial Connection [step]

      1. Wait for the terminal to establish the connection.
      2. If the terminal is temporarily blank, click once in the black terminal area and then press **Enter** to wake up the serial output.
      3. When the terminal shows the sub-node prompt or a login prompt, the serial connection is successful. If the sub-node operating system requires login, use the system account and password of the sub-node itself; the Web account `admin/admin` is only used to log in to the aBMC page.

      ![Sub-node Serial terminal](../../../servers_img/common/abmc_subnode_serial_terminal_en.png)
      </CodeBlockTab>

    <CodeBlockTab value="ADB">
      ### ADB Login [step]

      #### Open the Sub-node Debug Window [step]

      1. Select **Devices** in the left navigation bar.
      2. Select **General** in the device menu, and find the Android sub-node to access in the device list. It is recommended to select a node whose status is **Online** or **Ready**.
      3. Click the first terminal icon in the **Shortcuts** column of the target node, i.e., **Open Shell Command**. If the page is not wide enough, scroll the device list horizontally to the far right first.

      ![Open the sub-node ADB debug window](../../../servers_img/common/abmc_open_subnode_debug_en.png)

      #### Select the ADB Debug Mode [step]

      1. Confirm **Debug Mode** in the **Open Debugging** window.
      2. Select **ADB**.
      3. Click **Confirm** to open the ADB terminal of the sub-node in a new browser window.

      ![Select the ADB debug mode](../../../servers_img/common/abmc_select_adb_debug_mode_en.png)

      #### Confirm the ADB Connection [step]

      1. Wait for the terminal to establish an ADB connection with the target sub-node.
      2. If the terminal is temporarily blank, click once in the black terminal area and then press **Enter** to refresh the prompt.
      3. When the terminal shows a prompt similar to `CS_B1_rk3576_jd4_sub:/ #`, it means you have entered the ADB Shell of the target sub-node and can execute the required node maintenance commands.

      ![Sub-node ADB terminal](../../../servers_img/common/abmc_subnode_adb_terminal_en.png)
    </CodeBlockTab>
</CodeBlockTabs>


## CLI Command Tool Login [step]

The bmc command is a CLI command-line tool built into the aBMC software. Users can connect to sub-boards through the bmc command-line tool.
  ```bash
    ./bmc terminal <type> [aBMC connection parameters] <--core core-board-name>
    * Parameters in <> are required
    * Parameters in [] are optional.
    * To exit: first press Ctrl+A (case-insensitive), then press Q (case-insensitive), and finally press Enter to confirm the exit.
    * Parameter options:

      --protocol <http | https>
      --ip <bmc ip>
      --port <aBMC service listening port>
      --user <aBMC service login user>
      --password <aBMC service login password>
      When the above parameters are omitted, the default values are used: https, 127.0.0.1, 443, admin, admin.
  ```
<CodeBlockTabs defaultValue="ADB">
    <CodeBlockTabsList>
        <CodeBlockTabsTrigger value="ADB">SSH</CodeBlockTabsTrigger>
        <CodeBlockTabsTrigger value="Serial">Network ADB</CodeBlockTabsTrigger>
        <CodeBlockTabsTrigger value="SSH">SSH</CodeBlockTabsTrigger>
    </CodeBlockTabsList>

    <CodeBlockTab value="ADB">
    ### ADB
      Connect to the sub-board via ADB
      ```bash
        ./bmc terminal adb --core sub01
      ```
    </CodeBlockTab>

    <CodeBlockTab value="Serial">
      ### Serial
      Connect to the sub-board via Serial
      ```bash
        ./bmc terminal serial --core sub01
      ```
    </CodeBlockTab>

    <CodeBlockTab value="SSH">
      ### SSH
      <Callout title="Tip" type="info">
          If you choose the ssh method, you can skip the bmc command-line tool and directly use `ssh sub-board-username@sub-board-ip` to connect, which is more convenient.
      </Callout>
      Connect to the sub-board via ssh
      ```bash
        ./bmc terminal ssh --protocol <http|https> --ip <aBMC service ip> --port <aBMC service port> --user <aBMC service login user> --password <aBMC service login password> --core <target core board> --ssh-user <username> --ssh-password <password> --ssh-port <ssh port>
      ```
    </CodeBlockTab>
</CodeBlockTabs>



## Other Login Methods [step]

<CodeBlockTabs defaultValue="SSH">
    <CodeBlockTabsList>
        <CodeBlockTabsTrigger value="SSH">SSH</CodeBlockTabsTrigger>
        <CodeBlockTabsTrigger value="NetADB">Network ADB</CodeBlockTabsTrigger>
    </CodeBlockTabsList>

    <CodeBlockTab value="SSH">
      ### SSH Login [step]

      #### Configure a Static IPv4 Address for the Sub-board [step]

      1. Select **Devices** in the left navigation bar.
      2. Select **Network** in the device menu. You can also directly visit `http://172.16.100.172:443/#/deviceManage/boardNetManage`; in actual use, replace it with the management address and port of your device.
      3. Find the NIC corresponding to the shared network port of the target sub-board based on **Device Name**, **Net Card**, and **MAC Address**, and then click **Configure** in that row.

      ![Open the sub-board network configuration](../../../servers_img/common/abmc_configure_subboard_network_en.png)

      <Callout title="NIC Selection" type="warn">
        You must select the sub-board NIC corresponding to the server's shared network port. Do not modify the `bmc/MGMT` management port or the NICs used for internal interconnection between sub-boards; if you cannot confirm the interface, verify it against the product network port description, the NIC name, and the MAC address.
      </Callout>

      1. In the **IPv4 Configuration** tab, set **IPv4 Mode** to **Manual**.
      2. Fill in **Address** and **Subnet Mask** according to the network plan; fill in **Gateway** and **Gateway Priority** only if cross-subnet access is required.
      3. After checking that the address is not occupied by other devices, click **Confirm** to save the configuration.

      ![Configure the sub-board static IPv4](../../../servers_img/common/abmc_set_subboard_static_ipv4_en.png)

      The figure uses the following example configuration, which must be replaced with the addresses planned for the site in actual deployment:

      | Parameter | Example Value | Description |
      | --- | --- | --- |
      | Address | `192.168.10.10` | Static IPv4 address of the target sub-board's shared network port. |
      | Subnet Mask | `255.255.255.0` | Corresponds to a `/24` network prefix. |
      | Gateway | `192.168.10.1` | Used for cross-subnet access; not required when the PC and the sub-board are on the same Layer 2 network. |
      | Gateway Priority | `100` | Used when multiple gateways or default routes coexist; the value should conform to the site network plan. |
      | DNS | `114.114.114.114` | Not required when logging in via SSH using an IP address. |

      After saving, return to the **Network** page and confirm that the **IPv4 Address** of the target NIC has been updated. While the network configuration is taking effect, the network connection of the sub-board may be briefly interrupted.

      #### Connect to the Server's Shared Network Port [step]

      1. Use a network cable to connect the maintenance PC to the switch.
      2. Confirm that the PC port and the server port belong to the same switching network and VLAN.
      3. Use a network cable to connect the server's shared network port to the same switch.

      ![Shared network port connection](../../../servers_img/common/pc_switch_shared_network_topology_steps.png)

      Set the maintenance PC to the same subnet as the sub-board's static IP, and the address must not be duplicated. For example, if the sub-board is `192.168.10.10/24`, the PC can be set to `192.168.10.100/24`.

      Test the network connectivity in the PC terminal:

      ```bash
      ping 192.168.10.10
      ```

      After receiving replies from the sub-board, proceed with the SSH login. If the connection cannot be established, check the shared network port cabling, the switch VLAN, the PC IP, the sub-board static IP, and the firewall configuration.

      #### Perform SSH Login on the PC [step]

      1. Open the PC's terminal, PowerShell, or another SSH client.
      2. Establish the connection using the sub-board operating system username and the static IP. The default SSH port is `22`.
      3. On the first connection, verify the host fingerprint, enter `yes` after confirming it is correct, and then enter the sub-board operating system password.

      ```bash
      ssh <SUBBOARD_USER>@<SUBBOARD_STATIC_IP>
      ```

      The following command uses the example IP from this section:

      ```bash
      ssh <SUBBOARD_USER>@192.168.10.10
      ```

      If the SSH service uses a non-default port, specify the port with `-p`:

      ```bash
      ssh -p <SSH_PORT> <SUBBOARD_USER>@192.168.10.10
      ```

      When the terminal shows the command prompt of the target sub-board, the SSH login is successful.

      <Callout title="SSH Login Credentials" type="warn">
        SSH uses the sub-board operating system account and password, not the aBMC Web `admin/admin`. Before logging in, confirm that the sub-board has the SSH service enabled, the target account is allowed to log in remotely, and the firewall permits the corresponding SSH port.
      </Callout>

    </CodeBlockTab>

    <CodeBlockTab value="NetADB">
    ### Network ADB [step]
    </CodeBlockTab>

</CodeBlockTabs>