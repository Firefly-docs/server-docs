# 快速开始

第一次使用 CSB1-N10SPK3？按任务直接进入，或切到「第一次使用」按 9 步走一遍。本页只做入口，详细说明在对应页面。

<Callout title="上电前先看：安全规范" type="warn">
  严禁带电插拔电源线；断电后等待 30 秒以上再上电。不同机型的上电方式不同，请先阅读对应机型的[电源操作](start_server_power)。
</Callout>

<CodeBlockTabs defaultValue="tasks">
    <CodeBlockTabsList>
        <CodeBlockTabsTrigger value="tasks">按任务查找</CodeBlockTabsTrigger>
        <CodeBlockTabsTrigger value="first">第一次使用</CodeBlockTabsTrigger>
    </CodeBlockTabsList>

    <CodeBlockTab value="tasks">
      <Cards>
        <Card title="安装与上电" href="start_server_installation">
          服务器安装、电源规范、上电与状态检查。
        </Card>
        <Card title="网络与登录" href="start_server_network">
          BMC IP、子板网络，以及 Web / SSH / 串口登录。
        </Card>
        <Card title="aBMC 功能" href="aBMC">
          用户、告警、存储、电源策略等常用管理功能。
        </Card>
        <Card title="应用案例" href="ap_vlan_Isolation">
          单网卡多网段、网络隔离、网络共享等案例。
        </Card>
        <Card title="固件与定制" href="bmc_firmware_upgrade">
          BMC 升级、支持列表、定制固件与 recovery。
        </Card>
        <Card title="运维与排障" href="op_issues_troubleshooting">
          日志收集、异常排查、子板断联处理。
        </Card>
      </Cards>
    </CodeBlockTab>

    <CodeBlockTab value="first">
      **1. 确认规格与支持**

      - [规格和组件](product_specifications)
      - [系统支持列表](product_system_support_list)

      **2. 开箱与配件确认**

      核对电源线、滑轨 / 支架、SFP 模块、串口线等配件，具体以机型配件清单为准。

      **3. 上电规范**

      - [电源操作](start_server_power)
      - 严禁带电插拔电源线；断电后等待 30 秒以上再上电。

      **4. 上电、开机与状态检查**

      - [状态识别](start_server_status)
      - 检查节点 / 系统指示灯和 BMC 指示灯，确认设备进入可管理状态。

      **5. 网络配置**

      - [网络接线方式](start_server_network)
      - [aBMC 网络配置](/docs/server/bmc-software/aBMC/bmcNetwork)

      **6. 登录 aBMC / 子板**

      - [访问 BMC](start_login_bmc)
      - [子节点登录](start_login_sub)
      - [串口使用](start_server_serial)

      **7. aBMC 功能与方案**

      - [aBMC 使用手册](/docs/server/bmc-software/aBMC/preface)
      - [应用案例](ap_vlan_Isolation)

      **8. 固件与定制**

      - [获取 SDK](sdk_application)
      - [定制固件](dev_sub_firmware)
      - [BMC 升级](bmc_firmware_upgrade)

      **9. 现场部署与上架安装**

      - [服务器安装](start_server_installation)
      - [电源操作](start_server_power)
      - [BMC 升级](bmc_firmware_upgrade)

      客户部署自己的业务软件，验收标准由客户自行确定。
    </CodeBlockTab>
</CodeBlockTabs>

## 下载与支持

<Cards>
  <Card title="下载与支持" href="/docs/server/bmc-software/aBMC/install">
    获取 aBMC 安装包，查看版本目录与安装命令。
  </Card>
  <Card title="固件支持列表" href="product_system_support_list">
    查看当前机型支持的固件版本。
  </Card>
  <Card title="更新记录" href="wiki_release_notes">
    查看 aBMC 与 Wiki 的最新变更。
  </Card>
</Cards>