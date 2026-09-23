# 子节点登录

计算子节点（子板）的 **Serial**、**ADB**、**SSH** 三种连接通道，可以分别从下面三个入口发起，按现场条件任选一条可用路径即可：

| 入口 | Serial | ADB | SSH | 什么时候用它 |
| --- | --- | --- | --- | --- |
| Web 控制台 | 支持 | 支持 | 支持 | 图形化操作，只调一台子节点 |
| BMC 命令行 `bmc` | 支持 | 支持 | 支持 | 批量或脚本化操作，不用开浏览器 |
| 维护电脑直连 | — | — | 支持 | 长时间调试，子节点需先配静态 IP |

Web 的 **Debug Mode** 只列出子节点上报支持的通道，个别子节点可能不提供全部通道。本节只讲子节点，登录 BMC 管理控制器请参考[访问 BMC](start_login_bmc.md)。

## Web 登录 [step]

在 aBMC Web 页面中直接打开子节点终端，不需要额外配置网络。

### 打开子节点调试窗口 [step]

1. 在左侧导航栏中选择 **Devices**。
2. 在设备菜单中选择 **General**，在设备列表里找到要访问的子节点，优先选状态为 **Online** 或 **Ready** 的节点。
3. 在目标节点 **Shortcuts** 列中单击第一个终端图标（**Open Shell Command**）。页面宽度不足时，先把设备列表横向滚动到最右侧。

![打开子节点调试窗口](../../../servers_img/common/abmc_open_subnode_debug_en.png)

### 选择连接通道并连接 [step]

在 **Open Debugging** 窗口的 **Debug Mode** 中选择连接通道，单击 **Confirm**，新浏览器窗口中会打开该子节点的终端。

<CodeBlockTabs defaultValue="Serial">
    <CodeBlockTabsList>
        <CodeBlockTabsTrigger value="Serial">Serial</CodeBlockTabsTrigger>
        <CodeBlockTabsTrigger value="ADB">ADB</CodeBlockTabsTrigger>
        <CodeBlockTabsTrigger value="SSH">SSH</CodeBlockTabsTrigger>
    </CodeBlockTabsList>

    <CodeBlockTab value="Serial">
      **Serial**：适用于全部子节点；系统尚未启动或显示无输出时，也能看到底层启动日志。

      1. 确认 **Debug Mode** 为 **Serial**，单击 **Confirm**。
      2. 在新窗口中等待终端建立连接；终端暂时空白时，在黑色区域单击一次，再按 **Enter** 唤醒串口输出。
      3. 出现子节点提示符或登录提示，说明串口已经连通。

      ![选择 Serial 调试模式](../../../servers_img/common/abmc_select_serial_debug_mode_en.png)

      ![子节点 Serial 终端](../../../servers_img/common/abmc_subnode_serial_terminal_en.png)
    </CodeBlockTab>

    <CodeBlockTab value="ADB">
      **ADB**：适用于 Android 子节点。

      1. 确认 **Debug Mode** 为 **ADB**，单击 **Confirm**。
      2. 在新窗口中等待终端与子节点建立 ADB 连接；终端暂时空白时，在黑色区域单击一次，再按 **Enter** 刷新提示符。
      3. 出现类似 `CS_B1_rk3576_jd4_sub:/ #` 的提示符，说明已经进入目标子节点的 ADB Shell。

      ![选择 ADB 调试模式](../../../servers_img/common/abmc_select_adb_debug_mode_en.png)

      ![子节点 ADB 终端](../../../servers_img/common/abmc_subnode_adb_terminal_en.png)
    </CodeBlockTab>

    <CodeBlockTab value="SSH">
      **SSH**：适用于已启用 SSH 服务的子节点，直接登录子节点操作系统。

      1. 在 **Debug Mode** 中选择 **SSH**。
      2. 在 **User** 中选择或输入子节点操作系统的用户名。
      3. 在 **Login Method** 中选择认证方式：账号密码方式填写密码和端口（默认 `22`），密钥方式提供对应私钥。
      4. 单击 **Confirm**，在新浏览器窗口中打开子节点终端；出现命令提示符说明登录成功。
    </CodeBlockTab>
</CodeBlockTabs>

<Callout title="登录凭据" type="warn">
  Serial 和 SSH 登录的是子节点自己的操作系统，请使用该子节点的系统账号密码；`admin/admin` 只能登录 aBMC 页面。ADB 是调试通道，一般直接进入 Shell。
</Callout>

## CLI 命令工具登录 [step]

`bmc` 是 aBMC 自带的 CLI 工具，在 BMC 上执行即可连接各子节点，适合批量或脚本化操作。按连接通道选择命令：

<CodeBlockTabs defaultValue="ADB">
    <CodeBlockTabsList>
        <CodeBlockTabsTrigger value="ADB">ADB</CodeBlockTabsTrigger>
        <CodeBlockTabsTrigger value="Serial">Serial</CodeBlockTabsTrigger>
        <CodeBlockTabsTrigger value="SSH">SSH</CodeBlockTabsTrigger>
    </CodeBlockTabsList>

    <CodeBlockTab value="ADB">
      **ADB**：适合 Android 子节点，`--core` 后填目标核心板名称。

      ```bash
      ./bmc terminal adb --core sub01
      ```
    </CodeBlockTab>

    <CodeBlockTab value="Serial">
      **Serial**：网络不通或系统尚未启动时也能用，`--core` 后填目标核心板名称。

      ```bash
      ./bmc terminal serial --core sub01
      ```
    </CodeBlockTab>

    <CodeBlockTab value="SSH">
      **SSH**：需要同时给出 aBMC 服务和子节点操作系统的登录参数。

      ```bash
      ./bmc terminal ssh --protocol <http|https> --ip <aBMC 服务 ip> --port <aBMC 服务端口> --user <aBMC 服务登录用户> --password <aBMC 服务登录密码> --core <目标核心板> --ssh-user <用户名> --ssh-password <密码> --ssh-port <ssh 端口>
      ```

      <Callout title="提示" type="info">
        子节点与管理网络已经互通时，可以不借助 `bmc` 工具，直接参考下文「维护电脑直连登录」，从电脑执行 `ssh <子节点用户名>@<子节点 IP>`。
      </Callout>
    </CodeBlockTab>
</CodeBlockTabs>

### 命令参数说明

- `./bmc terminal <连接通道> [aBMC 连接参数] --core <核心板名称>`：`<>` 中的参数必填，`[]` 中的参数可选。
- 省略 aBMC 连接参数时使用默认值：`https`、`127.0.0.1`、`443`、`admin`、`admin`；需要指定时可用 `--protocol`、`--ip`、`--port`、`--user`、`--password`。
- 退出方式：依次按 `Ctrl+A`、`Q`、`Enter`。

## 维护电脑直连登录 [step]

子节点与管理网络互通时，可以不经过 Web 或 `bmc` 工具，直接从维护电脑 SSH 登录子节点系统。

这条路径依赖子节点上与服务器**共享网口**连通的那块网卡。共享网口是服务器复用业务网卡、同时承载业务流量与管理流量的网口（详见[访问 BMC](start_login_bmc.md)），它经内部交换机与各子节点相连。因此需要先给该网卡配置一个与维护电脑同网段的静态地址，再把电脑接入服务器所在的交换网络。

### 配置子节点静态 IPv4 地址 [step]

1. 在左侧导航栏中选择 **Devices** → **Network**；也可以直接访问 `https://172.16.100.172:443/#/deviceManage/boardNetManage`，实际使用时请替换为设备的管理地址和端口。
2. 根据 **Device Name**、**Net Card** 和 **MAC Address** 找到子节点上连通共享网口的那块网卡，单击该行的 **Configure**。
3. 在 **IPv4 Configuration** 页签中，将 **IPv4 Mode** 设置为 **Manual**，填写 **Address** 和 **Subnet Mask**；需要跨网段访问时，再填写 **Gateway** 和 **Gateway Priority**。
4. 确认该地址没有被其他设备占用后，单击 **Confirm** 保存，再返回 **Network** 页面确认网卡的 **IPv4 Address** 已更新。配置生效期间，该子节点的网络连接可能会短暂中断。

![打开子节点网络配置](../../../servers_img/common/abmc_configure_subboard_network_en.png)

![配置子节点静态 IPv4](../../../servers_img/common/abmc_set_subboard_static_ipv4_en.png)

<Callout title="网卡选择" type="warn">
  必须选择子节点上连通共享网口的那块网卡。不要修改 `bmc/MGMT` 管理口或子节点内部互联使用的网卡；无法确认时，请对照产品网口说明、网卡名称和 MAC 地址核对。
</Callout>

上图为示例：`Address` 填 `192.168.10.10`，`Subnet Mask` 填 `255.255.255.0`（即 `/24`）。实际部署请替换为现场规划的地址；电脑与子节点在同一二层网络时可以不填 `Gateway`。

### 把维护电脑接入交换机 [step]

服务器到交换机的网线连接请参考[网络接线方式](start_server_network.md)，按带外管理或带内管理任选一种完成；之后把维护电脑接入同一台交换机：

1. 用网线把维护电脑接入交换机。
2. 确认电脑端口与服务器端口属于同一交换网络和 VLAN。

![共享网口网络连接](../../../servers_img/common/pc_switch_shared_network_topology_steps.png)

把维护电脑的 IP 设置为与子节点同网段且不重复（子节点为 `192.168.10.10/24` 时，电脑可设为 `192.168.10.100/24`），然后测试连通性：

```bash
ping 192.168.10.10
```

能收到子节点回复即可继续；连不通时，按顺序检查共享网口接线、交换机 VLAN、电脑 IP、子节点静态 IP 和防火墙配置。

### 在电脑上执行 SSH 登录 [step]

1. 打开电脑的终端、PowerShell 或其他 SSH 客户端，执行 `ssh <子节点用户名>@<子节点静态 IP>`（默认端口 `22`，非默认端口用 `-p` 指定）。
2. 首次连接时核对主机指纹，确认无误后输入 `yes`，再输入子节点操作系统密码。
3. 终端出现目标子节点的命令提示符，说明 SSH 登录成功。

以本节示例 IP 为例：

```bash
ssh <SUBBOARD_USER>@192.168.10.10
ssh -p <SSH_PORT> <SUBBOARD_USER>@192.168.10.10   # SSH 服务使用非默认端口时
```

<Callout title="SSH 登录凭据" type="warn">
  SSH 使用的是子节点操作系统账号和密码，不是 aBMC Web 的 `admin/admin`。登录前应确认子节点已启用 SSH 服务、目标账号允许远程登录，并且防火墙放行了对应的 SSH 端口。
</Callout>

## FAQ [step]
### Q：如何获取用户手册？[step]
完整功能说明参考 [《aBMC Web 使用手册》](/docs/server/bmc-software/aBMC/preface)。

### Q：所有登录方式都无法登录该怎么处理？[step]
请参考 [异常排查](/docs/server/bmc-software/aBMC/op_issues_troubleshooting)。