# 热管理

## 简介

热管理（Thermal Control）是aBMC面向阵列式服务器设计的阵列散热群控系统。该系统由状态监测和温度控制两大模块组成。
1. 监测模块：负责采集阵列 SOC 温度、风扇实时转速等硬件热态信息
2. 温度控制模块：内置静音、均衡（默认）、强劲三种预设散热策略，同时开放自定义温控配置能力，用户可结合机房环境与算力负载灵活选用。

## 开发愿景
1. 为用户提供可视化的温度图表，方便客户根据散热情况调整算法强度
2. 用户可以利用温度控制模块的均衡散热策略，可以有效防止阵列集群单点热失控


# 功能使用

## 设置风扇运行模式

<CodeBlockTabs defaultValue="WEB">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="WEB">WEB</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="CLI">CLI</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="API">API</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="WEB">
    ### 打开风扇设置窗口 [step]

    1. 在左侧导航栏中选择 **System**。
    2. 二级导航栏选择 **Thermal**
    3. 在 **Fan Info** 页面的 **Summary** 中确认风扇在位情况。
    4. 在 **Details** 中检查 **Status** 和 **RPM**。
    5. 单击 **Setting**，打开 **Fan Settings** 窗口。

    ![打开风扇设置窗口](../../../img/aBMC_img/v3.0/thermal_img/01-enter-fan-info-en-steps-v2.png)

    ### 选择风扇运行模式 [step]

    1. 在 **Mode** 中选择 **Silent**、**Balanced**、**Powerful** 或 **Custom**。
    2. 选择 **Custom** 时，在 **Gear Level** 中输入 `1–20` 范围内的整数。
    3. 检查配置无误后，单击 **Confirm**。

    ![选择风扇运行模式](../../../img/aBMC_img/v3.0/thermal_img/02-configure-fan-mode-en-steps-v2.png)

    ### 确认配置结果 [step]

    返回 **Fan Info**，确认 **Speed Adjustment Mode** 已更新，并持续观察 **RPM** 和板卡温度是否符合预期。

    ### 模式选择建议 [step]

    | 模式 | 说明 | 使用建议 |
    | --- | --- | --- |
    | Silent | 优先降低风扇噪声。 | 适用于负载较低且温度稳定的环境，使用后应持续观察温度。 |
    | Balanced | 在散热能力、功耗和噪声之间保持平衡。 | 适合作为一般运行场景的常用模式。 |
    | Powerful | 提高散热能力。 | 适用于高负载或温度较高的场景，噪声和功耗可能增加。 |
    | Custom | 使用指定的 Gear Level 控制风扇。 | 仅在了解设备散热要求时使用，设置后应验证温度和 RPM。 |

    <Callout title="Custom 模式说明" type="info">
      Gear Level 的取值范围为 `1–20`。档位与实际 RPM、散热效果的对应关系由设备决定，档位值不代表转速百分比。修改后应继续观察风扇转速和板卡温度。
    </Callout>
  </CodeBlockTab>
  <CodeBlockTab value="CLI">
    ### 查看风扇信息 [step]

    不指定 `--mode` 时，命令返回当前风扇和温度信息。

    ```bash
    bmc fan --protocol http --ip <BMC_IP> --port <BMC_PORT> --user <USERNAME> --password <PASSWORD>
    ```

    ### 设置预置运行模式 [step]

    根据散热和噪声需求，选择一种预置模式执行。

    **Silent 模式**

    ```bash
    bmc fan --protocol http --ip <BMC_IP> --port <BMC_PORT> --user <USERNAME> --password <PASSWORD> --mode Silent
    ```

    **Balanced 模式**

    ```bash
    bmc fan --protocol http --ip <BMC_IP> --port <BMC_PORT> --user <USERNAME> --password <PASSWORD> --mode Balanced
    ```

    **Powerful 模式**

    ```bash
    bmc fan --protocol http --ip <BMC_IP> --port <BMC_PORT> --user <USERNAME> --password <PASSWORD> --mode Powerful
    ```

    ### 设置自定义档位 [step]

    使用 **Custom** 模式时，必须通过 `--level` 指定 `1–20` 范围内的档位。

    ```bash
    bmc fan --protocol http --ip <BMC_IP> --port <BMC_PORT> --user <USERNAME> --password <PASSWORD> --mode Custom --level 20
    ```

    ### Demo [step]

    以下示例假设 BMC 地址为 172.16.100.173、服务端口为 443、用户名为 admin。请将 admin 替换为实际密码，并按现场配置修改地址、端口和账号。

    ```bash
    # 设置为 Balanced 模式
    bmc fan --protocol http --ip 172.16.100.173 --port 2108 --user admin --password admin --mode Balanced

    #查询设置结果
    bmc fan --protocol http --ip 172.16.100.173 --port 2108 --user admin --password admin
    ```

    ### 参数说明[step]

    | 参数 | 是否必填 | 说明 |
    | --- | --- | --- |
    | `--protocol` | 是 | 请求协议，例如 `http`。 |
    | `--ip` | 是 | BMC 管理地址。 |
    | `--port` | 是 | BMC 服务端口。 |
    | `--user` | 是 | HTTP Basic 认证用户名。 |
    | `--password` | 是 | HTTP Basic 认证密码。 |
    | `--mode` | 设置模式时必填 | 可选值为 `Silent`、`Balanced`、`Powerful` 或 `Custom`。 |
    | `--level` | Custom 模式必填 | 风扇档位，取值范围为 `1–20`。 |
    | `--output-format` | 否 | 指定客户端输出格式。 |

    <Callout title="凭据安全" type="warn">
      命令行参数可能被 Shell 历史记录或进程列表保存。请避免在共享环境中直接使用真实密码，并根据部署环境采用安全的凭据传递方式。
    </Callout>
  </CodeBlockTab>
  <CodeBlockTab value="API">

    | 操作 | 方法 | URI |
    | --- | --- | --- |
    | 查询动作参数 | GET | `/redfish/v1/Chassis/bmc/FanModeActionInfo` |
    | 查看风扇和温度信息 | GET | `/redfish/v1/Chassis/bmc/Thermal` |
    | 设置风扇运行模式 | POST | `/redfish/v1/Chassis/bmc/Actions/FanMode.Setting` |

    <Callout title="提示" type="info">
      关于接口认证、请求参数、返回字段和错误码的详细说明，请查看 Redfish API 文档。
    </Callout>

  </CodeBlockTab>
</CodeBlockTabs>


## 查看热管理监测图表

<CodeBlockTabs defaultValue="WEB">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="WEB">
      WEB
    </CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="WEB">
    ### 打开温度监控页面 [step]

    1. 在左侧导航栏中选择 **System > Thermal**。
    2. 在页面顶部单击 **Ocean Temp**。
    3. 等待设备拓扑、温度数据和趋势图完成加载。

    ### 查看温度拓扑 [step]

    1. 查看各采集点的当前温度和状态标识。
    2. 需要调整视图时，使用拓扑控件进行缩放、适配或复位。

    ### 配置温度趋势参数 [step]

    1. 使用 **Select Time Range** 选择历史数据范围，例如 **Last 30 Min**。
    2. 在输入框显示的推荐范围内设置数据采样 **Step**。
    3. 选择自动刷新间隔，例如 **15 Seconds**。
    4. 需要立即更新数据时，单击 **Refresh**。

    ![View Ocean Temp monitoring](../../../img/aBMC_img/v3.0/thermal_img/03-view-ocean-temp-en-steps-v5.png)

    ### 确认监控结果 [step]

    1. 确认设备拓扑中的温度采集点已正常显示。
    2. 确认趋势图的时间范围和数据密度符合预期。
    3. 手动刷新或等待自动刷新后，确认温度数据和状态能够正常更新。
    4. 如果采集点显示异常状态或温度持续升高，应结合风扇 RPM、当前运行模式和设备告警进一步排查。

    ### 温度监控参数说明[step]

    | 参数或区域 | 说明 | 使用要求 |
    | --- | --- | --- |
    | Device Topology | 按设备物理布局显示板卡、接口、温度值和状态点。 | 应结合设备规格、当前负载和设备告警判断温度是否正常。 |
    | Select Time Range | 选择温度趋势数据的查询时间范围。 | 时间范围越大，建议使用较大的采样步长。 |
    | Step | 控制图表数据的采样间隔。数值越小数据点越密，数值越大数据点越稀。 | 必须处于输入框两侧显示的系统推荐范围内。 |
    | Refresh Interval | 控制页面自动获取最新数据的时间间隔。 | 间隔较短时请求更频繁，应根据监控需求选择。 |
    | Refresh | 立即重新获取温度和状态数据。 | 数据长时间不更新时可以手动刷新。 |
    | Topology Controls | 用于缩放、适配视图或复位拓扑。 | 仅影响页面视图，不改变设备状态。 |

    <Callout title="采样步长说明" type="info">
      Step 用于控制趋势图的数据采样间隔。数值越小，数据点越密集；数值越大，数据点越稀疏。应使用页面根据当前时间范围给出的推荐区间，避免数据点过多影响查询和渲染效率。
    </Callout>
  </CodeBlockTab>
</CodeBlockTabs>

## 常见问题 FAQ

### 1.转速显示异常
风扇状态为 **Normal** 但 RPM 为 `0` 时，手动刷新并观察一段时间，同时结合设备告警判断风扇是否停止或数据读取失败。

### 2.趋势数据过密或过稀
在页面推荐范围内调整 Step。短时间范围使用较小值，长时间范围使用较大值。
