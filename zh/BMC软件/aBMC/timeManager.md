# 时间管理

## 简介

时间管理（Time Manager）是aBMC面向阵列式服务器设计的阵列时间群控系统。管理员可以检查设备时间、时区、同步模式、NTP 时间源和时钟偏差，并通过 NTP 或手动方式设置单台或多台设备的时间。

## 开发愿景

1. 为 BMC 和阵列子板建立统一的时间基准，减少时间偏差对日志排序、告警关联、任务调度和故障定位的影响。
2. 通过统一的 NTP、时区和批量配置入口，降低逐台配置设备的运维成本和出错概率。


# 功能使用

## 查看设备时间

<CodeBlockTabs defaultValue="WEB">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="WEB">WEB</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="API">API</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="WEB">
    ### 打开时间管理页面 [step]

    1. 在左侧主导航中选择 **Devices**。
    2. 在设备菜单中选择 **Time**。
    3. 等待设备时间列表完成加载，检查页面显示的时间状态字段。
    4. 如果某台设备的时间信息显示为 `-`，说明当前未能获取该设备的时间状态。
    5. 需要修改设备时间时，使用对应设备 **Actions** 列中的设置图标。

    ![查看设备时间状态](../../../aBMC_img/v3.0/timeManager_img/01-view-time-manager-en-steps-v2.png)

    ### 时间状态字段说明 [step]

    | Field | 说明 | 检查要点 |
    | --- | --- | --- |
    | Device Name | 设备在 aBMC 中的名称，例如 BMC 或阵列子板名称。 | 操作前确认目标设备，避免修改错误节点。 |
    | Device Time | 设备当前返回的系统日期和时间。 | 应与预期时间基准一致；显示 `-` 时先检查设备状态。 |
    | Time Zone | 设备当前使用的时区和 GMT 偏移。 | 同一业务集群通常应使用统一时区。 |
    | Sync Mode | 当前时间同步方式，包括 **Automatic (NTP)** 和 **Manual Mode**。 | 长期运行建议使用可用的 NTP 时间源。 |
    | NTP Server | 设备当前使用的 NTP 时间源。 | 应与规划的上游服务器或 BMC 地址一致。 |
    | Clock Offset (ms) | 页面返回的设备时钟偏差。 | 观察偏差是否持续增大或长期异常，不应仅根据一次采样判断。 |

    ### 判断设备时间状态 [step]

    1. 确认 **Device Time** 与当前标准时间基本一致。
    2. 确认 **Time Zone** 符合设备所在区域和业务规范。
    3. 使用 NTP 时，确认 **Sync Mode** 为 **Automatic (NTP)**，并检查 **NTP Server** 是否正确。
    4. 连续观察 **Clock Offset (ms)**；偏差持续扩大时，应检查时间源稳定性和网络质量。

    <Callout title="状态显示说明" type="info">
      时间列表显示的是设备最近一次返回的状态。设备离线、通信异常或状态读取失败时，相关字段可能显示为 `-`；应先恢复设备连接，再刷新页面检查。
    </Callout>
  </CodeBlockTab>
  <CodeBlockTab value="API">
    | 操作 | 方法 | URI |
    | --- | --- | --- |
    | 查看节点时间信息 | GET | `/redfish/v1/Systems/{{nodename}}/Oem/Firefly/TimeService` |

    <Callout title="提示" type="info">
      关于接口认证、请求参数、返回字段和错误码的详细说明，请查看 Redfish API 文档。
    </Callout>
  </CodeBlockTab>
</CodeBlockTabs>


## 设置单台设备时间

<CodeBlockTabs defaultValue="WEB">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="WEB">WEB</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="API">API</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="WEB">
    ### 打开时间设置窗口 [step]

    1. 在目标设备的 **Actions** 列中单击设置图标。
    2. 选择 **Time Settings**，打开时间设置窗口。

    ![打开单设备时间设置](../../../aBMC_img/v3.0/timeManager_img/02-open-time-settings-en-steps-v2.png)

    ### 配置 NTP 自动同步 [step]

    1. 在 **Sync Mode** 中选择 **Automatic (NTP)**。
    2. 在 **Time Zone** 中选择设备所在区域使用的时区。
    3. 填写 **Preferred NTP**；需要备用时间源时，再填写 **Alternative NTP**。
    4. 需要设置 RTC、校时阈值或本地 NTP 服务时，展开 **Advanced**。
    5. 检查配置无误后，单击 **Confirm**。

    ![配置 NTP 自动同步](../../../aBMC_img/v3.0/timeManager_img/03-configure-ntp-en-steps-v2.png)

    ### NTP 参数说明 [step]

    | 参数 | 说明 | 配置建议 |
    | --- | --- | --- |
    | Device Time | 当前设备时间，只用于配置前核对。 | 该字段不可编辑。 |
    | Sync Mode | 选择自动同步或手动设置。 | 持续校时场景选择 **Automatic (NTP)**。 |
    | Time Zone | 设备使用的时区。 | 应与业务、日志和运维规范保持一致。 |
    | Preferred NTP | 首选 NTP 时间源。 | 建议配置设备能够稳定访问的 IP 地址或域名。 |
    | Alternative NTP | 备用 NTP 时间源。 | 建议与首选时间源相互独立，提高时间源可用性。 |

    NTP 地址可以使用设备能够访问的 IP 地址或域名，例如：

    ```text
    192.168.10.20
    ntp.example.com
    ```

    <Callout title="NTP 网络要求" type="info">
      使用域名时，应确保设备能够正确解析该域名。还应确认路由和防火墙允许设备访问 NTP 服务，通常需要允许 UDP `123` 端口通信。首次完成同步可能需要等待一段时间。
    </Callout>

    ### 配置 BMC 与子板两级同步 [step]

    1. 为 BMC 选择 **Automatic (NTP)**，将 **Preferred NTP** 设置为可靠的外部时间源。
    2. 根据现场规划配置 BMC 的 **Local NTP Server** 参数，使其能够向子板提供时间服务。
    3. 返回时间列表，确认 BMC 的 **NTP Server** 和 **Clock Offset (ms)** 状态正常。
    4. 打开子板的 **Time Settings**，选择 **Automatic (NTP)**。
    5. 将子板的 **Preferred NTP** 设置为子板管理网络中能够访问的 BMC 地址。
    6. 保存后确认子板的 **NTP Server** 指向 BMC，并观察时钟偏差是否稳定。

    <Callout title="避免时间同步环路" type="warn">
      不要将 BMC 的上游 NTP 时间源配置为其下联子板，否则可能形成时间同步环路。BMC 和子板之间也应允许 NTP 服务端口通信。
    </Callout>

    ### 配置高级 NTP 参数 [step]

    1. 选中 **Advanced**，展开高级参数。
    2. 根据设备要求设置 **Sync RTC**。
    3. 在 **Step Calibration** 中设置前几次校时的次数和偏差阈值。
    4. 在 **Listen Port** 中设置本地 NTP 服务监听端口。
    5. 在 **Stratum** 中设置本地 NTP 服务器层级。
    6. 检查配置无误后，单击 **Confirm**。

    ![配置高级 NTP 参数](../../../aBMC_img/v3.0/timeManager_img/04-configure-advanced-ntp-en-steps-v2.png)

    | 参数 | 页面范围或默认值 | 说明 |
    | --- | --- | --- |
    | Sync RTC | 开启或关闭 | 控制系统时间与设备 RTC 硬件时钟的同步。 |
    | Step Calibration - Before | 最小值为 `1` | 指定启用阶跃校时判断的前 N 次校时。 |
    | Step Calibration - greater than | 最小值为 `0` 秒 | 当前几次校时的偏差超过该阈值时，允许直接修正时间。 |
    | Listen Port | `1–65535`，页面默认值为 `123` | 本地 NTP 服务使用的监听端口。 |
    | Stratum | `1–16`，页面默认值为 `10` | 本地 NTP 服务器层级；数值越小，表示时间源越接近基准时钟。 |

    <Callout title="高级参数使用建议" type="warn">
      高级参数会影响设备校时方式以及向下游设备提供的 NTP 服务。仅在了解现场时间同步拓扑和设备要求时修改，并优先在少量设备上验证。
    </Callout>

    ### 手动设置日期和时间 [step]

    1. 在 **Sync Mode** 中选择 **Manual Mode**。
    2. 在 **Time Zone** 中选择设备使用的时区。
    3. 设置 **Date** 和 **Time**；需要使用当前管理主机时间时，单击 **Get**。
    4. 检查日期、时间和时区后，单击 **Confirm**。

    ![手动设置设备时间](../../../aBMC_img/v3.0/timeManager_img/05-configure-manual-time-en-steps-v2.png)

    <Callout title="手动模式说明" type="warn">
      手动模式只设置当前时间，无法持续校正设备时钟漂移。切换到手动模式后，设备不再依赖配置的 NTP 时间源；网络条件允许时，建议使用 NTP 自动同步。
    </Callout>

    ### 确认单设备配置结果 [step]

    1. 返回 **Time** 列表，确认目标设备的 **Device Time** 和 **Time Zone** 已更新。
    2. 确认 **Sync Mode** 与配置一致。
    3. 使用 NTP 时，确认 **NTP Server** 显示预期时间源。
    4. 等待一段时间后刷新页面，确认 **Clock Offset (ms)** 没有持续增大。
  </CodeBlockTab>

  <CodeBlockTab value="API">
    | 操作 | 方法 | URI |
    | --- | --- | --- |
    | 查询动作参数 | GET | `/redfish/v1/Systems/{{nodename}}/Oem/Firefly/TimeService/Actions/ConfigureTimeServiceActionInfo` |
    | 设置节点时间服务 | POST | `/redfish/v1/Systems/{{nodename}}/Oem/Firefly/TimeService/Actions/ConfigueTimeService` |


    <Callout title="提示" type="info">
      关于接口认证、请求参数、返回字段和错误码的详细说明，请查看 Redfish API 文档。
    </Callout>
  </CodeBlockTab>
</CodeBlockTabs>


## 批量设置设备时间

<CodeBlockTabs defaultValue="WEB">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="WEB">WEB</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="WEB">
    ### 打开批量时间设置 [step]

    1. 在设备列表中勾选至少两台需要使用相同时间配置的设备。
    2. 单击任意一台已选设备 **Actions** 列中的设置图标。
    3. 选择 **Batch Time Settings**。

    ![打开批量时间设置](../../../aBMC_img/v3.0/timeManager_img/06-open-batch-time-settings-en-steps-v2.png)

    ### 下发批量 NTP 配置 [step]

    1. 在 **Select Device** 中核对目标设备及选中数量。
    2. 在 **Sync Mode** 中选择所有目标设备共同使用的同步方式。
    3. 在 **Time Zone** 中选择统一时区。
    4. 使用 **Automatic (NTP)** 时，填写首选和备用 NTP 时间源。
    5. 需要统一配置高级参数时，展开 **Advanced**。
    6. 核对所有配置后，单击 **Confirm**。

    ![配置批量设备时间](../../../aBMC_img/v3.0/timeManager_img/07-configure-batch-time-en-steps-v2.png)

    使用 **Manual Mode** 批量设置时，窗口会显示 **Date**、**Time** 和 **Host Time**。其设置方式与单设备手动模式相同，提交后会向所有选中设备下发相同的日期、时间和时区。

    <Callout title="批量配置范围" type="warn">
      批量操作会覆盖所有选中设备的时间策略。执行前应核对 **Select Device** 中的设备和数量，不要混选需要不同时间源、时区或高级参数的设备。
    </Callout>

    ### 确认批量配置结果 [step]

    1. 返回 **Time** 列表，逐台确认 **Sync Mode** 和 **Time Zone**。
    2. 使用 NTP 时，逐台检查 **NTP Server** 是否为预期时间源。
    3. 检查是否有设备仍显示 `-` 或保留旧配置。
    4. 对未生效的设备单独打开 **Time Settings**，检查设备连接状态和字段可编辑性。
  </CodeBlockTab>
</CodeBlockTabs>


## 常见问题 FAQ

### 1.设备时间或时区显示为 `-`

设备可能离线、未接入或状态读取失败。先确认设备运行状态以及 aBMC 与设备之间的网络连接，恢复后刷新时间页面。

### 2.批量设置后部分设备未生效

部分设备可能离线、能力不同或暂时无法修改时间配置。单独打开失败设备的 **Time Settings**，检查状态和参数后重新设置。

### 3.子板无法通过 BMC 同步时间

确认 BMC 已从上游时间源完成同步，并检查 BMC 的本地 NTP 监听端口、Stratum、子板使用的 BMC 地址以及两者之间的路由和防火墙策略。

### 4.BMC无法同步域名时间源

检查DNS解析状态，并Ping通域名确认网络；若不通，建议改用IP地址测试
