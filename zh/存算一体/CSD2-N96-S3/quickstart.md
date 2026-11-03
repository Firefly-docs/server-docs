# 快速开始

第一次使用 CSD2-N96-S3？按任务直接进入，或切到「第一次使用」按 9 步走一遍。本页只做入口，详细说明在对应页面。

<Callout title="上电前先看：安全规范" type="warn">
  严禁带电插拔电源线；断电后等待 30 秒以上再上电。不同机型的上电方式不同，请先阅读对应机型的[电源操作](start-server-power)。
</Callout>

<CodeBlockTabs defaultValue="tasks">
    <CodeBlockTabsList>
        <CodeBlockTabsTrigger value="tasks">按任务查找</CodeBlockTabsTrigger>
        <CodeBlockTabsTrigger value="first">第一次使用</CodeBlockTabsTrigger>
    </CodeBlockTabsList>

    <CodeBlockTab value="tasks">
      <Cards>
        <Card title="安装与上电" href="start-server-installation">
          服务器安装、电源规范、上电与状态检查。
        </Card>
        <Card title="网络与登录" href="start-server-network">
          BMC IP、子板网络，以及 Web / SSH / 串口登录。
        </Card>
        <Card title="aBMC 功能" href="aBMC">
          用户、告警、存储、电源策略等常用管理功能。
        </Card>
        <Card title="应用案例" href="application-cases">
          单网卡多网段、网络隔离、网络共享等案例。
        </Card>
        <Card title="固件与定制" href="bmc-firmware-upgrade">
          BMC 升级、支持列表、定制固件与 recovery。
        </Card>
        <Card title="运维与排障" href="op-issues-troubleshooting">
          日志收集、异常排查、子板断联处理。
        </Card>
      </Cards>
    </CodeBlockTab>

    <CodeBlockTab value="first">
      **1. 确认规格与支持**

      - [规格和组件](product-specifications)
      - [系统支持列表](product-system-support-list)

      **2. 开箱与配件确认**

      核对电源线、滑轨 / 支架、SFP 模块、串口线等配件，具体以机型配件清单为准。

      **3. 上电规范**

      - [电源操作](start-server-power)
      - 严禁带电插拔电源线；断电后等待 30 秒以上再上电。

      **4. 上电、开机与状态检查**

      - [状态识别](start-server-status)
      - 检查节点 / 系统指示灯和 BMC 指示灯，确认设备进入可管理状态。

      **5. 网络接线与访问 BMC**

      - [网络接线方式](start-server-network)
      - [访问 BMC](start-login-bmc)

      **6. 配置 aBMC 网络与子板**

      - [aBMC 网络配置](/docs/server/bmc-software/aBMC/bmcNetwork)
      - [子节点登录](start-login-sub)
      - [串口使用](start-server-serial)

      **7. aBMC 功能与方案**

      - [aBMC 使用手册](/docs/server/bmc-software/aBMC/preface)
      - [应用案例](application-cases)

      **8. 固件与定制**

      - [获取 SDK](sdk-application)
      - [定制固件](dev-sub-firmware)
      - [BMC 升级](bmc-firmware-upgrade)

      **9. 现场部署与上架安装**

      - [服务器安装](start-server-installation)
      - [电源操作](start-server-power)

      客户部署自己的业务软件，验收标准由客户自行确定。
    </CodeBlockTab>
</CodeBlockTabs>

## 下载与支持

<Cards>
  <Card title="下载与支持" href="/docs/server/bmc-software/aBMC/install">
    获取 aBMC 安装包，查看版本目录与安装命令。
  </Card>
  <Card title="固件支持列表" href="product-system-support-list">
    查看当前机型支持的固件版本。
  </Card>
  <Card title="更新记录" href="wiki-release-notes">
    查看 aBMC 与 Wiki 的最新变更。
  </Card>
</Cards>