# 网络接线方式

本章节介绍服务器网络的通用接线方法。以下接线方式可以满足大部分使用场景；如有特殊要求，或需要了解服务器内部的网络拓扑，请联系商务获取技术支持。

接网线前，先了解一下各个网口的作用：

- **MGMT 口**：直连 BMC，是服务器的带外管理口。
- **SFP+1、SFP+2、GE1、GE2 口**：服务器的业务网口，均连接至内部三层交换机，内部交换机再连接各子板。

带外管理是指不依赖操作系统与业务网络的远程管理通道：服务器只要接通电源，即使尚未开机或系统故障，也可以通过 MGMT 口访问 BMC，进行远程开关机、硬件监控、固件升级等操作，详见「访问 BMC」章节。

**接线方式分为带外管理和带内管理两种，任选其一都可以让整机连上外网；推荐优先使用带外管理。**

<CodeBlockTabs defaultValue="OutOfBand">
    <CodeBlockTabsList>
        <CodeBlockTabsTrigger value="OutOfBand">带外管理</CodeBlockTabsTrigger>
        <CodeBlockTabsTrigger value="InBand">带内管理</CodeBlockTabsTrigger>
    </CodeBlockTabsList>

    <CodeBlockTab value="OutOfBand">
      ![带外管理接线示意](../../../servers_img/CSB2-N10/start_server_network/out_of_band_management_wiring.png)

      **工具准备**

      - 网线 × 2（一根连接 MGMT 口，一根连接 GE1 口）

      **接线步骤**

      1. 将一根网线接入服务器的 MGMT 口，另一端接入上游交换机（Upstream Switch）。
      2. 将另一根网线接入服务器的 GE1 口，另一端接入同一台上游交换机（Upstream Switch）。

      该方式下，带外管理（MGMT 口）与业务网络（GE1 口）各自独立上联、互不影响；上游交换机再连接个人电脑（Personal PC）等终端，服务器即可与外部网络通信。
    </CodeBlockTab>

    <CodeBlockTab value="InBand">
      ![带内管理接线示意](../../../servers_img/CSB2-N10/start_server_network/in_band_management_wiring.png)

      **工具准备**

      - 网线 × 1（用于连接上游交换机）

      **接线步骤**

      1. 将网线接入服务器的 GE1 口，另一端接入上游交换机（Upstream Switch）。

      说明：该方式下只需一根网线接入 GE1 口，管理流量即可经由内部交换机借道业务网络与外部网络通信，与业务共用 GE1 口的同一根外网线；上游交换机再连接个人电脑（Personal PC）等终端。
    </CodeBlockTab>
</CodeBlockTabs>