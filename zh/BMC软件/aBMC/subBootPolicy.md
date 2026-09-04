# 子板电源启动策略

## 简介

子板电源启动策略（Boot Policy Setting）是 aBMC 面向阵列子板提供的电源恢复配置功能。管理员可以为单台子板设置电源恢复策略和上电延迟时间，使子板在 aBMC 服务初始化或电源恢复时按照预期状态启动。

## 开发愿景

1. 通过统一的策略配置入口，明确子板在电源恢复后的启动状态，降低人工逐台开机和恢复配置的运维成本。
2. 通过设置上电延迟，让阵列子板错峰启动，减少集中上电时的瞬时负载，并匹配现场的供电和业务恢复顺序。

# 功能使用

## 了解启动策略

<CodeBlockTabs defaultValue="WEB">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="WEB">WEB</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="WEB">
    ### 功能范围 [step]

    | 功能 | 说明 |
    | --- | --- |
    | 查看启动策略 | 在 **General** 设备列表的 **Boot** 列查看当前子板的启动策略和延迟时间。 |
    | 设置启动策略 | 为单台子板选择 **AlwaysOn**、**AlwaysOff** 或 **LastState**。 |
    | 设置上电延迟 | 配置电源上电前等待的秒数，值为不小于 `0` 的整数。 |
    | 查询配置能力 | 通过 `SetPowerConfig.ActionInfo` 查看目标子板允许修改的字段和策略取值。 |

    ### 使用前准备 [step]

    1. 已使用具备目标设备配置权限的账号登录 aBMC。
    2. 确认目标子板已经接入 aBMC，并且能够在 **Devices > General** 页面显示。
    3. 配置前确认目标设备名称、业务角色和预期的电源恢复行为。
    4. 批量或集中恢复供电前，确认策略和延迟不会破坏阵列设备的依赖顺序。

    ### 界面术语 [step]

    | 中文 | English |
    | --- | --- |
    | 启动策略 | Boot Policy |
    | 启动策略设置 | Boot Policy Setting |
    | 上电延迟 | Power-on Delay |
    | 始终开启 | AlwaysOn |
    | 始终关闭 | AlwaysOff |
    | 上一次状态 | LastState |

    ### 策略生效时机 [step]

    aBMC 启动 PowerManager 时会读取并应用子板的启动策略。在支持子板联动的机型中，整机电源按键上电或子板热插拔流程也会再次应用对应策略。保存配置不会立即重启、关机或改变当前已上电的子板状态；设置了大于 `0` 的 **Power-on Delay** 时，后续上电动作会在执行硬件上电回调前等待对应秒数。
  </CodeBlockTab>
</CodeBlockTabs>

## 查看子板的电源启动策略

<CodeBlockTabs defaultValue="WEB">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="WEB">WEB</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="WEB">
    ### 打开设备列表 [step]

    1. 在左侧主导航栏中选择 **Devices**。
    2. 在二级导航栏中选择 **General**。
    3. 在设备列表中定位目标子板。
    4. 查看 **Boot** 列中的启动策略；配置了大于 `0` 的延迟时，策略下方同时显示 `Delay: <seconds>s`。

    ![电源启动策略状态](../../../aBMC_img/v3.0/subBootPolicy_img/05-boot-policy-status-v2.png)

    ### 列表字段说明 [step]

    | 字段 | 说明 |
    | --- | --- |
    | Device | 子板在 aBMC 中的节点名称。 |
    | Boot | 当前启动策略，可能显示 **AlwaysOn**、**AlwaysOff** 或 **LastState**。 |
    | Delay | 当前启动策略对应的上电延迟，单位为秒；未设置延迟时通常不显示。 |
    | Status | 子板当前状态。设备离线或尚未完成状态采集时，其他字段可能显示为 `-`。 |

    <Callout title="状态核对" type="info">
      页面显示的是当前由设备上报并由 aBMC 列表刷新得到的配置。修改策略后，如果列表没有立即变化，请等待请求完成后刷新页面，并确认目标设备仍处于可通信状态。
    </Callout>
  </CodeBlockTab>
</CodeBlockTabs>

## 设置单台子板启动策略

<CodeBlockTabs defaultValue="WEB">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="WEB">WEB</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="API">API</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="WEB">
    ### 打开启动策略窗口 [step]

    1. 在 **Devices > General** 页面定位目标子板。
    2. 在目标设备 **Actions** 列中单击设置图标。
    3. 选择 **Boot Policy Setting**，打开启动策略窗口。

    ![打开启动策略设置](../../../aBMC_img/v3.0/subBootPolicy_img/01-chose-boot-policy-setting-v2.png)

    ### 查看当前配置 [step]

    启动策略窗口会显示目标子板当前的 **Boot Policy** 和 **Power-on Delay**。提交前先核对窗口顶部的设备名称，避免将配置写入错误的子板。

    ![启动策略设置窗口](../../../aBMC_img/v3.0/subBootPolicy_img/02-boot-policy-setting-pop-up-windows-v2.png)

    ### 选择启动策略 [step]

    在 **Boot Policy** 下拉框中选择目标策略：

    | 策略 | 说明 | 适用场景 |
    | --- | --- | --- |
    | **AlwaysOn** | aBMC 服务初始化或电源恢复时，强制开启该子板电源域。 | 需要随系统恢复自动启动的关键子板。 |
    | **AlwaysOff** | aBMC 服务初始化或电源恢复时，保持该子板电源域关闭。 | 需要人工确认后再启动，或暂不参与业务的子板。 |
    | **LastState** | 根据状态持久化记录恢复上一次开关机状态；没有有效记录或状态未知时，当前实现不执行该域的恢复动作。 | 希望尽量保持已记录的断电前运行状态的子板。 |

    ![选择启动策略](../../../aBMC_img/v3.0/subBootPolicy_img/03-boot-policy-option-v2.png)

    ### 设置上电延迟 [step]

    1. 在 **Power-on Delay** 中输入等待秒数。
    2. 输入值必须为不小于 `0` 的整数；设置为 `0` 表示不增加延迟。
    3. 结合阵列拓扑、设备依赖关系和供电能力设置合理的延迟。延迟只影响后续上电流程，不会使当前正在运行的设备自动重启。

    ![设置上电延迟](../../../aBMC_img/v3.0/subBootPolicy_img/04-boot-policy-delay-v2.png)

    ### 保存并确认结果 [step]

    1. 确认设备名称、**Boot Policy** 和 **Power-on Delay** 均正确。
    2. 单击 **Confirm** 保存；不保存修改时单击 **Cancel**。
    3. 返回 **General** 列表，确认 **Boot** 列已显示新的策略。
    4. 设置了大于 `0` 的延迟时，确认策略下方显示 `Delay: <seconds>s`。

    <Callout title="配置影响" type="warn">
      启动策略会影响目标子板后续的电源恢复行为。将策略设为 **AlwaysOff** 可能导致依赖该子板的业务不会自动恢复；将大量子板设为 **AlwaysOn** 可能增加集中上电时的负载。提交前应确认现场的供电规划和业务恢复要求。
    </Callout>
  </CodeBlockTab>

  <CodeBlockTab value="API">
    ### 查询目标设备的配置能力 [step]

    在设置前，先查询目标子板的 ActionInfo，确认字段是否允许输入以及 **PowerRestorePolicy** 的可选值。

    | 操作 | 方法 | URI |
    | --- | --- | --- |
    | 查询设备列表中的启动策略和延迟 | GET | `/redfish/v1/Oem/PrometheusServices/NodeConfig` |
    | 查询启动策略动作参数 | GET | `/redfish/v1/Systems/{{nodename}}/Actions/SetPowerConfig.ActionInfo` |
    | 设置启动策略 | POST | `/redfish/v1/Systems/{{nodename}}/Actions/SetPowerConfig` |

    ```json title="ActionInfo 响应示例"
    {
      "Parameters": [
        {
          "DisallowedInput": false,
          "DataType": "Number",
          "Name": "PowerOnDelaySeconds",
          "Required": false
        },
        {
          "DisallowedInput": false,
          "AllowableValues": ["AlwaysOn", "AlwaysOff", "LastState"],
          "DataType": "String",
          "Name": "PowerRestorePolicy",
          "Required": true
        }
      ]
    }
    ```

    ### 提交启动策略 [step]

    请求体包含以下字段：

    ```json title="设置启动策略"
    {
      "PowerRestorePolicy": "AlwaysOn",
      "PowerOnDelaySeconds": 0
    }
    ```

    | 字段 | 类型 | 必填 | 说明 |
    | --- | --- | --- | --- |
    | `PowerRestorePolicy` | string | 是 | 电源恢复策略。允许值以 ActionInfo 返回的 `AllowableValues` 为准，通常为 `AlwaysOn`、`AlwaysOff` 或 `LastState`。 |
    | `PowerOnDelaySeconds` | integer | 否 | 上电前延迟秒数，页面要求为不小于 `0` 的整数。不传时服务端按 `0` 处理。 |

    <CodeBlockTabs defaultValue="basic-auth">
      <CodeBlockTabsList>
        <CodeBlockTabsTrigger value="basic-auth">Basic Auth</CodeBlockTabsTrigger>
        <CodeBlockTabsTrigger value="token">Token</CodeBlockTabsTrigger>
      </CodeBlockTabsList>

      <CodeBlockTab value="basic-auth">
        ```bash title="设置上电策略"
        curl --user '<username>:<password>' \\
          --request POST \\
          --header 'Content-Type: application/json' \\
          --data '{
            "PowerRestorePolicy": "AlwaysOn",
            "PowerOnDelaySeconds": 0
          }' \\
          '<protocol>://<device-ip>:<port>/redfish/v1/Systems/<system-id>/Actions/SetPowerConfig'
        ```
      </CodeBlockTab>

      <CodeBlockTab value="token">
        ```bash title="使用 Token 设置上电策略"
        curl --request POST \\
          --header 'X-Xsrf-Token: <token>' \\
          --header 'Content-Type: application/json' \\
          --data '{
            "PowerRestorePolicy": "AlwaysOn",
            "PowerOnDelaySeconds": 0
          }' \\
          '<protocol>://<device-ip>:<port>/redfish/v1/Systems/<system-id>/Actions/SetPowerConfig'
        ```
      </CodeBlockTab>
    </CodeBlockTabs>

    <Callout title="接口权限和生效范围" type="info">
      查询 ActionInfo 和提交配置都需要目标设备的 `OemPowerControl` 权限。请求成功表示配置已经更新到运行时电源域并持久化，不会立即触发电源动作；策略和延迟会在后续 aBMC 初始化或电源恢复流程中使用。当前后端处理函数未单独校验 `PowerOnDelaySeconds` 的下限，调用 API 时应自行保证发送非负整数。`bmc` 系统本身不支持该操作。
    </Callout>
  </CodeBlockTab>
</CodeBlockTabs>

## 批量设置子板启动策略

<CodeBlockTabs defaultValue="WEB">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="WEB">WEB</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="WEB">
    ### 选择批量目标设备 [step]

    1. 在 **Devices > General** 页面勾选至少两台需要使用相同配置的子板。
    2. 单击任意一台已选设备 **Actions** 列中的设置图标。
    3. 选择 **Boot Policy Setting**，打开批量配置窗口。

    ### 提交批量配置 [step]

    1. 在 **Boot Policy** 中选择所有目标设备共同使用的策略。
    2. 在 **Power-on Delay** 中设置统一的延迟秒数。
    3. 核对已选设备范围后，单击 **Confirm**。
    4. 页面会向每台已选设备分别发送 `SetPowerConfig` 请求；请求全部完成后，页面同步更新列表中的策略和延迟。

    <Callout title="批量操作限制" type="warn">
      批量窗口只查询第一台已选设备的 ActionInfo，并使用其允许的策略值和字段能力；其他设备可能具有不同的固件能力。多个请求是并行发送的，某些设备失败时，已经成功的设备不会自动回滚。提交后请逐台核对 **Boot** 列，并对失败设备单独检查和重试。
    </Callout>
  </CodeBlockTab>
</CodeBlockTabs>

## 常见问题 FAQ

### 1. 启动策略下拉框为空或无法修改

页面会根据 `SetPowerConfig.ActionInfo` 的 `DisallowedInput` 和 `AllowableValues` 判断字段能力。请确认目标设备在线、账号具有 `OemPowerControl` 权限，并检查接口是否允许修改 `PowerRestorePolicy`。

### 2. Power-on Delay 无法保存

Web 界面的数字控件只允许输入不小于 `0` 的整数，并且会禁止小数和负数。API 调用也应发送非负整数；同时确认目标设备的 ActionInfo 没有将 `PowerOnDelaySeconds` 标记为禁止输入。

### 3. 保存后设备没有立即启动或关闭

这是预期行为。保存启动策略会更新运行时电源域配置并持久化，但不会立即执行开机、关机或重启；它会在 aBMC 启动、整机上电联动或特定热插拔流程应用。需要立即改变电源状态时，应使用 **General** 页面中的 **Power** 操作，并先确认其业务影响。

### 4. 批量配置后只有部分子板更新

批量操作会并行向每台选中设备发送请求，不同设备可能因权限、在线状态、动作参数能力或接口错误导致结果不同。返回列表逐台核对 **Boot** 和延迟显示，对失败设备检查 ActionInfo 和接口响应后单独重试。
