# Sub-node Login

A compute sub-node (sub-board) has three connection channels: **Serial**, **ADB**, and **SSH**. Each channel can be used through any of the following three options:

| Option | Supported channels | When to use it |
| --- | --- | --- |
| Web console | Serial, ADB, SSH | Ad-hoc checks; point and click |
| BMC CLI `bmc` | Serial, ADB, SSH | Batch or scripted operations, no browser |
| Maintenance PC (direct) | SSH only | Long debugging sessions; the sub-node needs a static IP first |

All three options are built on the same network connection: complete the "Network Connection" section below first, then pick whichever fits your situation. The **Debug Mode** in the Web page only lists the channels the sub-node reports, so an individual sub-node may not provide all of them. This section covers sub-nodes only; to log in to the BMC management controller, see [Accessing the BMC](start_login_bmc.md).

## Prerequisite: Network Connection [step]

All three options require the same network connection; complete these two steps first:

1. **Server cabling**: Connect the server to the switch as described in [Network Wiring](start_server_network.md); use either the out-of-band or the in-band method.
2. **Connect the maintenance PC**: Connect the maintenance PC to the same switch, confirm that the PC port and the server port belong to the same switching network and VLAN, and make sure the PC can reach the aBMC management IP (for the default value and how to query it, see [Accessing the BMC](start_login_bmc.md)).

![Shared network port connection](../../../servers_img/common/pc_switch_shared_network_topology_steps.png)

After these two steps, "Option 1: Web Console" and "Option 2: BMC CLI" are ready to use; "Option 3: Direct Login from the Maintenance PC" needs one more step: assigning a static IP to the sub-node.

## Option 1: Web Console [step]

Open the sub-node terminal directly in the aBMC Web page.

### Open the Sub-node Debug Window

1. Select **Devices** in the left navigation bar.
2. Select **General** in the device menu, then find the sub-node you need in the device list; prefer a node whose status is **Online** or **Ready**.
3. Click the first terminal icon (**Open Shell Command**) in the **Shortcuts** column of the target node. If the page is too narrow, scroll the device list to the far right first.

![Open the sub-node debug window](../../../servers_img/common/abmc_open_subnode_debug_en.png)

### Select the Connection Channel and Connect

In the **Open Debugging** window, select the connection channel in **Debug Mode** and click **Confirm**; the terminal of the sub-node opens in a new browser window.

<CodeBlockTabs defaultValue="Serial">
    <CodeBlockTabsList>
        <CodeBlockTabsTrigger value="Serial">Serial</CodeBlockTabsTrigger>
        <CodeBlockTabsTrigger value="ADB">ADB</CodeBlockTabsTrigger>
        <CodeBlockTabsTrigger value="SSH">SSH</CodeBlockTabsTrigger>
    </CodeBlockTabsList>

    <CodeBlockTab value="Serial">
      **Serial**: works for all sub-nodes; it also shows low-level boot logs when the system has not started or shows no output.

      1. Confirm that **Debug Mode** is **Serial** and click **Confirm**.
      2. In the new window, wait for the terminal to connect; if the terminal stays blank, click once in the black area and press **Enter** to wake up the serial output.
      3. When a sub-node or login prompt appears, the serial connection is up.

      ![Select the Serial debug mode](../../../servers_img/common/abmc_select_serial_debug_mode_en.png)

      ![Sub-node Serial terminal](../../../servers_img/common/abmc_subnode_serial_terminal_en.png)
    </CodeBlockTab>

    <CodeBlockTab value="ADB">
      **ADB**: works for sub-nodes that support ADB; Android sub-nodes usually provide it by default, and some Linux sub-nodes do as well.

      1. Confirm that **Debug Mode** is **ADB** and click **Confirm**.
      2. In the new window, wait for the terminal to establish an ADB connection; if the terminal stays blank, click once in the black area and press **Enter** to refresh the prompt.
      3. When a prompt similar to `CS_B1_rk3576_jd4_sub:/ #` appears, you are in the ADB Shell of the target sub-node.

      ![Select the ADB debug mode](../../../servers_img/common/abmc_select_adb_debug_mode_en.png)

      ![Sub-node ADB terminal](../../../servers_img/common/abmc_subnode_adb_terminal_en.png)
    </CodeBlockTab>

    <CodeBlockTab value="SSH">
      **SSH**: works for sub-nodes with the SSH service enabled; it logs in to the sub-node operating system directly.

      1. Select **SSH** in **Debug Mode**.
      2. Select or enter the sub-node operating system username in **User**.
      3. Select the authentication method in **Login Method**: for account and password, fill in the password and the port (default `22`); for key authentication, provide the corresponding private key.
      4. Click **Confirm** to open the sub-node terminal in a new browser window; a command prompt means the login succeeded.
    </CodeBlockTab>
</CodeBlockTabs>

<Callout title="Login Credentials" type="warn">
  Serial and SSH log in to the sub-node's own operating system, so use that sub-node's system account and password; `admin/admin` only logs in to the aBMC page. ADB is a debugging channel and normally enters the Shell directly.
</Callout>

## Option 2: BMC CLI [step]

`bmc` is a CLI tool bundled with aBMC. Run it on the BMC to connect to sub-nodes; it is a good fit for batch or scripted operations. If you omit the aBMC connection parameters, the defaults are used: `https`, `127.0.0.1`, `443`, `admin`, `admin` (use `--protocol`, `--ip`, `--port`, `--user`, or `--password` to override them); to exit, press `Ctrl+A`, `Q`, `Enter`. Pick the command for your connection channel:

<CodeBlockTabs defaultValue="ADB">
    <CodeBlockTabsList>
        <CodeBlockTabsTrigger value="ADB">ADB</CodeBlockTabsTrigger>
        <CodeBlockTabsTrigger value="Serial">Serial</CodeBlockTabsTrigger>
        <CodeBlockTabsTrigger value="SSH">SSH</CodeBlockTabsTrigger>
    </CodeBlockTabsList>

    <CodeBlockTab value="ADB">
      **ADB**: works for sub-nodes that support ADB; put the target core board name after `--core`.

      ```bash
      ./bmc terminal adb --core sub01
      ```
    </CodeBlockTab>

    <CodeBlockTab value="Serial">
      **Serial**: also works when the network is down or the system has not started; put the target core board name after `--core`.

      ```bash
      ./bmc terminal serial --core sub01
      ```
    </CodeBlockTab>

    <CodeBlockTab value="SSH">
      **SSH**: requires both the aBMC service parameters and the sub-node operating system credentials.

      ```bash
      ./bmc terminal ssh --protocol <http|https> --ip <aBMC service ip> --port <aBMC service port> --user <aBMC service login user> --password <aBMC service login password> --core <target core board> --ssh-user <username> --ssh-password <password> --ssh-port <ssh port>
      ```

      <Callout title="Tip" type="info">
        If the sub-node already reaches your management network, you can skip the `bmc` tool and follow "Option 3: Direct Login from the Maintenance PC" below to run `ssh <sub-node user>@<sub-node IP>` from a PC.
      </Callout>
    </CodeBlockTab>
</CodeBlockTabs>

## Option 3: Direct Login from the Maintenance PC [step]

When the sub-node and the management network can reach each other, you can log in to the sub-node system over SSH directly from the maintenance PC, without the Web page or the `bmc` tool.

On top of "Network Connection", this path adds two steps: assigning a static IPv4 address to the sub-node, then verifying the connectivity between the PC and the sub-node. It relies on the sub-node NIC that connects to the server's **shared network port**: the server port that reuses a service NIC and carries both service traffic and management traffic (see [Accessing the BMC](start_login_bmc.md)), which reaches the sub-nodes through the internal switch.

### Configure a Static IPv4 Address for the Sub-node

<Callout title="NIC Selection" type="warn">
  You must select the sub-node NIC that connects to the shared network port. Do not modify the `bmc/MGMT` management port or the NICs used for internal interconnection between sub-nodes; if you cannot tell them apart, check the product network port description, the NIC name, and the MAC address.
</Callout>

1. Select **Devices** → **Network** in the left navigation bar; you can also visit `https://172.16.100.172:443/#/deviceManage/boardNetManage` directly, replacing it with the management address and port of your device.
2. Find the sub-node NIC that connects to the shared network port based on **Device Name**, **Net Card**, and **MAC Address**, then click **Configure** in that row.
3. In the **IPv4 Configuration** tab, set **IPv4 Mode** to **Manual**, then fill in **Address** and **Subnet Mask**; fill in **Gateway** and **Gateway Priority** only if cross-subnet access is required.
4. After checking that the address is not taken by another device, click **Confirm** to save, then return to the **Network** page and check that the **IPv4 Address** of the NIC has been updated. The sub-node's network connection may drop briefly while the change takes effect.

Locate the target NIC on the **Network** page by **Device Name → Net Card → MAC Address**:

![Open the sub-node network configuration](../../../servers_img/common/abmc_configure_subboard_network_en.png)

The **IPv4 Configuration** tab that **Configure** opens:

![Configure the sub-node static IPv4](../../../servers_img/common/abmc_set_subboard_static_ipv4_en.png)

In the example above, `Address` is `192.168.10.10` and `Subnet Mask` is `255.255.255.0` (that is `/24`). Replace them with the addresses planned for your site; `Gateway` can be left empty when the PC and the sub-node are on the same Layer 2 network.

### Verify the Connectivity Between the PC and the Sub-node

Set the maintenance PC to the same subnet as the sub-node's static IP, and make sure the address does not clash (if the sub-node is `192.168.10.10/24`, the PC can be set to `192.168.10.100/24`), then test the connectivity:

```bash
ping 192.168.10.10
```

Once the sub-node replies, go ahead with the SSH login. If it does not respond, check in this order: shared network port cabling, switch VLAN, PC IP, sub-node static IP, firewall settings.

### Perform SSH Login on the PC

1. Open the PC's terminal, PowerShell, or another SSH client, and run `ssh <sub-node user>@<sub-node static IP>` (the default port is `22`; use `-p` for a non-default port).
2. On the first connection, verify the host fingerprint, enter `yes` after confirming it is correct, and then enter the sub-node operating system password.
3. When the target sub-node's command prompt appears, the SSH login has succeeded.

Using the example IP from this section:

```bash
ssh <SUBBOARD_USER>@192.168.10.10
ssh -p <SSH_PORT> <SUBBOARD_USER>@192.168.10.10   # non-default SSH port
```

<Callout title="SSH Login Credentials" type="warn">
  SSH uses the sub-node operating system account and password, not the aBMC Web `admin/admin`. Before logging in, confirm that the sub-node has the SSH service enabled, the target account is allowed to log in remotely, and the firewall permits the corresponding SSH port.
</Callout>

## FAQ [step]
### 1.Where can I get the user manual?
For full feature descriptions, refer to [aBMC Web User Manual](/docs/server/bmc-software/aBMC/preface).

### 2.What should I do if none of the login methods works?
Refer to [Troubleshooting](/docs/server/bmc-software/aBMC/op_issues_troubleshooting).