# 网络接线方式

本章节介绍服务器网络的通用接线方法。以下接线方式可以满足大部分使用场景；如有特殊要求，或需要了解服务器内部的网络拓扑，请联系商务获取技术支持。

接网线前，先了解一下各个网口的作用：

- **MGMT 口**：直连 BMC，是服务器的带外管理口。
- **SFP+1～SFP+4 口**：服务器的业务网口，均连接至内部交换机。

带外管理是指不依赖操作系统与业务网络的远程管理通道：服务器只要接通电源，即使尚未开机或系统故障，也可以通过 MGMT 口访问 BMC，进行远程开关机、硬件监控、固件升级等操作，详见「访问 BMC」章节。

**接线方式分为带外管理和带内管理两种，任选其一都可以让整机连上外网；推荐优先使用带外管理。**

<CodeBlockTabs defaultValue="OutOfBand">
    <CodeBlockTabsList>
        <CodeBlockTabsTrigger value="OutOfBand">带外管理</CodeBlockTabsTrigger>
        <CodeBlockTabsTrigger value="InBand">带内管理</CodeBlockTabsTrigger>
    </CodeBlockTabsList>

    <CodeBlockTab value="OutOfBand">
      ## 带外管理：MGMT 口独立上联 [step]

      ![两根网线接线示意](../../../servers_img/CSC2-N48/start_server_network/dual_external_network_wiring.png)

      **工具准备**

      - 万兆光口转 RJ45 模块 × 1（[购买链接](https://item.taobao.com/item.htm?id=615471664761&skuId=4338443096690)）
      - 网线 × 2（一根连接 MGMT 口，一根连接外网光口上的模块）

      **接线步骤**

      1. 将一根网线接入服务器的 MGMT 口，另一端接入上游交换机（Upstream Switch）。
      2. 将万兆光口转 RJ45 模块插入服务器的 SFP+1～SFP+4 中任意一个光口（以 SFP+1 为例），另一根网线一端接入该模块，另一端接入同一台上游交换机（Upstream Switch）。

      该方式下，带外管理（MGMT 口）与业务网络（SFP+ 口）各自独立上联、互不影响；上游交换机再连接个人电脑（Personal PC）等终端，服务器即可与外部网络通信。
    </CodeBlockTab>

    <CodeBlockTab value="InBand">
      ## 带内管理：管理流量借道业务网络 [step]

      ![单独一根外网网线接线示意](../../../servers_img/CSC2-N48/start_server_network/single_external_network_wiring.png)

      **工具准备**

      - 万兆光口转 RJ45 模块 × 2（[购买链接](https://item.taobao.com/item.htm?id=615471664761&skuId=4338443096690)）
      - 网线 × 2（一根用于互连 MGMT 口与 SFP+1 口，一根用于连接上游交换机）

      **接线步骤**

      1. 将一个万兆光口转 RJ45 模块插入服务器的 SFP+1 光口，并使用一根网线将 MGMT 口与该模块直连。
      2. 将另一个模块插入 SFP+2、SFP+3、SFP+4 中任意一个光口（以 SFP+2 为例），外网网线一端接入该模块，另一端接入上游交换机（Upstream Switch）。

      说明：MGMT 口直连 BMC，将其与 SFP+1 口互连后，BMC 管理网口即可经由内部交换机、通过外网光口上的模块与外部网络通信。MGMT 口为千兆电口，SFP+1 口的模块与之协商为 1 Gbps。该方式下，带外管理流量借道业务网络，与业务共用同一根外网线；上游交换机再连接个人电脑（Personal PC）等终端。
    </CodeBlockTab>
</CodeBlockTabs>