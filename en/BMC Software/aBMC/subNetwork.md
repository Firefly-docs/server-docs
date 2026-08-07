# 子板网络管理

## 简介

子板网络管理（Sub Network Manager）是aBMC面向阵列式服务器提供的集中网络配置功能。该功能由网络信息管理和配置文件操作两大模块组成。
1. 网络信息管理：集中查看阵列子节点网卡的 IPv4 地址、网关、网关优先级、MAC 地址和 DNS，并支持 DHCP 或手动配置。
2. 配置文件操作：提供网络数据导出、模板导出和配置导入入口，其中导入功能仅支持 Linux 设备。

## 开发愿景
1. 为阵列式服务器提供统一、可视化管理阵列子节点的网络入口，减少逐台登录设备配置网络的工作量。
2. 通过明确的配置范围、私有网卡的限制和结果的校验，降低因为错误网络配置而导致子板失联的风险。


# 功能使用

## 查看和配置网卡

<CodeBlockTabs defaultValue="WEB">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="WEB">WEB</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="CLI">CLI</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="API">API</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="WEB">
    ### 进入网络管理页面 [step]

    1. 在左侧主导航栏中选择 **Devices**。
    2. 在二级导航栏中选择 **Network**。
    3. 如需查找指定设备，在 **Enter device name** 中输入设备名称；如需重新获取数据，单击 **Refresh**。
    4. 在网卡列表中根据 **Device Name** 和 **Net Card** 确认目标接口。列表同时显示 BMC 和子板网卡，不显示回环接口 `lo`。

    ![进入 Network 页面：1 选择 Devices，2 选择 Network，3 搜索或刷新，4 查看网络列表](../../../img/aBMC_img/v3.0/subNetwork_img/01-enter-sub-network-en-steps-v3.png)

    ### 查看网卡信息 [step]

    同一设备可能包含多块网卡。配置前应同时核对设备名称和网卡名称，并记录原有地址、网关和 DNS，以便异常时恢复。

    | Field | 说明 |
    | --- | --- |
    | Device Name | 节点名称，例如 `bmc` 或 `sub01`。 |
    | Net Card | 网卡名称，例如 `MGMT`、`eth0` 或 `eth1`。 |
    | IPv4 Address | 当前 IPv4 地址和前缀长度。一个接口存在多个地址时，单击地址可展开查看。 |
    | Gateway | 当前 IPv4 默认网关。 |
    | Gateway Priority | 当前网关的路由优先级。 |
    | MAC Address | 网卡 MAC 地址，用于识别物理接口。 |
    | DNS | 当前 DNS 服务器列表。 |
    | Actions | **Configure** 可用时允许配置；按钮禁用时，将鼠标移至按钮上查看限制原因。 |

    ### 判断网卡是否允许配置 [step]

    - **Configure** 可用：允许修改该接口。
    - **Configure** 禁用：该接口通常是 BMC 与子板通信使用的私有网卡，或受到当前拓扑策略限制，不允许配置。

    <Callout title="私有网卡限制" type="warn">
      不要尝试通过 CLI 或 API 绕过页面限制。后端会检查网卡策略，当 `Oem.Firefly.SettingEnabled` 为 `false` 时返回类似 `card eth1 can not setting` 的错误。
    </Callout>

    ### 使用 DHCP 获取地址 [step]

    1. 在目标接口的 **Actions** 列单击 **Configure**。
    2. 在 **IPv4 Mode** 中选择 **Auto (DHCP)**。
    3. 如需指定 DNS，在 **DNS** 中输入 DNS 服务器。多个地址使用英文逗号分隔。
    4. 单击 **Confirm**。
    5. 返回列表并单击 **Refresh**，确认接口已获得预期地址。

    ### 手动配置 IPv4 [step]

    先在目标接口的 **Actions** 列单击 **Configure**，打开 **Configuration** 窗口，然后按照下图操作：

    1. 在 **IPv4 Mode** 中选择 **Manual**。
    2. 在 **Address** 中输入静态 IPv4 地址。
    3. 在 **Subnet Mask** 中输入对应的子网掩码。填完一行地址后，页面会自动增加空白行，可继续添加其他地址。
    4. 如需配置默认路由，在 **Gateway** 中输入网关地址。
    5. 填写网关时，在 **Gateway Priority** 中输入正整数优先级。
    6. 如需配置 DNS，在 **DNS** 中输入 DNS 服务器；多个地址使用英文逗号分隔。
    7. 检查参数无误后，单击 **Confirm** 保存配置。
    8. 如果不保存本次修改，单击 **Cancel**。

    ![配置单个网卡：1 IPv4 模式，2 地址，3 子网掩码，4 网关，5 网关优先级，6 DNS，7 确定，8 取消](../../../img/aBMC_img/v3.0/subNetwork_img/02-edit-sub-network-en-steps-v3.png)

    ### 修改 DNS [step]

    先打开目标接口的 **Configuration** 窗口，并保持当前 IPv4 模式、地址和网关参数不变，然后按照下图操作：

    1. 在 **DNS** 中修改服务器地址。多个地址使用英文逗号分隔，例如 `192.0.2.53,192.0.2.54`。
    2. 单击 **Confirm** 保存修改。
    3. 如果不保存本次修改，单击 **Cancel**。

    ![配置当前选中网卡的 DNS 字段：1 DNS，2 确定，3 取消](../../../img/aBMC_img/v3.0/subNetwork_img/04-configure-sub-network-dns-en-steps-v3.png)

    ### 配置规则 [step]

    | 配置项 | 规则 |
    | --- | --- |
    | Auto (DHCP) | 由 DHCP 服务分配 IPv4 地址。提交时不应再填写静态地址和网关。 |
    | Address | Manual 模式至少填写一个有效 IPv4 地址；支持为同一接口配置多个地址。 |
    | Subnet Mask | 每个静态地址都必须填写有效的子网掩码。 |
    | Gateway | 可选；填写网关时必须同时填写 Gateway Priority。 |
    | Gateway Priority | 必须为正整数。同一网关和优先级组合不能与其他网卡或 VLAN 重复。 |
    | DNS | 可选；必须为有效 IP 地址，多个地址使用英文逗号分隔。 |

    <Callout title="当前页面能力" type="info">
      当前版本仅开放 **IPv4 Configuration**。**IPv6 Configuration** 和 **VLAN Configuration** 标签处于禁用状态。
    </Callout>

    ### 验证配置结果 [step]

    1. 返回 **Network** 列表并单击 **Refresh**。
    2. 找到刚才配置的设备和网卡。
    3. 确认 **IPv4 Address**、**Gateway**、**Gateway Priority** 和 **DNS** 已更新。
    4. 从对应网段验证地址连通性、默认路由和域名解析。

    <Callout title="网络变更风险" type="warn">
      修改正在使用的地址、子网掩码或默认网关可能使设备立即失联。操作前应记录原配置，并准备串口或其他独立恢复通道。截图中的地址仅用于说明页面位置。
    </Callout>
  </CodeBlockTab>

  <CodeBlockTab value="CLI">
    ### 查看网卡信息 [step]

    不指定配置参数时，`bmc ethernet` 返回目标节点的网卡信息。

    **查看节点的全部网卡**

    不指定 `--interface` 时，命令返回目标节点的全部接口。

    ```bash
    bmc ethernet --protocol http --ip <BMC_IP> --port <BMC_PORT> --user <USERNAME> --password <PASSWORD> --core <NODE_NAME>
    ```

    **查看指定网卡**

    通过 `--interface` 指定网卡名称。配置网卡前，应确认返回结果中的 `Oem.Firefly.SettingEnabled` 为 `true`。

    ```bash
    bmc ethernet --protocol http --ip <BMC_IP> --port <BMC_PORT> --user <USERNAME> --password <PASSWORD> --core <NODE_NAME> --interface <INTERFACE>
    ```

    <Callout title="WEB 与 CLI 的显示差异" type="info">
      WEB 页面会隐藏回环接口 `lo`；CLI 查询全部接口时会返回 `lo`。不要配置回环接口。
    </Callout>

    ### 使用 DHCP [step]

    通过 `--core` 和 `--interface` 指定目标网卡，并将 `--interface-dhcp4` 设置为 `true`。

    ```bash
    bmc ethernet --protocol http --ip <BMC_IP> --port <BMC_PORT> --user <USERNAME> --password <PASSWORD> --core <NODE_NAME> --interface <INTERFACE> --interface-dhcp4=true
    ```

    ### 配置静态 IPv4 [step]

    将 `--interface-dhcp4` 设置为 `false`，并指定 IPv4 地址、前缀长度、网关、网关优先级和 DNS。以下地址属于文档示例网段，执行前必须替换为现场规划值。

    ```bash
    bmc ethernet --protocol http --ip <BMC_IP> --port <BMC_PORT> --user <USERNAME> --password <PASSWORD> --core <NODE_NAME> --interface <INTERFACE> --interface-dhcp4=false --interface-ip 192.0.2.10 --interface-ip-cidr 24 --interface-gateway 192.0.2.1 --interface-gateway-metric 100 --interface-dns 192.0.2.53
    ```

    CLI 每次只能提交一个静态地址和一个 DNS 服务器。如需为同一接口配置多个地址或多个 DNS，请使用 WEB 或 API。

    ### Demo [step]

    以下示例假设 aBMC 地址为 `172.16.100.173`、服务端口为 `443`、用户名为 `admin`，目标网卡为 `sub01/eth0`。请按实际环境修改地址、端口、账号和目标网卡。

    ```bash
    # 查看指定网卡
    bmc ethernet --protocol http --ip 172.16.100.173 --port 443 --user admin --password admin --core sub01 --interface eth0

    # 将指定网卡设置为 DHCP
    bmc ethernet --protocol http --ip 172.16.100.173 --port 443 --user admin --password admin --core sub01 --interface eth0 --interface-dhcp4=true

    # 查询设置结果
    bmc ethernet --protocol http --ip 172.16.100.173 --port 443 --user admin --password admin --core sub01 --interface eth0
    ```

    ### 参数说明 [step]

    | 参数 | 是否必填 | 说明 |
    | --- | --- | --- |
    | `--protocol` | 是 | 请求协议，例如 `http`。 |
    | `--ip` | 是 | aBMC 管理地址。 |
    | `--port` | 是 | Redfish 服务端口。 |
    | `--user` | 是 | HTTP Basic 认证用户名。 |
    | `--password` | 是 | HTTP Basic 认证密码。 |
    | `--core` | 是 | 目标节点名称，例如 `sub01`。 |
    | `--interface` | 查看单个接口或配置时必填 | 目标网卡名称，例如 `eth0`。不指定时查询全部接口。 |
    | `--interface-dhcp4` | 配置时必填 | `true` 表示 DHCP，`false` 表示静态配置。 |
    | `--interface-ip` | 静态配置时使用 | IPv4 地址。 |
    | `--interface-ip-cidr` | 否 | IPv4 前缀长度；未指定时 CLI 默认使用 `24`。 |
    | `--interface-gateway` | 否 | IPv4 默认网关。 |
    | `--interface-gateway-metric` | 否 | 网关路由优先级，必须为整数。 |
    | `--interface-dns` | 否 | DNS 服务器。当前 CLI 每次接收一个 DNS 地址。 |
    | `--output-format` | 否 | 指定客户端输出格式。 |

    ### 命令结果说明 [step]

    | 场景 | 结果 |
    | --- | --- |
    | 查询成功 | 输出接口 JSON；查询全部接口时输出 JSON 数组。 |
    | 配置成功 | 输出 `Success`；使用 JSON 输出格式时返回 Redfish 成功消息。 |
    | 参数错误 | 输出缺失参数或格式错误，例如 `interface-dhcp4 is required`。 |
    | 请求失败 | 输出 Redfish 返回的错误信息并以非零状态码退出。 |

    <Callout title="凭据和网络安全" type="warn">
      命令行中的密码可能被 Shell 历史记录或进程列表保存。执行 Demo 中的 DHCP 设置命令前，应确认目标网卡允许配置且现场存在可用的 DHCP 服务。配置当前通信接口可能立即中断连接，应在具备独立恢复通道时执行。
    </Callout>
  </CodeBlockTab>

  <CodeBlockTab value="API">
    | 操作 | 方法 | URI |
    | --- | --- | --- |
    | 查询动作参数 | GET | `/redfish/v1/Systems/{ComputerSystemId}/EthernetInterfaces/{EthernetInterfaceId}/Actions/Oem/Firefly/ConfigureActionInfo` |
    | 查看网卡集合 | GET | `/redfish/v1/Systems/{ComputerSystemId}/EthernetInterfaces` |
    | 查看指定网卡 | GET | `/redfish/v1/Systems/{ComputerSystemId}/EthernetInterfaces/{EthernetInterfaceId}` |
    | 配置指定网卡 | POST | `/redfish/v1/Systems/{ComputerSystemId}/EthernetInterfaces/{EthernetInterfaceId}/Actions/Oem/Firefly/EthernetInterface.Configure` |

    <Callout title="提示" type="info">
      关于接口认证、请求参数、返回字段和错误码的详细说明，请查看 Redfish API 文档。
    </Callout>
  </CodeBlockTab>
</CodeBlockTabs>


## 导入和导出网络配置

<CodeBlockTabs defaultValue="WEB">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="WEB">WEB</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="WEB">
    ### 打开批量操作入口 [step]

    在 **Network** 页面右上角可以使用以下操作：

    1. **Export Data**：导出当前查询范围内的网络信息。
    2. **Export Template**：导出 Linux 设备的网络配置模板。
    3. **Import**：导入填写完成的 `.xls` 或 `.xlsx` 文件。
    4. 将鼠标指针移至 **Import**，可查看导入限制：当前仅支持 Linux 设备，不支持 Android 子板。

    ![文件操作与导入限制：1 Export Data，2 Export Template，3 Import，4 Android 子板不支持导入且仅支持 Linux 设备](../../../img/aBMC_img/v3.0/subNetwork_img/03-batch-import-sub-network-en-steps-v3.png)

    ### 导出当前网络数据 [step]

    1. 如需限制导出设备，先在 **Enter device name** 中输入设备名称。
    2. 单击 **Export Data**。
    3. 打开导出的 Excel 文件，确认其中包含设备名称、网卡、IPv4 地址、网关、网关优先级、MAC 地址和 DNS。

    **Export Data** 导出当前搜索条件匹配的全部数据，不受页面分页限制。

    ### 导出并填写配置模板 [step]

    1. 如需限制模板中的设备，先使用设备名称搜索。
    2. 单击 **Export Template**。
    3. 保留第一行表头和原有列顺序，在模板第一张工作表中填写配置。

    | 模板列 | 是否必填 | 填写说明 |
    | --- | --- | --- |
    | Device Name | 是 | 目标节点名称，例如 `sub01`。必须与页面中的设备名称一致。 |
    | Net Card | 是 | 目标网卡名称，例如 `eth0`；`lo` 不会被导入。 |
    | IPv4 Address | Manual 模式必填 | 使用 `IPv4/前缀长度` 格式，例如 `192.0.2.10/24`；多个地址使用英文逗号分隔。 |
    | Gateway | 否 | IPv4 默认网关。 |
    | Dhcp4 | 建议填写 | 推荐填写 `yes` 或 `no`。`yes`、`true` 或 `1` 表示 DHCP；留空或填写其他值时按 Manual 处理。 |
    | Gateway Priority | 否 | 网关路由优先级，填写数字。 |
    | DNS | 否 | DNS 服务器；多个地址使用英文逗号分隔。 |

    <Callout title="模板行数" type="info">
      文件包含表头时最多允许 `100` 行，即最多 `99` 条配置数据。页面只读取第一张工作表。
    </Callout>

    ### 导入网络配置 [step]

    1. 确认模板中的设备和网卡存在，并删除不需要修改的行。
    2. 单击 **Import**，选择填写完成的 Excel 文件。
    3. 等待每个 `设备/网卡` 的成功或失败提示。页面最多同时处理 `5` 个配置请求。
    4. 导入完成后，页面会重新获取网络信息。

    ### 导入处理规则 [step]

    | 规则 | 处理方式 |
    | --- | --- |
    | 设备名称或网卡名称为空 | 文件校验失败，不开始导入。 |
    | 网卡为 `lo` | 文件校验失败。 |
    | 设备或网卡在当前列表中不存在 | 跳过该行。 |
    | 配置内容为空 | 跳过该行。 |
    | 配置与当前值一致 | 跳过该行。 |
    | Linux 设备 | 支持导入。 |
    | Android 子板 | 当前版本不支持导入。 |

    ### 验证导入结果 [step]

    1. 导入完成后单击 **Refresh**。
    2. 核对受影响接口的 IPv4 地址、网关、网关优先级和 DNS。
    3. 验证目标设备的网络连通性和域名解析。

    <Callout title="批量导入风险" type="warn">
      批量导入会直接修改多台设备的网络配置。导入前应使用 **Export Data** 备份当前信息，并确保所有目标设备都具备独立恢复通道。
    </Callout>
  </CodeBlockTab>
</CodeBlockTabs>

## 常见问题 FAQ

### 1.找不到目标设备
清除或修正 **Enter device name** 中的搜索条件，单击 **Refresh** 后重新查找。

### 2.Configure 不可用
该接口可能是用于 BMC 与子板通信的独立私有网卡。查看页面提示；如确认为私有网卡，则不能由用户修改。

### 3.IPv6 Configuration 或 VLAN Configuration 无法选择
当前界面尚未开放这两个配置标签。请使用当前可用的 **IPv4 Configuration**，不要将禁用标签视为已支持功能。

### 4.如何填写多个 DNS 地址
按页面提示 **Use commas to separate IP addresses**，使用英文逗号分隔多个地址，不要使用中文逗号。

### 5.Import 无法导入 Android 子板
这是当前版本的功能限制。Import 只支持 Linux 设备，Android 子板不支持导入网络配置。
