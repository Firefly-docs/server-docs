# Sub-node Login

You can access a sub-node in three ways, choose the one that fits your situation:

1. **Web login**: Open the sub-node terminal directly in the aBMC Web interface;
2. **CLI command tool**: Use the `bmc` tool on the BMC; good for batch or scripted operations;
3. **Other methods**: Assign a static IP to the sub-node, then SSH into it from the maintenance PC.


## Web Login [step]

<CodeBlockTabs defaultValue="Shell">
    <CodeBlockTabsList>
        <CodeBlockTabsTrigger value="Shell">BMC Shell</CodeBlockTabsTrigger>
        <CodeBlockTabsTrigger value="Serial">Serial</CodeBlockTabsTrigger>
        <CodeBlockTabsTrigger value="ADB">ADB</CodeBlockTabsTrigger>
    </CodeBlockTabsList>

    <CodeBlockTab value="Shell">
      ### BMC Shell [step]
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

      When the terminal shows a prompt similar to `root@bmc:~#`, you are connected to the BMC management controller.

      ![aBMC Shell terminal](../../../servers_img/common/abmc_bmc_shell_terminal_en.png)

      <Callout title="Target of the Operations" type="warn">
        The BMC Shell operates on the BMC management controller, not the compute sub-nodes. Commands here change the BMC system directly, so confirm what a command does before running it.
      </Callout>
    </CodeBlockTab>

    <CodeBlockTab value="Serial">
      ### Web Serial Login [step]

      #### Open the Sub-node Debug Window [step]

      1. Select **Devices** in the left navigation bar.
      2. Select **General** in the device menu, then find the sub-node you need in the device list; prefer a node whose status is **Online** or **Ready**.
      3. Click the first terminal icon (**Open Shell Command**) in the **Shortcuts** column of the target node. If the page is too narrow, scroll the device list to the far right first.

      ![Open the sub-node debug window](../../../servers_img/common/abmc_open_subnode_debug_en.png)

      #### Select the Serial Debug Mode [step]

      1. Expand **Debug Mode** in the **Open Debugging** window.
      2. Select **Serial**.
      3. Click **Confirm** to open the serial terminal of the sub-node in a new browser window.

      ![Select the Serial debug mode](../../../servers_img/common/abmc_select_serial_debug_mode_en.png)

      #### Confirm the Serial Connection [step]

      1. Wait for the terminal to establish the connection.
      2. If the terminal is blank, click once in the black area and press **Enter** to wake up the serial output.
      3. When a sub-node prompt or login prompt appears, the serial connection is up. If the sub-node OS requires login, use that sub-node's own system account and password; `admin/admin` only logs in to the aBMC page.

      ![Sub-node Serial terminal](../../../servers_img/common/abmc_subnode_serial_terminal_en.png)
    </CodeBlockTab>

    <CodeBlockTab value="ADB">
      ### ADB Login [step]

      #### Open the Sub-node Debug Window [step]

      1. Select **Devices** in the left navigation bar.
      2. Select **General** in the device menu, then find the Android sub-node you need in the device list; prefer a node whose status is **Online** or **Ready**.
      3. Click the first terminal icon (**Open Shell Command**) in the **Shortcuts** column of the target node. If the page is too narrow, scroll the device list to the far right first.

      ![Open the sub-node ADB debug window](../../../servers_img/common/abmc_open_subnode_debug_en.png)

      #### Select the ADB Debug Mode [step]

      1. Confirm **Debug Mode** in the **Open Debugging** window.
      2. Select **ADB**.
      3. Click **Confirm** to open the ADB terminal of the sub-node in a new browser window.

      ![Select the ADB debug mode](../../../servers_img/common/abmc_select_adb_debug_mode_en.png)

      #### Confirm the ADB Connection [step]

      1. Wait for the terminal to establish an ADB connection with the target sub-node.
      2. If the terminal is blank, click once in the black area and press **Enter** to refresh the prompt.
      3. When a prompt similar to `CS_B1_rk3576_jd4_sub:/ #` appears, you are in the ADB Shell of the target sub-node.

      ![Sub-node ADB terminal](../../../servers_img/common/abmc_subnode_adb_terminal_en.png)
    </CodeBlockTab>
</CodeBlockTabs>


## CLI Command Tool Login [step]

`bmc` is a CLI tool bundled with aBMC. Run it on the BMC to connect to sub-nodes.

```bash
./bmc terminal <type> [aBMC connection parameters] --core <core board name>
```

- Parameters in `<>` are required; parameters in `[]` are optional.
- If you omit the aBMC connection parameters, the defaults are used: `https`, `127.0.0.1`, `443`, `admin`, `admin`.
- To exit: press `Ctrl+A`, then `Q`, then `Enter`.

Available connection parameters:

- `--protocol <http | https>`: aBMC service protocol;
- `--ip <bmc ip>`: aBMC service IP;
- `--port <aBMC service port>`: aBMC service listening port;
- `--user <aBMC service login user>`: aBMC login user;
- `--password <aBMC service login password>`: aBMC login password.

Pick the command for your sub-node type:

<CodeBlockTabs defaultValue="ADB">
    <CodeBlockTabsList>
        <CodeBlockTabsTrigger value="ADB">ADB</CodeBlockTabsTrigger>
        <CodeBlockTabsTrigger value="Serial">Serial</CodeBlockTabsTrigger>
        <CodeBlockTabsTrigger value="SSH">SSH</CodeBlockTabsTrigger>
    </CodeBlockTabsList>

    <CodeBlockTab value="ADB">
      ### ADB

      Connect to a sub-node over ADB:

      ```bash
      ./bmc terminal adb --core sub01
      ```
    </CodeBlockTab>

    <CodeBlockTab value="Serial">
      ### Serial

      Connect to a sub-node over Serial:

      ```bash
      ./bmc terminal serial --core sub01
      ```
    </CodeBlockTab>

    <CodeBlockTab value="SSH">
      ### SSH

      <Callout title="Tip" type="info">
        For a quick one-off session you can skip the `bmc` tool and run `ssh <sub-node user>@<sub-node IP>` directly.
      </Callout>

      Connect to a sub-node over SSH:

      ```bash
      ./bmc terminal ssh --protocol <http|https> --ip <aBMC service ip> --port <aBMC service port> --user <aBMC service login user> --password <aBMC service login password> --core <target core board> --ssh-user <username> --ssh-password <password> --ssh-port <ssh port>
      ```
    </CodeBlockTab>
</CodeBlockTabs>


## Other Login Methods [step]

<CodeBlockTabs defaultValue="SSH">
    <CodeBlockTabsList>
        <CodeBlockTabsTrigger value="SSH">SSH</CodeBlockTabsTrigger>
    </CodeBlockTabsList>

    <CodeBlockTab value="SSH">
      ### SSH Login [step]

      #### Configure a Static IPv4 Address for the Sub-node [step]

      1. Select **Devices** in the left navigation bar.
      2. Select **Network** in the device menu; you can also visit `https://172.16.100.172:443/#/deviceManage/boardNetManage` directly, replacing it with the management address and port of your device.
      3. Find the NIC for the shared network port of the target sub-node based on **Device Name**, **Net Card**, and **MAC Address**, then click **Configure** in that row.

      ![Open the sub-node network configuration](../../../servers_img/common/abmc_configure_subboard_network_en.png)

      <Callout title="NIC Selection" type="warn">
        You must select the sub-node NIC that matches the server's shared network port. Do not modify the `bmc/MGMT` management port or the NICs used for internal interconnection between sub-nodes; if you cannot tell them apart, check the product network port description, the NIC name, and the MAC address.
      </Callout>

      1. In the **IPv4 Configuration** tab, set **IPv4 Mode** to **Manual**.
      2. Fill in **Address** and **Subnet Mask** according to the network plan; fill in **Gateway** and **Gateway Priority** only if cross-subnet access is required.
      3. After checking that the address is not taken by another device, click **Confirm** to save.

      ![Configure the sub-node static IPv4](../../../servers_img/common/abmc_set_subboard_static_ipv4_en.png)

      The values below are examples; replace them with the addresses planned for your site:

      | Parameter | Example Value | Description |
      | --- | --- | --- |
      | Address | `192.168.10.10` | Static IPv4 address of the target sub-node's shared network port. |
      | Subnet Mask | `255.255.255.0` | Corresponds to a `/24` network prefix. |
      | Gateway | `192.168.10.1` | Used for cross-subnet access; not required when the PC and the sub-node are on the same Layer 2 network. |
      | Gateway Priority | `100` | Used when multiple gateways or default routes coexist; the value should conform to the site network plan. |
      | DNS | `114.114.114.114` | Not required when logging in via SSH using an IP address. |

      After saving, return to the **Network** page and check that the **IPv4 Address** of the target NIC has been updated. The sub-node's network connection may drop briefly while the change takes effect.

      #### Connect to the Server's Shared Network Port [step]

      1. Use a network cable to connect the maintenance PC to the switch.
      2. Confirm that the PC port and the server port belong to the same switching network and VLAN.
      3. Use a network cable to connect the server's shared network port to the same switch.

      ![Shared network port connection](../../../servers_img/common/pc_switch_shared_network_topology_steps.png)

      Set the maintenance PC to the same subnet as the sub-node's static IP, and make sure the address does not clash. For example, if the sub-node is `192.168.10.10/24`, the PC can be set to `192.168.10.100/24`.

      Test the network connectivity from the PC terminal:

      ```bash
      ping 192.168.10.10
      ```

      Once the sub-node replies, go ahead with the SSH login. If it does not respond, check the shared network port cabling, the switch VLAN, the PC IP, the sub-node static IP, and the firewall settings.

      #### Perform SSH Login on the PC [step]

      1. Open the PC's terminal, PowerShell, or another SSH client.
      2. Establish the connection using the sub-node operating system username and the static IP. The default SSH port is `22`.
      3. On the first connection, verify the host fingerprint, enter `yes` after confirming it is correct, and then enter the sub-node operating system password.

      ```bash
      ssh <SUBBOARD_USER>@<SUBBOARD_STATIC_IP>
      ```

      Using the example IP from this section:

      ```bash
      ssh <SUBBOARD_USER>@192.168.10.10
      ```

      If the SSH service uses a non-default port, specify the port with `-p`:

      ```bash
      ssh -p <SSH_PORT> <SUBBOARD_USER>@192.168.10.10
      ```

      When the target sub-node's command prompt appears, the SSH login has succeeded.

      <Callout title="SSH Login Credentials" type="warn">
        SSH uses the sub-node operating system account and password, not the aBMC Web `admin/admin`. Before logging in, confirm that the sub-node has the SSH service enabled, the target account is allowed to log in remotely, and the firewall permits the corresponding SSH port.
      </Callout>
    </CodeBlockTab>
</CodeBlockTabs>