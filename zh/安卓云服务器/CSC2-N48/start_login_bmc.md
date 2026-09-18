# 访问 BMC
## 登录须知
aBMC 出厂预设默认参数便于初次调试，下表为默认登录、网络、串口配置。出于设备安全，首次登录务必修改默认账号密码，并定期轮换更新。


<table border="1" cellPadding="8" cellSpacing="0" width="100%">
  <thead>
    <tr>
      <th>类别</th>
      <th>名称</th>
      <th>默认值</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td rowSpan="2">aBMC 管理系统数据</td>
      <td>登录用户名</td>
      <td>admin</td>
    </tr>
    <tr>
      <td>登录密码</td>
      <td>admin</td>
    </tr>
    <tr>
      <td rowSpan="2">aBMC 管理网口 IPv4 地址<br/>● MGMT 或 GM 网口</td>
      <td rowSpan="2">管理网口 IP 与子网掩码</td>
      <td>默认 IP 地址： 192.168.1.2</td>
    </tr>
    <tr>
      <td>默认子网掩码：255.255.255.0</td>
    </tr>
    <tr>
      <td rowSpan="2">BMC Console 串口</td>
      <td>波特率</td>
      <td>115200</td>
    </tr>
    <tr>
      <td>登录凭据</td>
      <td>无需账号密码，按 Enter 键即可进入 BMC Linux 系统</td>
    </tr>
    <tr>
      <td rowSpan="2">BMC Linux 用户数据<br/>● 用于 SSH 登录</td>
      <td>登录用户名</td>
      <td>bmc 或 firefly</td>
    </tr>
    <tr>
      <td>登录密码</td>
      <td>与用户名相同（即 bmc/bmc 或 firefly/firefly）</td>
    </tr>
  </tbody>
</table>

## 控制台登录 [step]

控制台登录支持 Web、Console、SSH 三种方式，按现场条件任选其一，日常运维推荐优先使用 Web：

- **Web**：管理网口与操作 PC 网络互通，通过浏览器图形界面完成整机监控、固件升级等操作；
- **Console**：无网络、系统尚未启动或显示无输出，需要查看底层启动日志；
- **SSH**：习惯命令行操作，或需要批量、脚本化运维。

具体步骤见下方标签页。

<CodeBlockTabs defaultValue="Web_login">
    <CodeBlockTabsList>
        <CodeBlockTabsTrigger value="Web_login">Web</CodeBlockTabsTrigger>
        <CodeBlockTabsTrigger value="Console_login">Console</CodeBlockTabsTrigger>
        <CodeBlockTabsTrigger value="Ssh_login">SSH</CodeBlockTabsTrigger>
    </CodeBlockTabsList>

    <CodeBlockTab value="Web_login">
      ### Web 远程控制台登录
      aBMC 提供可视化的 Web 管理界面，可完成服务器整机监控、硬件运维、固件升级等操作。

      #### 环境准备
      ##### 服务器网络接线
      登录前将 aBMC 管理网口接入局域网，保证操作 PC 与 BMC 管理 IP 三层互通。
      ![PC-Switch-Server Basic Network Connection Topology Diagram](../../../servers_img/common/pc_switch_server_basic_network_topology.png)

      支持两类管理网口，按需选用：
      - **共享网口**：复用服务器业务网卡，同时承载业务流量与 BMC 管理流量；
      - **专用 MGMT 网口**：独立硬件网口，仅传输 BMC 管理指令，隔离业务网络。

      ![MGMT Management Port Wiring Diagram](../../../servers_img/common/mgmt_port_cable_connection.png)

      ##### 查询 aBMC 管理 IP
      可在服务器本地 Linux 系统执行 `ip` / `ifconfig` 命令，读取 MGMT 网口 IP 地址。
      ![MGMT Port IP Query Command Output Screenshot](../../../servers_img/common/mgnt_ip_query_terminal_screenshot.png)

      #### Web 客户端环境要求
      浏览器兼容性与分辨率标准如下：
      | 浏览器 | 最低版本 | 分辨率要求 |
      | :--- | :--- | :--- |
      | Google Chrome | 48.0 及以上 | ≥1366*768，推荐 1600*900 及以上 |
      | Mozilla Firefox | 50.0 及以上 | ≥1366*768，推荐 1600*900 及以上 |
      | Internet Explorer | 11 及以上 | ≥1366*768，推荐 1600*900 及以上 |
      | Microsoft Edge | 97 及以上 | ≥1366*768，推荐 1600*900 及以上 |

      #### Web 页面登录步骤
      以 Chrome 浏览器为例：
      1. 浏览器地址栏输入 `https://aBMC管理IP`，访问时会弹出证书安全告警。
          ![aBMC Certificate Warning Operation Schematic Diagram](../../../servers_img/common/abmc_chrome_cert_warning_schematic.png)
      2. 点击页面上的 `Advanced（高级）`；
      3. 选择 `Proceed to (site) (unsafe)` 忽略证书告警，跳转到登录页。
          ![aBMC Login Page Schematic Diagram](../../../servers_img/common/abmc_login_page.png)
      4. 输入上方「登录须知」表格中的默认账号密码（`admin`/`admin`）登录，进入整机总览面板：
          - 设备面板：查看 ARM 计算单元硬件运行状态、执行底层 Shell 命令；
          ![aBMC dashboard View](../../../servers_img/common/abmc_device_list.png)
          - 固件升级页面：批量更新各计算单元固件；
          ![Add Firmware Upgrade Popup Schematic Diagram](../../../servers_img/common/abmc_fw_upgrade_popup.png)
          ![Firmware Upgrade Task Monitoring Page Schematic Diagram](../../../servers_img/common/abmc_fw_upgrade_monitor_page.png)

      > 安全提示：首次登录请立即修改默认账号密码，并定期更新，降低设备入侵风险。
      > 完整功能说明参考配套《aBMC 用户指南》。
    </CodeBlockTab>

    <CodeBlockTab value="Console_login">
      ### Console 串口登录
      串口线的选型与终端连接方法详见[串口使用](start_server_serial.md)。
      1. 使用 RJ45 串口线连接服务器的 Console 口与调试终端；
      2. 终端软件参数设置如下：
          - 波特率：115200
          - 数据位：8
          - 奇偶校验：无
          - 停止位：1
          - 流控：无
      3. 连接建立后按 **Enter** 键唤醒终端，即可登录 BMC Linux 系统，串口登录无需账号密码；
      4. 登录完成后，即可执行底层系统查询命令。
        ![BMC OS Release Query Command Line Schematic Diagram](../../../servers_img/common/cmd_os_release_info.png)
    </CodeBlockTab>

    <CodeBlockTab value="Ssh_login">
      ### SSH 远程登录
      1. 在本地使用系统自带的 `ssh` 工具，或 MobaXterm 等终端软件；
      2. 输入 aBMC 管理 IP 登录，BMC Linux 账号密码为 `bmc`/`bmc` 或 `firefly`/`firefly`（密码与用户名相同）。

      > 默认账号视设备版本而定：请先尝试 `bmc`/`bmc`，若无法登录，再改用 `firefly`/`firefly`。
    </CodeBlockTab>
</CodeBlockTabs>

> 以上方式均无法登录时，请参考[异常排查](op_issues_troubleshooting.md)。