# 电源信息

## 简介

电源信息（Power Info）是aBMC面向阵列式服务器设计的阵列电源监测系统。该系统由电源概览、输出功率趋势和电源模块详情三部分组成。用户可以集中查看电源模块的在位状态、健康状态、额定功率、输入输出电气参数和功率变化趋势，及时识别电源缺失、运行异常或供电能力不足等问题。

## 开发愿景

1. 为用户提供可视化的电源状态和输出功率趋势，方便客户持续了解阵列式服务器的供电情况和功率变化。
2. 通过统一展示电源模块的在位、健康和电气参数，提高电源故障判断效率，降低单个电源模块异常对阵列稳定运行的影响。


# 功能使用

## 查看电源信息

<CodeBlockTabs defaultValue="WEB">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="WEB">WEB</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="API">API</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="WEB">
    ### 打开电源信息页面 [step]

    1. 在左侧主导航中选择 **System**。
    2. 在二级导航栏中选择 **Power**。
    3. 在页面顶部单击 **Power Info**。
    4. 等待 **Summary**、**Power Supply Wattage** 和 **Power Details** 完成加载。

    ![查看电源信息](../../../img/aBMC_img/v3.0/powerMessage_img/01-view-power-info-en-steps-v2.png)

    ### 查看电源概览 [step]

    1. 在 **Summary** 中检查电源模块总数和在位数量。
    2. 检查当前 **Input Power** 和 **Power Work Mode**。
    3. 查看概览右侧的整体健康状态。状态为 **Abnormal** 时，应继续检查 **Power Details** 中的异常电源模块。

    ### 电源概览字段说明 [step]

    | Field | 说明 | 检查要点 |
    | --- | --- | --- |
    | Total | 系统识别到的电源模块总数。 | 应与设备规划安装的电源模块数量一致。 |
    | In Place | 当前处于在位状态的电源模块数量。 | 小于 **Total** 时，检查缺失电源模块的槽位、安装状态和供电连接。 |
    | Input Power | 当前电源模块输入功率的合计值；无法取得输入功率时，页面可能使用系统返回的已消耗功率。 | 应结合设备负载和历史趋势判断，不应只根据一次采样确认异常。 |
    | Power Work Mode | 当前电源工作模式，可能包括 **Load Balancing**、**Sharing** 或 **Active Standby**。 | 设备未返回工作模式时显示 `-`，不表示电源模块一定异常。 |
    | Health Status | 根据在位电源模块状态和系统返回的电源健康状态生成的整体判断。 | 显示 **Abnormal** 时，应逐项检查详情、设备告警和系统日志。 |

    ### 查看输出功率趋势 [step]

    1. 在 **Power Supply Wattage** 中查看各电源模块的输出功率曲线。
    2. 使用图例中的位置名称识别对应电源模块，例如 **bmc-Top** 表示 BMC 顶部槽位的电源模块。
    3. 观察功率是否持续剧烈波动、突然降为 `0` 或长时间缺少数据。
    4. 页面每 `15` 秒自动刷新电源详情和输出功率趋势，无需手动刷新。

    <Callout title="功率趋势说明" type="info">
      趋势图显示的是电源模块输出功率的历史采样值。趋势图末端数值与 **Power Details** 中的当前 **Output Power** 可能因采样时间不同存在小幅差异，应结合连续趋势和当前详情综合判断。
    </Callout>

    ### 查看电源模块详情 [step]

    1. 在 **Power Details** 中根据 **Power Name** 和槽位信息确认目标电源模块。
    2. 检查 **Current Status** 是否为 **Normal**。
    3. 检查额定功率、输入电压、输入电流、输入功率和输出功率是否符合设备规格及当前负载情况。
    4. 发现 **Not In Place** 或 **Abnormal** 状态时，记录槽位、厂商和序列号，并结合设备告警与现场连接进一步排查。

    ### 电源详情字段说明 [step]

    | Field | 说明 | 检查要点 |
    | --- | --- | --- |
    | Power Name | 电源模块名称，例如 **PSU 1**。 | 操作和排查前确认目标电源模块。 |
    | Current Status | 当前状态，包括 **Normal**、**Not In Place** 和 **Abnormal**。 | 非 **Normal** 状态时检查模块安装、供电连接和设备告警。 |
    | Slot Number | 电源模块所在槽位编号。 | 用于定位设备中的物理槽位。 |
    | Slot Position | 电源模块所在位置，例如 **Top**。 | 应与设备实际安装位置一致。 |
    | Manufacturer | 电源模块制造商。 | 更换模块时用于核对兼容性和设备规格。 |
    | Serial Number | 电源模块序列号。 | 用于资产记录、故障追踪和售后定位。 |
    | Rated Power | 电源模块的额定功率，单位为 W。 | 应满足设备供电设计要求；不能只根据当前输入功率判断是否超出额定能力。 |
    | Input Voltage | 电源模块当前输入电压，单位为 V。 | 应处于电源模块和现场供电系统允许的范围内。 |
    | Input Current | 电源模块当前输入电流，单位为 A。 | 应结合输入电压、输入功率和设备负载综合判断。 |
    | Input Power | 电源模块当前输入侧功率，单位为 W。 | 应结合历史趋势观察是否存在异常突变。 |
    | Output Power | 电源模块当前输出侧功率，单位为 W。 | 通常低于输入功率；两者差值与电源转换损耗和测量误差有关。 |

    ### 确认电源状态 [step]

    1. 确认 **Total** 与设备规划安装数量一致，且 **In Place** 等于实际在位数量。
    2. 确认整体健康状态以及各电源模块的 **Current Status** 均为 **Normal**。
    3. 连续观察 **Power Supply Wattage**，确认输出功率能够正常更新且没有无业务原因的持续突变。
    4. 对比额定功率、输入输出参数和当前设备负载；发现异常时，应进一步检查设备告警、系统日志、电源线和现场供电环境。

    <Callout title="状态刷新说明" type="info">
      页面每 `15` 秒重新获取电源模块详情和输出功率趋势。电源模块刚插入、拔出或供电状态刚发生变化时，请等待下一次刷新后再次确认。
    </Callout>
  </CodeBlockTab>

  <CodeBlockTab value="API">
    | 操作 | 方法 | URI |
    | --- | --- | --- |
    | 查看电源概览和电源模块详情 | GET | `/redfish/v1/Chassis/bmc/Power` |
    | 查询电源模块输出功率趋势 | POST | `/redfish/v1/Oem/PrometheusServices/PowerOutputList` |

    <Callout title="提示" type="info">
      关于接口认证、请求参数、返回字段和错误码的详细说明，请查看 Redfish API 文档。查询输出功率趋势时，应根据需要提供节点名称、开始时间、结束时间和采样步长。
    </Callout>
  </CodeBlockTab>
</CodeBlockTabs>


## 常见问题 FAQ

### 1.电源工作模式或电气参数显示为 `-`

设备可能未提供对应字段，或状态读取暂时失败。先确认电源模块状态和 BMC 通信正常，等待页面自动刷新后再次检查；如果其他关键字段同时缺失，应结合设备告警和接口响应排查。

### 2.电源模块显示为 **Not In Place** 或 **Abnormal**

根据 **Slot Number** 和 **Slot Position** 定位模块，检查模块是否安装到位、电源线是否连接以及现场供电是否正常，并查看设备告警和系统日志。重新插拔或更换电源模块前，应遵循设备维护规范。

### 3.输入功率和输出功率不一致

电源模块在转换过程中会产生损耗，因此 **Input Power** 通常高于 **Output Power**。两者还可能因采样时间和测量精度存在小幅差异；如果差值持续异常增大，应结合负载、温度、设备告警和电源规格进一步排查。

### 4.输出功率趋势没有数据

确认电源模块处于在位和正常状态，并等待至少一个自动刷新周期。如果详情中已有输出功率但趋势图仍无数据，应检查功率监控服务、历史数据采集状态以及相关接口是否能够正常返回数据。
