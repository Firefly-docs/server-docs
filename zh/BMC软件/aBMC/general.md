# 常规配置

## 简介

常规配置（General）是aBMC面向阵列式服务器设计的阵列设备集中管理系统。管理员可以统一查看阵列子板的在线状态、系统信息、运行时间、温度、网络地址、硬件配置和资源利用率，并对单台或多台设备执行启动策略及电源管理操作。

## 开发愿景

1. 为用户提供集中、可视化的阵列设备状态和资源信息，帮助管理员快速识别离线设备、资源异常和配置差异。
2. 通过统一的筛选、启动策略和批量电源管理入口，降低逐台操作阵列子板的运维成本和出错概率。


# 功能使用

## 查看设备概览

<CodeBlockTabs defaultValue="WEB">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="WEB">WEB</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="WEB">
    ### 打开常规配置页面 [step]

    1. 在左侧主导航中选择 **Devices**。
    2. 在设备菜单中选择 **General**。
    3. 在页面顶部查看设备总数、可用设备数和不可用设备数。
    4. 需要查找设备时，输入设备名称或系统名称，或者在 **Select Device Status** 中选择状态。
    5. 单击 **Reset** 可以清除搜索和状态筛选条件。
    6. 在设备列表中查看设备状态、启动策略、系统信息和资源利用率；需要管理设备时，使用 **Actions** 列中的设置图标或 **Shortcuts** 中的快捷入口。

    ![查看常规配置页面](../../../aBMC_img/v3.0/general_img/01-view-general-settings-en-steps-v1.png)

    ### 设备统计说明 [step]

    | 统计项 | 说明 | 检查要点 |
    | --- | --- | --- |
    | Total Number of Computer Devices | 当前由 aBMC 管理的阵列子板总数。 | 应与设备规划和实际接入数量一致。 |
    | Available Computer Devices | 当前处于 **Online** 或 **Ready** 状态、能够正常使用的设备数量。 | 数量减少时，检查设备状态、网络连接和微服务加载情况。 |
    | Unavailable Computer Devices | 当前未处于可用状态的设备数量。 | 结合列表中的 **Status** 定位对应设备和所处阶段。 |

    ### 设备列表字段说明 [step]

    | Field | 说明 | 检查要点 |
    | --- | --- | --- |
    | Actions | 打开单设备或批量设备的电源管理和启动策略菜单。 | 操作前确认目标设备；勾选至少两台设备后进入批量模式。 |
    | Device | 设备在 aBMC 中的节点名称。 | 应与资产记录和实际阵列位置对应。 |
    | Boot Policy | aBMC 服务初始化或电源按键置为 ON 时使用的子板启动策略；配置了延迟时同时显示 `Delay: <seconds>`。 | 检查策略是否符合业务恢复顺序和供电规划。 |
    | Status | 设备当前状态，例如 **Offline**、**Loader**、**Online**、**Linking** 或 **Ready**。 | 根据状态判断设备是否可操作以及微服务是否完成加载。 |
    | System Name | 设备当前运行的操作系统名称和版本。 | 显示 `-` 时先检查设备状态和状态采集链路。 |
    | Uptime | 设备自最近一次启动后的持续运行时间。 | 异常缩短可能表示设备近期发生过重启。 |
    | Temp | 设备当前温度。 | 应结合设备负载、热管理页面和告警综合判断。 |
    | Network Address | 设备返回的管理网络地址和前缀长度。 | 确认地址与网络规划一致，避免地址冲突或网段错误。 |
    | Device Config | 设备平台、内存和存储容量等硬件配置摘要。 | 用于核对节点规格以及识别阵列中的配置差异。 |
    | PM | 预留的电源模式字段。当前版本固定显示 `-`，尚未在该列表中展示电源模式。 | 不要将 `-` 判断为设备故障；当前页面没有提供该字段的配置入口。 |
    | Memory | 当前内存利用率。 | 持续偏高时应结合进程和业务负载排查。 |
    | CPU | 当前 CPU 利用率。 | 持续偏高时应检查业务负载、系统任务和异常进程。 |
    | Disk | 当前磁盘利用率。 | 接近容量上限时应及时清理或扩容，避免服务异常。 |
    | Shortcuts | 从左到右依次为 **Open Shell Command**、**Self Check**、**System Check**、**File Transfer** 和 **Info**。 | 当前已实现 Shell、System Check 和 Info；Self Check 与 File Transfer 固定禁用。 |

    ### 设备状态说明 [step]

    | Status | 说明 | 建议 |
    | --- | --- | --- |
    | Offline | 设备不在线。 | 检查设备供电、物理连接、网络和节点服务。 |
    | Loader | 设备进入升级状态。 | 等待升级完成，不要在升级过程中执行电源操作。 |
    | Online | 设备已经启动。 | 设备可用，但微服务可能仍在继续加载。 |
    | Linking | 设备微服务正在加载。 | 等待状态变为 **Ready**；长时间未完成时检查相关服务和日志。 |
    | Ready | 设备微服务已经加载。 | 表示设备已经完成主要服务加载，可以正常使用。 |
    | Unavail | 页面无法将设备状态识别为已知状态。 | 检查设备返回值、通信状态和 aBMC 日志。 |

    <Callout title="状态数据说明" type="info">
      页面中的统计值、状态和资源指标会随设备上报数据更新。设备离线、通信异常或指标暂未采集时，相关字段可能显示为 `-`；应先恢复设备连接，再刷新或等待页面更新。
    </Callout>
  </CodeBlockTab>
</CodeBlockTabs>


## 使用设备快捷方式

<CodeBlockTabs defaultValue="WEB">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="WEB">WEB</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="API">API</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="WEB">
    ### 识别快捷方式 [step]

    **Shortcuts** 列中的图标按以下顺序显示。将鼠标指针停留在图标上，可以查看对应的英文提示。

    | 顺序 | 页面提示 | 当前状态 | 作用 | 可用条件 |
    | --- | --- | --- | --- | --- |
    | 1 | Open Shell Command | 已实现 | 查询设备支持的调试连接方式，填写连接参数后在新窗口打开设备终端。 | 目标设备能够返回调试方式；使用 SSH 时还必须提供有效账号和认证信息。 |
    | 2 | Self Check | 未实现 | 预留的设备自检入口。 | 图标固定为禁用状态，当前版本不能单击。 |
    | 3 | System Check | 已实现 | 在当前页面弹出系统检测窗口，查看设备历史资源和 I/O 趋势。 | 设备状态必须为 **Online** 或 **Ready**。 |
    | 4 | File Transfer | 未实现 | 预留的文件传输入口。 | 图标固定为禁用状态，当前版本不能单击。 |
    | 5 | Info | 已实现 | 进入设备详情页，查看设备基础信息、资源状态、系统检测和网络设置。 | 设备状态必须为 **Online** 或 **Ready**；网络设置还需要相应权限。 |

    ![识别设备快捷方式](../../../aBMC_img/v3.0/general_img/04-view-shortcuts-en-steps-v1.png)

    <Callout title="禁用图标说明" type="info">
      **Self Check** 和 **File Transfer** 虽然显示在页面中，但前端当前没有绑定操作事件，并且固定使用禁用样式。它们属于预留功能，不表示设备自检失败或文件传输服务异常。
    </Callout>

    ### 打开设备 Shell [step]

    1. 在目标设备的 **Shortcuts** 中单击第一个终端图标。
    2. 等待页面读取设备的 `SerialConsole.ConnectTypesSupported`，并在 **Debug Mode** 中选择设备实际支持的调试方式。
    3. 选择 **SSH** 时，在 **User** 中选择或输入用户名。
    4. 在 **Login Method** 中选择账号密码或密钥认证。
    5. 使用账号密码认证时，填写端口和密码；端口范围为 `1–65535`，默认使用 `22`。
    6. 使用密钥认证时，提供对应私钥。
    7. 单击 **Confirm**。页面在新浏览器窗口中打开终端，并连接当前设备。

    ![打开设备 Shell](../../../aBMC_img/v3.0/general_img/05-open-shell-en-steps-v1.png)

    图示设备只返回 **ADB**，因此弹窗仅显示调试方式和确认按钮；只有设备返回 **SSH** 时，页面才显示用户、登录方式、端口、密码或私钥等连接参数。

    页面还允许创建或删除设备的 SSH 用户配置。创建用户时需要填写用户名、密码、端口以及密钥相关参数；该操作会修改目标设备保存的 SSH 用户信息。

    <Callout title="Shell 凭据安全" type="warn">
      打开终端会把本次连接参数编码后传递给新窗口，其中可能包含用户名、密码或私钥。仅在可信管理终端和受控网络中使用，不要在共享浏览器中保存生产凭据。使用完毕后应关闭终端窗口并清理不再需要的 SSH 用户。
    </Callout>

    ### 查看 System Check [step]

    1. 确认目标设备状态为 **Online** 或 **Ready**。
    2. 单击 **Shortcuts** 中的第三个趋势图图标。
    3. 在弹出的 **System Check - `<device>`** 窗口中选择开始时间、结束时间和采样步长。
    4. 查看以下趋势图：
       - CPU 使用率
       - 内存使用率
       - 温度
       - 网络上传速率
       - 网络下载速率
       - CPU 频率
       - 磁盘读取速率
       - 磁盘写入速率
    5. 修改时间或采样参数后刷新数据；图表显示空数据时，应检查时间范围、监控服务和设备指标采集状态。

    ![查看 System Check](../../../aBMC_img/v3.0/general_img/06-view-system-check-en-steps-v1.png)

    System Check 只读取监控数据，不会修改设备配置或中断业务。

    ### 打开设备 Info [step]

    1. 确认目标设备状态为 **Online** 或 **Ready**。
    2. 单击 **Shortcuts** 中最右侧的设备信息图标。
    3. 页面进入目标设备详情，顶部显示设备名称、硬件配置、网络速率和设备状态。
    4. 在详情区域查看制造商、SOC、操作系统、温度、内存和存储容量、运行时间、网络地址以及资源利用率。
    5. 使用 **System Check** 页签查看完整趋势图。
    6. 具有网络配置权限时，可以使用 **Net Config** 页签查看或配置设备网络。
    7. 详情页还提供 **Debugger** 和 **Node Operation**，其 Shell 和电源操作方式与 General 页面一致。

    ![打开设备 Info](../../../aBMC_img/v3.0/general_img/07-open-device-info-en-steps-v1.png)

    <Callout title="快捷方式可用状态" type="info">
      **System Check** 和 **Info** 只允许状态为 **Online** 或 **Ready** 的设备使用。设备为 **Offline**、**Loader**、**Linking** 或未知状态时，这两个图标显示为不可用。Shell 图标没有使用相同的状态禁用判断，但设备无法通信时仍可能无法取得调试方式或建立连接。
    </Callout>
  </CodeBlockTab>

  <CodeBlockTab value="API">
    | 功能 | 方法 | URI |
    | --- | --- | --- |
    | 查询设备支持的 Shell 连接方式 | GET | `/redfish/v1/Managers/{{nodename}}` |
    | 查看设备 SSH 用户 | GET | `/redfish/v1/Systems/{{nodename}}/Oem/Firefly/SSHUsers` |
    | 添加设备 SSH 用户 | POST | `/redfish/v1/Systems/{{nodename}}/Oem/Firefly/SSHUsers/Actions/AddSSHUser` |
    | 删除设备 SSH 用户 | DELETE | `/redfish/v1/Systems/{{nodename}}/Oem/Firefly/SSHUsers/Actions/DeleteSSHUser` |
    | 查询 CPU 使用率趋势 | POST | `/redfish/v1/Oem/PrometheusServices/CpuUsageList` |
    | 查询内存使用率趋势 | POST | `/redfish/v1/Oem/PrometheusServices/MemoryUsageList` |
    | 查询 CPU 温度趋势 | POST | `/redfish/v1/Oem/PrometheusServices/CpuTempList` |
    | 查询网络上传速率趋势 | POST | `/redfish/v1/Oem/PrometheusServices/UploadSpeedList` |
    | 查询网络下载速率趋势 | POST | `/redfish/v1/Oem/PrometheusServices/DownLoadSpeedList` |
    | 查询 CPU 频率趋势 | POST | `/redfish/v1/Oem/PrometheusServices/CpuFrequencyList` |
    | 查询磁盘读取速率趋势 | POST | `/redfish/v1/Oem/PrometheusServices/DiskReadSpeedList` |
    | 查询磁盘写入速率趋势 | POST | `/redfish/v1/Oem/PrometheusServices/DiskWriteSpeedList` |

    <Callout title="提示" type="info">
      监控趋势接口需要提供节点名称、开始时间、结束时间和采样步长。关于 Shell 连接、SSH 用户字段、监控请求参数和错误码的详细说明，请查看 Redfish API 文档。
    </Callout>
  </CodeBlockTab>
</CodeBlockTabs>


## 设置单台设备启动策略

<CodeBlockTabs defaultValue="WEB">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="WEB">WEB</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="API">API</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="WEB">
    ### 打开启动策略窗口 [step]

    1. 在目标设备的 **Actions** 列中单击设置图标。
    2. 在菜单中找到设备电源相关配置。
    3. 选择 **Boot Policy Setting**，打开启动策略窗口。

    ![打开启动策略设置](../../../aBMC_img/v3.0/general_img/02-open-boot-policy-setting-en-steps-v1.png)

    ### 配置启动策略 [step]

    1. 在 **Boot Policy** 中选择 aBMC 服务初始化或电源按键置为 ON 时使用的子板启动策略。
    2. 在 **Power-on Delay** 中设置上电动作执行前的等待秒数。该值为不小于 `0` 的整数，设置为 `0` 表示不增加延迟。
    3. 检查目标设备和配置内容后，单击 **Confirm**。
    4. 不需要保存修改时，单击 **Cancel**。

    ![配置设备启动策略](../../../aBMC_img/v3.0/general_img/03-configure-boot-policy-en-steps-v1.png)

    ### 启动策略参数说明 [step]

    | 参数 | 可选值或范围 | 说明 |
    | --- | --- | --- |
    | Boot Policy | **AlwaysOn** | aBMC 服务初始化或电源按键置为 ON 时，强制开启该子板电源域。 |
    | Boot Policy | **AlwaysOff** | aBMC 服务初始化或电源按键置为 ON 时，强制保持该子板电源域关闭。 |
    | Boot Policy | **LastState** | 恢复持久化记录的上一次开关机状态；没有有效记录时按关闭状态处理。 |
    | Power-on Delay | 不小于 `0` 的整数，单位为秒 | 电源管理器执行上电回调前等待的时间，可用于错峰启动阵列设备。 |

    <Callout title="可配置项说明" type="info">
      **Boot Policy** 的可选值和字段是否允许修改由目标设备的动作参数接口决定。如果某个参数在页面中不可编辑，说明当前设备不允许通过该动作修改该参数。
    </Callout>

    ### 确认配置结果 [step]

    1. 返回 **General** 列表，确认目标设备的 **Boot Policy** 已更新。
    2. 配置了大于 `0` 的延迟时，确认策略下方显示 `Delay: <seconds>`。
    3. 保存配置不会立即重启或关闭设备；策略在后续 aBMC 初始化或电源按键置为 ON 的流程中生效，上电延迟也会用于后续上电动作。
  </CodeBlockTab>

  <CodeBlockTab value="API">
    | 操作 | 方法 | URI |
    | --- | --- | --- |
    | 查询启动策略动作参数 | GET | `/redfish/v1/Systems/{{nodename}}/Actions/SetPowerConfig.ActionInfo` |
    | 设置设备启动策略 | POST | `/redfish/v1/Systems/{{nodename}}/Actions/SetPowerConfig` |

    设置启动策略时，请求体包含以下字段：

    ```json
    {
      "PowerOnDelaySeconds": 0,
      "PowerRestorePolicy": "AlwaysOn"
    }
    ```

    | 字段 | 类型 | 说明 |
    | --- | --- | --- |
    | `PowerOnDelaySeconds` | integer | 延迟启动秒数，页面要求为不小于 `0` 的整数。 |
    | `PowerRestorePolicy` | string | 启动策略，可选值以动作参数接口返回的 `AllowableValues` 为准。 |

    <Callout title="提示" type="info">
      关于接口认证、请求参数、返回字段和错误码的详细说明，请查看 Redfish API 文档。提交配置前，应先查询动作参数并使用目标设备允许的取值。
    </Callout>
  </CodeBlockTab>
</CodeBlockTabs>


## 批量设置设备启动策略

<CodeBlockTabs defaultValue="WEB">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="WEB">WEB</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="WEB">
    ### 选择批量目标设备 [step]

    1. 在设备列表中勾选至少两台需要使用相同启动策略的设备。
    2. 单击任意一台已选设备 **Actions** 列中的设置图标。
    3. 选择 **Boot Policy Setting**。

    ### 下发批量启动策略 [step]

    1. 在 **Boot Policy** 中选择所有目标设备共同使用的策略。
    2. 在 **Power-on Delay** 中设置统一的延迟启动秒数。
    3. 再次核对已勾选的设备，确认它们适合使用相同配置。
    4. 单击 **Confirm**，页面会向每台已选设备分别下发启动策略。
    5. 返回列表，逐台确认 **Boot Policy** 和延迟时间已经更新。

    <Callout title="批量配置风险" type="warn">
      批量配置会向所有已选设备下发相同的启动策略和延迟时间。不同硬件、业务角色或供电顺序的设备可能需要不同策略；提交前应确认选择范围，避免影响阵列恢复顺序。
    </Callout>
  </CodeBlockTab>
</CodeBlockTabs>


## 管理设备电源

<CodeBlockTabs defaultValue="WEB">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="WEB">WEB</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="API">API</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="WEB">
    ### 打开电源管理菜单 [step]

    1. 单设备操作时，在目标设备的 **Actions** 列中单击设置图标，然后展开 **Power**。
    2. 批量操作时，先勾选至少两台设备，再从任意一台已选设备的 **Actions** 菜单中展开批量电源管理菜单。
    3. 页面读取 `ResetActionInfo`，只显示目标设备当前返回的可用动作；没有可用动作时，**Power** 菜单不可用。
    4. 确认动作含义、目标设备和业务影响后，再选择对应电源动作。选择后页面会立即发送请求，没有二次确认窗口。

    ### 电源动作说明 [step]

    当前 `bmc-core` 可以为阵列子板返回以下动作。实际菜单只显示目标设备通过 `ResetActionInfo` 返回的值。

    | ResetType | 页面含义 | 当前后端实际行为 | 使用建议 |
    | --- | --- | --- | --- |
    | `On` | 开机 | 调用硬件电源控制开启子板电源域。 | 用于正常启动已关闭的设备。设备已开启时通常无需重复执行。 |
    | `ForceOn` | 上电 | 当前版本与 `On` 调用相同的硬件上电路径。 | 仅在接口返回该动作且设备确实处于关闭状态时使用。 |
    | `GracefulShutdown` | 正常关机 | Linux 执行 `shutdown now -h`；Android 执行 `reboot -p`，由操作系统完成关机。 | 首选关机方式。应等待设备状态变为离线后再断开外部供电。 |
    | `ForceOff` | 强制下电 | 直接关闭硬件电源域，不等待操作系统退出。 | 只在正常关机无响应时使用；可能造成文件系统或业务数据损坏。 |
    | `GracefulRestart` | 重启 | 通过操作系统执行 `reboot`，不直接切断硬件电源。 | 首选重启方式。要求操作系统命令通道可用。 |
    | `ForceRestart` | 强制上下电 | 当前版本调用硬件复位路径。 | 操作系统无响应且必须恢复设备时使用，未保存数据会丢失。 |
    | `PowerCycle` | 强制重启 | 当前版本与 `ForceRestart` 一样调用硬件复位路径；根据硬件能力执行复位信号，或下电后重新上电。 | 与 `ForceRestart` 的实际效果在当前版本中基本相同。 |
    | `LoaderByHardware` | 进入硬件 Loader | 拉起 Maskrom/Loader 控制信号并执行硬件复位，使设备进入底层恢复或烧录模式。 | 仅用于固件恢复、烧录或维修；普通重启和业务运维不要使用。 |

    `PushPowerButton` 和 `Nmi` 虽然在 Redfish 类型定义中存在，但当前 `ResetActionInfo` 没有返回它们，后端动作处理也未启用，因此不会出现在页面菜单中。

    ### 执行单设备电源操作 [step]

    1. 根据 **Device**、**Status** 和当前业务确认目标设备。
    2. 优先选择 **GracefulShutdown** 或 **GracefulRestart**，让操作系统正常停止服务和写回数据。
    3. 只有操作系统失去响应时，才使用 **ForceOff**、**ForceRestart** 或 **PowerCycle**。
    4. 单击动作后立即开始执行。观察 **Status**、**Uptime** 和设备业务状态，确认动作完成。
    5. 如果动作失败，先重新查询可用动作并检查设备通信、权限和硬件电源控制状态，不要连续重复单击。

    ### 执行批量电源操作 [step]

    1. 勾选至少两台需要执行同一动作的设备。
    2. 打开任意已选设备的 **Actions > Power**。
    3. 确认所有已选设备均支持菜单中的目标 `ResetType`。
    4. 单击电源动作后，页面会并行向所有已选节点分别发送请求。
    5. 操作完成后逐台检查状态；单台请求失败不会自动回滚其他已经执行成功的设备。

    <Callout title="批量动作能力来源" type="warn">
      批量模式中的动作列表取自第一台已选设备，其他设备不一定支持相同的 `ResetType`。批量执行前应确认设备型号、固件和能力一致；否则可能出现部分设备成功、部分设备失败。
    </Callout>

    <Callout title="电源操作风险" type="warn">
      电源菜单没有二次确认。电源动作可能立即关闭、启动、复位设备或使设备进入 Loader，造成业务中断、未保存数据丢失、文件系统损坏或升级失败。执行前应停止相关业务、保存数据并确认设备没有处于升级或维护过程。执行电源动作需要 `OemPowerControl` 权限。
    </Callout>
  </CodeBlockTab>

  <CodeBlockTab value="API">
    | 操作 | 方法 | URI |
    | --- | --- | --- |
    | 查询设备允许的电源动作 | GET | `/redfish/v1/Systems/{{nodename}}/Actions/ResetActionInfo` |
    | 执行设备电源动作 | POST | `/redfish/v1/Systems/{{nodename}}/Actions/ComputerSystem.Reset` |

    执行电源动作时，请求体格式如下：

    ```json
    {
      "ResetType": "<ResetActionInfo 返回的允许值>"
    }
    ```

    例如，正常重启设备：

    ```json
    {
      "ResetType": "GracefulRestart"
    }
    ```

    | `ResetType` | API 行为摘要 |
    | --- | --- |
    | `On`、`ForceOn` | 开启硬件电源域。 |
    | `GracefulShutdown` | 请求操作系统正常关机。 |
    | `ForceOff` | 直接关闭硬件电源域。 |
    | `GracefulRestart` | 请求操作系统正常重启。 |
    | `ForceRestart`、`PowerCycle` | 执行硬件复位。 |
    | `LoaderByHardware` | 通过 Maskrom/Loader 控制进入硬件 Loader。 |

    <Callout title="提示" type="info">
      应先查询动作参数，并且只将 `AllowableValues` 中的值作为 `ResetType`。接口需要 `OemPowerControl` 权限。关于认证、返回字段和错误码的详细说明，请查看 Redfish API 文档。
    </Callout>
  </CodeBlockTab>
</CodeBlockTabs>


## 常见问题 FAQ

### 1.设备信息或资源利用率显示为 `-`

设备可能处于离线、升级或微服务加载阶段，也可能暂时无法返回对应指标。先检查 **Status**；设备恢复为 **Online** 或 **Ready** 后，等待页面更新。如果字段仍缺失，应检查设备通信、指标采集服务和相关接口响应。

### 2.设备长期停留在 **Linking** 或 **Loader**

**Linking** 表示设备微服务正在加载，**Loader** 表示设备处于升级状态。应等待当前流程完成并检查相关服务、升级任务和日志。不要在升级过程中执行关机或重启操作。

### 3.无法修改 Boot Policy 或 Power-on Delay

页面会根据启动策略动作参数接口判断字段是否允许修改。确认设备在线，并检查 `SetPowerConfig.ActionInfo` 返回的 `DisallowedInput` 和 `AllowableValues`。如果接口不允许输入该字段，应按照设备固件能力使用现有配置。

### 4.批量设置后部分设备没有更新

批量操作会分别向每台已选设备发送请求，部分设备可能因离线、能力不同或接口失败而未更新。返回列表逐台核对 **Boot Policy**，并对失败设备检查状态、动作参数和接口响应后单独重试。

### 5.Power 菜单为空或不可用

页面只显示目标设备通过 `ResetActionInfo` 返回的可用电源动作。确认当前账号具有 `OemPowerControl` 权限、设备处于可通信状态，并检查接口响应；不同设备、状态或固件版本支持的动作可能不同。

### 6.Self Check 或 File Transfer 图标无法单击

这两个入口在当前前端版本中固定为禁用状态，没有绑定自检或文件传输操作，属于预留功能。无法单击是当前设计，不需要排查设备状态或后端服务。

### 7.单击 Open Shell Command 后无法打开终端

先检查 `/redfish/v1/Managers/{{nodename}}` 是否返回 `SerialConsole.ConnectTypesSupported`。使用 SSH 时，还应确认用户名、密码或私钥正确，端口可访问，并允许浏览器为 aBMC 打开新窗口；浏览器阻止弹出窗口时，终端页面不会显示。

### 8.ForceRestart 和 PowerCycle 有什么区别

从 Redfish 语义看，两者分别表示强制重启和电源循环；但当前 `bmc-core` 实现将两者都映射到相同的硬件复位函数。该函数优先控制复位信号；没有独立复位控制时，才通过下电后重新上电完成复位。因此当前版本中的实际效果基本相同，均可能造成未保存数据丢失。
