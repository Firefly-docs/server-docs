# 子节点登录

访问子节点有三种方式，按现场条件任选其一：

1. **Web 登录**：在 aBMC Web 界面中直接打开子节点的终端；
2. **CLI 命令工具登录**：在 BMC 命令行中用 `bmc` 工具连接子节点，适合批量或脚本化操作；
3. **其他方式登录**：先给子节点配好静态 IP，再从维护电脑直接 SSH 登录子节点系统。


## Web 登录 [step]

<CodeBlockTabs defaultValue="Shell">
    <CodeBlockTabsList>
        <CodeBlockTabsTrigger value="Shell">BMC Shell</CodeBlockTabsTrigger>
        <CodeBlockTabsTrigger value="Serial">Serial</CodeBlockTabsTrigger>
        <CodeBlockTabsTrigger value="ADB">ADB</CodeBlockTabsTrigger>
    </CodeBlockTabsList>

    <CodeBlockTab value="Shell">
      ### BMC Shell [step]
      #### 打开 aBMC Shell 入口 [step]

      1. 登录 aBMC Web 页面后，在左侧导航栏中选择 **Home**。
      2. 在右侧 **Quick access** 区域找到 **Remote console**。
      3. 确认控制台类型显示为 **BMC SHELL**，单击 **Launch**。

      ![打开首页 Remote console](../../../servers_img/common/abmc_home_remote_console_en.png)

      #### 选择 Shell 调试模式 [step]

      1. 确认页面已经打开 **Open Debugging** 窗口。
      2. 在 **Debug Mode** 中选择 **Shell**。
      3. 单击 **Confirm**，在新浏览器窗口中打开 BMC 终端。

      ![选择 Shell 调试模式](../../../servers_img/common/abmc_open_bmc_shell_en.png)

      #### 确认 Shell 连接 [step]

      终端出现类似 `root@bmc:~#` 的提示符，说明已经连上 BMC 管理控制器。

      ![aBMC Shell 终端](../../../servers_img/common/abmc_bmc_shell_terminal_en.png)

      <Callout title="操作对象说明" type="warn">
        BMC Shell 操作的是 BMC 管理控制器，不是计算子节点。命令会直接改动 BMC 系统，执行前请确认它的作用。
      </Callout>
    </CodeBlockTab>

    <CodeBlockTab value="Serial">
      ### Web 串口登录 [step]

      #### 打开子节点调试窗口 [step]

      1. 在左侧导航栏中选择 **Devices**。
      2. 在设备菜单中选择 **General**，在设备列表里找到要访问的子节点，优先选状态为 **Online** 或 **Ready** 的节点。
      3. 在目标节点 **Shortcuts** 列中单击第一个终端图标（**Open Shell Command**）。页面宽度不足时，先把设备列表横向滚动到最右侧。

      ![打开子节点调试窗口](../../../servers_img/common/abmc_open_subnode_debug_en.png)

      #### 选择 Serial 调试模式 [step]

      1. 在 **Open Debugging** 窗口中展开 **Debug Mode**。
      2. 选择 **Serial**。
      3. 单击 **Confirm**，在新浏览器窗口中打开该子节点的串口终端。

      ![选择 Serial 调试模式](../../../servers_img/common/abmc_select_serial_debug_mode_en.png)

      #### 确认串口连接 [step]

      1. 等待终端建立连接。
      2. 终端暂时空白时，在黑色区域单击一次，再按 **Enter** 唤醒串口输出。
      3. 终端出现子节点提示符或登录提示，说明串口已经连通。子节点系统如需登录，请用该子节点自己的系统账号密码；`admin/admin` 只能登录 aBMC 页面。

      ![子节点 Serial 终端](../../../servers_img/common/abmc_subnode_serial_terminal_en.png)
    </CodeBlockTab>

    <CodeBlockTab value="ADB">
      ### ADB 登录 [step]

      #### 打开子节点调试窗口 [step]

      1. 在左侧导航栏中选择 **Devices**。
      2. 在设备菜单中选择 **General**，在设备列表里找到要访问的 Android 子节点，优先选状态为 **Online** 或 **Ready** 的节点。
      3. 在目标节点 **Shortcuts** 列中单击第一个终端图标（**Open Shell Command**）。页面宽度不足时，先把设备列表横向滚动到最右侧。

      ![打开子节点 ADB 调试窗口](../../../servers_img/common/abmc_open_subnode_debug_en.png)

      #### 选择 ADB 调试模式 [step]

      1. 在 **Open Debugging** 窗口中确认 **Debug Mode**。
      2. 选择 **ADB**。
      3. 单击 **Confirm**，在新浏览器窗口中打开该子节点的 ADB 终端。

      ![选择 ADB 调试模式](../../../servers_img/common/abmc_select_adb_debug_mode_en.png)

      #### 确认 ADB 连接 [step]

      1. 等待终端与目标子节点建立 ADB 连接。
      2. 终端暂时空白时，在黑色区域单击一次，再按 **Enter** 刷新提示符。
      3. 终端出现类似 `CS_B1_rk3576_jd4_sub:/ #` 的提示符，说明已经进入目标子节点的 ADB Shell。

      ![子节点 ADB 终端](../../../servers_img/common/abmc_subnode_adb_terminal_en.png)
    </CodeBlockTab>
</CodeBlockTabs>


## CLI 命令工具登录 [step]

`bmc` 是 aBMC 自带的 CLI 工具，在 BMC 上执行即可连接各子节点。

```bash
./bmc terminal <类型> [aBMC 连接参数] --core <核心板名称>
```

- `<>` 中的参数必填，`[]` 中的参数可选。
- 省略 aBMC 连接参数时使用默认值：`https`、`127.0.0.1`、`443`、`admin`、`admin`。
- 退出方式：依次按 `Ctrl+A`、`Q`、`Enter`。

可用的连接参数：

- `--protocol <http | https>`：aBMC 服务协议；
- `--ip <bmc ip>`：aBMC 服务 IP；
- `--port <aBMC 服务端口>`：aBMC 服务监听端口；
- `--user <aBMC 服务登录用户>`：aBMC 登录用户名；
- `--password <aBMC 服务登录密码>`：aBMC 登录密码。

按子节点类型选择对应命令：

<CodeBlockTabs defaultValue="ADB">
    <CodeBlockTabsList>
        <CodeBlockTabsTrigger value="ADB">ADB</CodeBlockTabsTrigger>
        <CodeBlockTabsTrigger value="Serial">Serial</CodeBlockTabsTrigger>
        <CodeBlockTabsTrigger value="SSH">SSH</CodeBlockTabsTrigger>
    </CodeBlockTabsList>

    <CodeBlockTab value="ADB">
      ### ADB

      通过 ADB 连接子节点：

      ```bash
      ./bmc terminal adb --core sub01
      ```
    </CodeBlockTab>

    <CodeBlockTab value="Serial">
      ### Serial

      通过 Serial 连接子节点：

      ```bash
      ./bmc terminal serial --core sub01
      ```
    </CodeBlockTab>

    <CodeBlockTab value="SSH">
      ### SSH

      <Callout title="提示" type="info">
        临时调试时可以不借助 `bmc` 工具，直接执行 `ssh <子节点用户名>@<子节点 IP>` 登录更省事。
      </Callout>

      通过 SSH 连接子节点：

      ```bash
      ./bmc terminal ssh --protocol <http|https> --ip <aBMC 服务 ip> --port <aBMC 服务端口> --user <aBMC 服务登录用户> --password <aBMC 服务登录密码> --core <目标核心板> --ssh-user <用户名> --ssh-password <密码> --ssh-port <ssh 端口>
      ```
    </CodeBlockTab>
</CodeBlockTabs>


## 其他方式登录 [step]

<CodeBlockTabs defaultValue="SSH">
    <CodeBlockTabsList>
        <CodeBlockTabsTrigger value="SSH">SSH</CodeBlockTabsTrigger>
    </CodeBlockTabsList>

    <CodeBlockTab value="SSH">
      ### SSH 登录 [step]

      #### 配置子节点静态 IPv4 地址 [step]

      1. 在左侧导航栏中选择 **Devices**。
      2. 在设备菜单中选择 **Network**；也可以直接访问 `https://172.16.100.172:443/#/deviceManage/boardNetManage`，实际使用时请替换为设备的管理地址和端口。
      3. 根据 **Device Name**、**Net Card** 和 **MAC Address** 找到目标子节点共享网口对应的网卡，单击该行的 **Configure**。

      ![打开子节点网络配置](../../../servers_img/common/abmc_configure_subboard_network_en.png)

      <Callout title="网卡选择" type="warn">
        必须选择与服务器共享网口对应的子节点网卡。不要修改 `bmc/MGMT` 管理口或子节点内部互联使用的网卡；无法确认时，请对照产品网口说明、网卡名称和 MAC 地址核对。
      </Callout>

      1. 在 **IPv4 Configuration** 页签中，将 **IPv4 Mode** 设置为 **Manual**。
      2. 按网络规划填写 **Address** 和 **Subnet Mask**；需要跨网段访问时，再填写 **Gateway** 和 **Gateway Priority**。
      3. 确认该地址没有被其他设备占用后，单击 **Confirm** 保存。

      ![配置子节点静态 IPv4](../../../servers_img/common/abmc_set_subboard_static_ipv4_en.png)

      下表为图中示例的取值，实际部署请替换为现场规划的地址：

      | 参数 | 示例值 | 说明 |
      | --- | --- | --- |
      | Address | `192.168.10.10` | 目标子节点共享网口的静态 IPv4 地址。 |
      | Subnet Mask | `255.255.255.0` | 对应 `/24` 网络前缀。 |
      | Gateway | `192.168.10.1` | 跨网段访问时使用；电脑与子节点位于同一二层网络时可以不配置。 |
      | Gateway Priority | `100` | 多个网关或默认路由并存时使用，取值应符合现场网络规划。 |
      | DNS | `114.114.114.114` | 使用 IP 地址进行 SSH 登录时不是必填项。 |

      保存后返回 **Network** 页面，确认目标网卡的 **IPv4 Address** 已更新。网络配置生效期间，该子节点的网络连接可能会短暂中断。

      #### 接入服务器共享网口 [step]

      1. 使用网线将维护电脑接入交换机。
      2. 确认电脑端口与服务器端口属于同一交换网络和 VLAN。
      3. 使用网线将服务器共享网口接入同一交换机。

      ![共享网口网络连接](../../../servers_img/common/pc_switch_shared_network_topology_steps.png)

      将维护电脑设置为与子节点静态 IP 相同的网段，且地址不能重复。例如子节点为 `192.168.10.10/24` 时，电脑可以设置为 `192.168.10.100/24`。

      在电脑终端中测试网络连通性：

      ```bash
      ping 192.168.10.10
      ```

      能收到子节点回复后，再执行 SSH 登录。如果连不通，请检查共享网口接线、交换机 VLAN、电脑 IP、子节点静态 IP 和防火墙配置。

      #### 在电脑上执行 SSH 登录 [step]

      1. 打开电脑的终端、PowerShell 或其他 SSH 客户端。
      2. 使用子节点操作系统的用户名和静态 IP 建立连接。SSH 默认端口为 `22`。
      3. 首次连接时核对主机指纹，确认无误后输入 `yes`，再输入子节点操作系统密码。

      ```bash
      ssh <SUBBOARD_USER>@<SUBBOARD_STATIC_IP>
      ```

      以本节示例 IP 为例：

      ```bash
      ssh <SUBBOARD_USER>@192.168.10.10
      ```

      SSH 服务使用非默认端口时，通过 `-p` 指定端口：

      ```bash
      ssh -p <SSH_PORT> <SUBBOARD_USER>@192.168.10.10
      ```

      终端出现目标子节点的命令提示符，说明 SSH 登录成功。

      <Callout title="SSH 登录凭据" type="warn">
        SSH 使用的是子节点操作系统账号和密码，不是 aBMC Web 的 `admin/admin`。登录前应确认子节点已启用 SSH 服务、目标账号允许远程登录，并且防火墙放行了对应的 SSH 端口。
      </Callout>
    </CodeBlockTab>
</CodeBlockTabs>