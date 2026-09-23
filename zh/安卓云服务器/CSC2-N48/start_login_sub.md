# 子节点登录

计算子节点（子板）的 **Serial**、**ADB**、**SSH** 三种连接通道，可以分别通过下面三种方式发起：

| 方式 | 支持的通道 | 什么时候用它 |
| --- | --- | --- |
| Web 控制台 | Serial、ADB、SSH | 临时排查，点选即用 |
| BMC 命令行 `bmc` | Serial、ADB、SSH | 批量或脚本化操作，不用开浏览器 |
| 维护电脑直连 | 仅 SSH | 长时间调试，子节点需先配静态 IP |

三种方式都建立在同一套网络连接之上：先完成下面的「网络连接」一节，再按现场条件任选一种。Web 的 **Debug Mode** 只列出子节点上报支持的通道，个别子节点可能不提供全部通道；本节只讲子节点，登录 BMC 管理控制器请参考[访问 BMC](start_login_bmc.md)。

## 先决条件：网络连接 [step]

三种方式都依赖同一套网络连接，先完成下面两步：

1. **服务器接线**：按[网络接线方式](start_server_network.md)把服务器接入交换机，带外管理或带内管理任选一种。
2. **接入维护电脑**：把维护电脑接入同一台交换机，确认电脑端口与服务器端口属于同一交换网络和 VLAN，并保证电脑能访问 aBMC 管理 IP（管理 IP 的默认值与查询方法见[访问 BMC](start_login_bmc.md)）。

![共享网口网络连接](../../../servers_img/common/pc_switch_shared_network_topology_steps.png)

完成这两步后，「方式一：Web 控制台」和「方式二：BMC 命令行」即可使用；「方式三：维护电脑直连」还需要额外给子节点配置静态 IP。

## 方式一：Web 控制台 [step]

在 aBMC Web 页面中直接打开子节点终端。

### 打开子节点调试窗口

1. 在左侧导航栏中选择 **Devices**。
2. 在设备菜单中选择 **General**，在设备列表里找到要访问的子节点，优先选状态为 **Online** 或 **Ready** 的节点。
3. 在目标节点 **Shortcuts** 列中单击第一个终端图标（**Open Shell Command**）。页面宽度不足时，先把设备列表横向滚动到最右侧。

![打开子节点调试窗口](../../../servers_img/common/abmc_open_subnode_debug_en.png)

### 选择连接通道并连接

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
      **ADB**：适用于支持 ADB 的子节点，Android 子节点通常默认支持，部分 Linux 子节点也提供。

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

## 方式二：BMC 命令行 [step]

`bmc` 是 aBMC 自带的 CLI 工具，在 BMC 上执行即可连接各子节点，适合批量或脚本化操作。省略 aBMC 连接参数时使用默认值 `https`、`127.0.0.1`、`443`、`admin`、`admin`（需要指定时用 `--protocol`、`--ip`、`--port`、`--user`、`--password`），退出按 `Ctrl+A`、`Q`、`Enter`。按连接通道选择命令：

<CodeBlockTabs defaultValue="ADB">
    <CodeBlockTabsList>
        <CodeBlockTabsTrigger value="ADB">ADB</CodeBlockTabsTrigger>
        <CodeBlockTabsTrigger value="Serial">Serial</CodeBlockTabsTrigger>
        <CodeBlockTabsTrigger value="SSH">SSH</CodeBlockTabsTrigger>
    </CodeBlockTabsList>

    <CodeBlockTab value="ADB">
      **ADB**：适合支持 ADB 的子节点，`--core` 后填目标核心板名称。

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
        子节点与管理网络已经互通时，可以不借助 `bmc` 工具，直接参考下文「方式三：维护电脑直连」，从电脑执行 `ssh <子节点用户名>@<子节点 IP>`。
      </Callout>
    </CodeBlockTab>
</CodeBlockTabs>

## 方式三：维护电脑直连 [step]

子节点与管理网络互通时，可以不经过 Web 或 `bmc` 工具，直接从维护电脑 SSH 登录子节点系统。

这条路在「网络连接」的基础上多两步：给子节点配一个静态 IPv4 地址，再验证电脑与子节点的连通性。它依赖子节点上与服务器**共享网口**连通的那块网卡——共享网口是服务器复用业务网卡、同时承载业务流量与管理流量的网口（详见[访问 BMC](start_login_bmc.md)），它经内部交换机与各子节点相连。

### 配置子节点静态 IPv4 地址

<Callout title="网卡选择" type="warn">
  必须选择子节点上连通共享网口的那块网卡。不要修改 `bmc/MGMT` 管理口或子节点内部互联使用的网卡；无法确认时，请对照产品网口说明、网卡名称和 MAC 地址核对。
</Callout>

1. 在左侧导航栏中选择 **Devices** → **Network**；也可以直接访问 `https://172.16.100.172:443/#/deviceManage/boardNetManage`，实际使用时请替换为设备的管理地址和端口。
2. 根据 **Device Name**、**Net Card** 和 **MAC Address** 找到子节点上连通共享网口的那块网卡，单击该行的 **Configure**。
3. 在 **IPv4 Configuration** 页签中，将 **IPv4 Mode** 设置为 **Manual**，填写 **Address** 和 **Subnet Mask**；需要跨网段访问时，再填写 **Gateway** 和 **Gateway Priority**。
4. 确认该地址没有被其他设备占用后，单击 **Confirm** 保存，再返回 **Network** 页面确认网卡的 **IPv4 Address** 已更新。配置生效期间，该子节点的网络连接可能会短暂中断。

在 **Network** 页面按 **Device Name → Net Card → MAC Address** 定位目标网卡：

![打开子节点网络配置](../../../servers_img/common/abmc_configure_subboard_network_en.png)

点 **Configure** 打开的 **IPv4 Configuration** 页签：

![配置子节点静态 IPv4](../../../servers_img/common/abmc_set_subboard_static_ipv4_en.png)

图中示例取值为 `Address` = `192.168.10.10`、`Subnet Mask` = `255.255.255.0`（即 `/24`）。实际部署请替换为现场规划的地址；电脑与子节点在同一二层网络时可以不填 `Gateway`。

### 验证电脑与子节点的连通性

把维护电脑的 IP 设置为与子节点同网段且不重复（子节点为 `192.168.10.10/24` 时，电脑可设为 `192.168.10.100/24`），然后测试连通性：

```bash
ping 192.168.10.10
```

能收到子节点回复即可继续；连不通时，按顺序检查共享网口接线、交换机 VLAN、电脑 IP、子节点静态 IP 和防火墙配置。

### 在电脑上执行 SSH 登录

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
### 1.如何获取用户手册？
完整功能说明参考 [《aBMC Web 使用手册》](/docs/server/bmc-software/aBMC/preface)。

### 2.所有登录方式都无法登录该怎么处理？
请参考 [异常排查](/docs/server/bmc-software/aBMC/op_issues_troubleshooting)。