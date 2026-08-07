# 设备概览

## 简介

设备概览（Device Summary）是面向阵列式服务器设计的查看阵列子节点关键指标的功能。页面按设备和时间范围查询历史监控数据，并以曲线图展示 CPU、内存、温度、网络、磁盘、风扇、NPU 和电源功率等关键指标。

## 开发愿景

1. 为管理员提供统一的多节点性能趋势视图，快速识别资源使用异常和突变。
2. 支持按时间范围、采样步长和刷新周期查询，同时满足实时巡检与历史趋势分析需求。

<Callout title="实现逻辑" type="info">
  aBMC 根据选中的节点、起止时间和 Step 查询 Prometheus 历史数据，将各节点和设备指标组合为可分页、可隐藏的趋势曲线。
</Callout>

# 功能使用

## 查看和分析设备运行趋势

<CodeBlockTabs defaultValue="WEB">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="WEB">WEB</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="API">API</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="WEB">
    ### 设置查询条件 [step]

    1. 在左侧主导航栏中选择 **Devices**。
    2. 在二级导航栏中选择 **Summary**。
    3. 在设备选择器中选择需要查询的 aBMC 或子节点。页面支持同时选择多个节点，默认选择所有已启用节点。
    4. 选择查询时间范围，例如 **Last 30 Min**。
    5. 在页面显示的推荐范围内设置 **Step**。
    6. 选择自动刷新间隔，可选 `15 Seconds`、`30 Seconds`、`1 Minute`、`3 Minutes` 或 `5 Minutes`。
    7. 需要立即获取最新数据时，单击刷新按钮。
    8. **Reset Data** 用于重置历史监控数据，不是图表刷新按钮。

    ![Open and configure Device Summary](../../../img/aBMC_img/v3.0/device_summary_img/01-open-device-summary-en-steps-v1.png)

    <Callout title="Step 设置" type="info">
      Step 表示趋势数据的采样间隔，单位为秒。时间范围改变后，页面会重新计算推荐值及可用上下限。数值越小，数据点越密集；数值越大，数据点越稀疏。
    </Callout>

    <Callout title="谨慎清空监控数据" type="warn">
      Reset Data 会重置 Prometheus 监控数据库，影响全部节点的历史趋势，不是仅清空当前图表或当前选中设备。执行前应确认数据保留要求，并仅由授权管理员操作。
    </Callout>

    ### 查看基础资源和下载流量趋势 [step]

    设置查询条件后，页面自动刷新全部趋势图。首先查看页面顶部的图表：

    1. **CPU Usage**：查看各节点 CPU 使用率趋势。
    2. **Memory Usage**：查看各节点内存使用率趋势。
    3. **Temperature**：查看各节点 CPU 或 SoC 温度趋势。
    4. **Network download speed**：查看各节点、各网卡的下行速率。

    比较多个节点时，先通过图例确认节点名称，再观察同一时间点的曲线差异。

    ### 查看上传、处理器和磁盘趋势 [step]

    向下滚动页面，按照下图依次查看：

    1. 在 **Network upload speed** 中查看网卡上行速率。
    2. 在 **CPU Frequency** 中查看各 CPU 核心的运行频率。
    3. 在 **Disk Read Data Rate** 中查看各存储设备的读取速率。
    4. 在 **Disk Write Data Rate** 中查看各存储设备的写入速率。

    ![View network CPU and disk trends](../../../img/aBMC_img/v3.0/device_summary_img/02-view-io-charts-en-steps-v2.png)

    ### 查看风扇、NPU 和电源功率趋势 [step]

    继续滚动到页面底部，按照下图检查硬件运行状态：

    1. 在 **Fan Speed** 中查看各风扇的实时转速。
    2. 在 **NPU Usage** 中查看各节点 NPU 使用率。
    3. 在 **Power Supply Wattage** 中查看设备电源功率趋势。

    ![View fan NPU and power supply wattage trends](../../../img/aBMC_img/v3.0/device_summary_img/03-view-hardware-charts-en-steps-v2.png)

    <Callout title="无监控数据" type="info">
      当前节点未安装对应硬件、未上报指标或所选时间范围内没有采集记录时，图表会显示 **No Data Available**。
    </Callout>

    ### 分析图表数据 [step]

    1. 将鼠标移入图表，查看对应时间点、节点和设备的指标值。
    2. 单击图例项，显示或隐藏对应节点、网卡、CPU 核心或存储设备的曲线。
    3. 图例项较多时，使用图例两侧的箭头切换分页。
    4. 更改设备、时间范围或 Step 后，等待全部图表完成刷新，再核对时间轴和图例。

    ### 趋势图指标说明 [step]

    | 图表 | 说明 | 常用单位 |
    | --- | --- | --- |
    | CPU Usage | CPU 使用率。 | `%` |
    | Memory Usage | 内存使用率。 | `%` |
    | Temperature | CPU 或 SoC 温度。 | `℃` |
    | Network download speed | 网卡下行速率。 | 页面根据数值自动换算 `bps`、`Kbps` 或 `Mbps` |
    | Network upload speed | 网卡上行速率。 | 页面根据数值自动换算 `bps`、`Kbps` 或 `Mbps` |
    | CPU Frequency | CPU 各核心的工作频率。 | `GHz` |
    | Disk Read Data Rate | 存储设备读取速率。 | 页面根据数值自动换算 `B/s`、`KB/s` 或 `MB/s` |
    | Disk Write Data Rate | 存储设备写入速率。 | 页面根据数值自动换算 `B/s`、`KB/s` 或 `MB/s` |
    | Fan Speed | 风扇实时转速。 | `RPM` |
    | NPU Usage | NPU 使用率。 | `%` |
    | Power Supply Wattage | 设备电源功率。 | `W` |
  </CodeBlockTab>

  <CodeBlockTab value="API">
    | 操作 | 方法 | URI |
    | --- | --- | --- |
    | 查看 CPU 使用率趋势 | POST | `/redfish/v1/Oem/PrometheusServices/CpuUsageList` |
    | 查看内存使用率趋势 | POST | `/redfish/v1/Oem/PrometheusServices/MemoryUsageList` |
    | 查看 CPU 或 SoC 温度趋势 | POST | `/redfish/v1/Oem/PrometheusServices/CpuTempList` |
    | 查看网络下行速率趋势 | POST | `/redfish/v1/Oem/PrometheusServices/DownLoadSpeedList` |
    | 查看网络上行速率趋势 | POST | `/redfish/v1/Oem/PrometheusServices/UploadSpeedList` |
    | 查看 CPU 频率趋势 | POST | `/redfish/v1/Oem/PrometheusServices/CpuFrequencyList` |
    | 查看磁盘读取速率趋势 | POST | `/redfish/v1/Oem/PrometheusServices/DiskReadSpeedList` |
    | 查看磁盘写入速率趋势 | POST | `/redfish/v1/Oem/PrometheusServices/DiskWriteSpeedList` |
    | 查看风扇转速趋势 | POST | `/redfish/v1/Oem/PrometheusServices/FanSpeedList` |
    | 查看 NPU 使用率趋势 | POST | `/redfish/v1/Oem/PrometheusServices/NpuUsageList` |
    | 查看电源功率趋势 | POST | `/redfish/v1/Oem/PrometheusServices/PowerOutputList` |
    | 重置 Prometheus 历史数据 | POST | `/redfish/v1/Oem/PrometheusServices/Reset` |

    <Callout title="提示" type="info">
      趋势查询接口使用相同的请求字段：`NodeName` 为节点名称，`Start` 和 `End` 为 Unix 时间戳（秒），`Step` 为采样间隔（秒）。关于接口认证、请求参数、返回字段和错误码的详细说明，请查看 Redfish API 文档。
    </Callout>

    <Callout title="Reset 接口风险" type="warn">
      `/redfish/v1/Oem/PrometheusServices/Reset` 会重置 Prometheus 历史数据库。该操作不接收节点范围，不应将其用作普通图表刷新接口。
    </Callout>
  </CodeBlockTab>
</CodeBlockTabs>

## 常见问题 FAQ

### 1.图表显示 No Data Available

先确认已选择正确的节点和时间范围，然后单击刷新按钮。如果只有某一类图表无数据，可能是该节点没有相应硬件，或当前版本未上报对应的 Prometheus 指标。

### 2.图表数据点过密或过稀

在页面推荐范围内调整 Step。短时间范围可使用较小的 Step，长时间范围建议使用较大的 Step。

### 3.某些节点或设备曲线没有显示

检查顶部设备选择器和图例状态。如果图例被隐藏，单击对应图例项重新显示曲线；图例较多时，使用两侧箭头切换到相应分页。

### 4.图表长时间不更新

单击手动刷新，并检查自动刷新间隔、节点在线状态和管理网络。节点离线或 Prometheus 采集异常时，历史曲线可能中断或停止更新。

### 5.Fan Speed 持续显示 No Data Available

确认设备是否支持风扇转速采集，并结合 **System > Thermal** 页面的风扇在位状态和 RPM 进行核对。

### 6.Reset Data 后历史趋势消失

Reset Data 会重置 Prometheus 历史数据库，已清空的数据无法通过页面恢复。新数据需要等待各节点继续采集后才会重新出现。
