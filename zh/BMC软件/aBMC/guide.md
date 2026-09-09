# 登录指南

本指南用于首次访问 aBMC Web 管理页面。完成管理网络连通性确认后，使用浏览器打开登录页面，并使用 aBMC 管理账号进入系统。

<Callout title="快速开始" type="info">
  Web 管理页面使用现场实际配置的 aBMC 管理 IP。`172.16.100.176` 仅作为示例地址，不代表所有设备的默认 IP。

  登录前请确认：

  - 操作电脑已接入 aBMC 管理网络；
  - 操作电脑与管理 IP 网络互通；
  - 已获取当前设备的 Web 管理账号和密码。
</Callout>

## 登录参数

| 项目 | 参数 |
| :--- | :--- |
| 登录方式 | Web 浏览器 |
| 访问协议 | HTTPS |
| 登录地址 | `https://<aBMC管理IP>/#/login` |
| 默认用户名 | `admin` |
| 默认密码 | `admin` |

<Callout title="安全要求" type="warn">
  默认凭据仅用于首次调试。首次登录成功后，请立即修改密码，并定期轮换管理账号凭据。不要将默认密码用于生产环境，也不要在共享电脑上保存密码。
</Callout>

## Web 页面登录

### 准备管理网络 [step]

1. 将 aBMC 管理网口接入交换机或其他可达的管理网络。
2. 确认操作电脑与 BMC 管理 IP 位于可互通的网络中。
3. 在服务器本地 Linux 系统执行 `ip` 或 `ifconfig`，查询当前管理网口的 IPv4 地址。

![操作电脑、交换机与服务器的基础网络连接拓扑](../../../servers_img/common/pc_switch_server_basic_network_topology.png)

<Callout title="管理 IP 说明" type="info">
  管理 IP 由现场网络规划和设备当前配置决定。请使用查询到的实际地址替换文中的 `<aBMC管理IP>`，不要直接照抄示例地址。
</Callout>

### 打开登录页面 [step]

1. 使用 Chrome、Firefox 或 Edge 浏览器，在地址栏输入以下地址：

   `https://<aBMC管理IP>/#/login`

2. 例如，当前测试环境的登录地址为：

   `https://172.16.100.176/#/login`

   该地址仅用于示例，实际登录时应替换为设备当前的管理 IP。
3. 如果浏览器显示 HTTPS 证书安全告警，请先核对地址栏中的管理 IP，再点击 **Advanced（高级）**。

   ![Chrome HTTPS 证书安全告警](../../../servers_img/common/abmc_chrome_cert_warning_schematic.png)

4. 点击 **Proceed to (site) (unsafe)**，进入 aBMC 登录页面。

<Callout title="关于证书告警" type="info">
  aBMC 默认可能使用设备自签名证书，因此浏览器会提示连接不是私密连接。只有在确认管理 IP 和网络环境可信后，才可以继续访问；如果地址不是预期的 BMC 管理地址，请停止操作并检查网络配置。
</Callout>

### 输入账号并登录 [step]

1. 在 **Account** 中输入默认用户名 `admin`。
2. 在 **Password** 中输入默认密码 `admin`。
3. 根据需要勾选 **Remember Password**。生产环境或共享电脑不建议保存密码。
4. 点击 **Sign in**，进入 aBMC 管理系统。

![aBMC 登录页面及账号密码输入位置](../../../servers_img/common/abmc_login_page.png)

| 页面字段 | 输入内容 |
| :--- | :--- |
| Account | `admin` |
| Password | `admin` |
| 操作 | **Sign in** |

### 验证登录结果 [step]

登录成功后，确认页面可以正常显示系统概览或设备列表，并检查页面右上角的用户状态。后续功能入口通常位于左侧导航栏，可根据需要进入 **Devices**、**System** 等模块。

![aBMC 管理系统首页](../../../servers_img/common/abmc_dashboard_view.png)

<Callout title="首次登录后的操作" type="warn">
  登录成功后立即进入账号或用户管理页面修改默认密码。修改密码后请使用新密码重新登录，确认凭据已经生效。
</Callout>

## 登录问题排查

| 现象 | 处理建议 |
| :--- | :--- |
| 无法打开登录页面 | 检查管理网口接线、操作电脑 IP 配置、交换机 VLAN 和管理 IP 是否正确。先确认操作电脑能够访问目标管理 IP。 |
| 浏览器显示证书告警 | 确认地址栏中的 IP 是目标 BMC 管理 IP；确认网络可信后，按页面提示进入高级选项并继续访问。 |
| 提示账号或密码错误 | 确认使用的是 Web 管理账号 `admin`，并注意密码可能已经被首次登录后的修改操作更新。 |
| 页面加载不完整 | 刷新页面并确认浏览器版本、管理网络和 aBMC 服务状态正常；问题持续时联系设备管理员。 |

<Callout title="仍无法登录" type="warn">
  如果网络已连通但页面仍无法登录，请联系设备管理员核对管理 IP、账号状态和密码是否已修改。不要反复尝试未知凭据，以免触发现场安全策略。
</Callout>
