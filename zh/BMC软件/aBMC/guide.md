# aBMC 登录指南

## 登录方式

aBMC 提供 Web 管理页面。登录前，请确认操作电脑已接入 aBMC 管理网络，并能与 BMC 管理 IP 互通。

在浏览器地址栏输入以下地址访问登录页面，其中 `<aBMC管理IP>` 请替换为现场实际配置的管理 IP：

`https://<aBMC管理IP>/#/login`

例如：`https://172.16.100.176/#/login`。该地址仅为示例，实际管理 IP 以设备当前网络配置为准。可在服务器本地 Linux 系统执行 `ip` 或 `ifconfig` 命令查询管理网口 IP。

1. 如果浏览器提示 HTTPS 证书安全告警，点击 **Advanced（高级）**。

    ![HTTPS 证书安全告警页面](../../../servers_img/common/abmc_chrome_cert_warning_schematic.png)

2. 选择 **Proceed to (site) (unsafe)**，忽略证书告警并进入 aBMC 登录页面。该告警通常由设备使用的自签名证书引起。

## 默认账号密码

| 项目 | 默认值 |
| :--- | :--- |
| 用户名 | `admin` |
| 密码 | `admin` |

3. 在登录页面输入上述用户名和密码，点击 **Sign in** 即可进入 aBMC 管理系统。

    ![aBMC 登录页面账号密码输入位置](../../../servers_img/common/abmc_login_page.png)

> 安全提示：首次登录成功后，请立即修改默认密码，并定期更新，避免使用默认凭据长期运行。
