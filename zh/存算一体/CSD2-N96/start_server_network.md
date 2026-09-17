# 网络接线方式

本章节介绍服务器网络的通用接线方法。以下接线方式可以满足大部分使用场景；如有特殊要求，或需要了解服务器内部的网络拓扑，请联系商务获取技术支持。

接网线前，先了解一下各个网口的作用：

- **MGMT 口**：直连 BMC，是服务器的带外管理口。
- **SFP+1～SFP+8 口**：服务器的业务网口；其中 SFP+1～SFP+4 与 SFP+5～SFP+8 分属两个不同的内部三层交换机。

带外管理是指不依赖操作系统与业务网络的远程管理通道：服务器只要接通电源，即使尚未开机或系统故障，也可以通过 MGMT 口访问 BMC，进行远程开关机、硬件监控、固件升级等操作，详见「访问 BMC」章节。

**接线方式分为带外管理和带内管理两种，任选其一都可以让整机连上外网；推荐优先使用带外管理。**

<CodeBlockTabs defaultValue="OutOfBand">
    <CodeBlockTabsList>
        <CodeBlockTabsTrigger value="OutOfBand">带外管理</CodeBlockTabsTrigger>
        <CodeBlockTabsTrigger value="InBand">带内管理</CodeBlockTabsTrigger>
    </CodeBlockTabsList>

    <CodeBlockTab value="OutOfBand">
      ![带外管理接线示意](../../../servers_img/CSD2-Nx/start_server_network/out_of_band_management_wiring.png)

      **工具准备**

      - 万兆光口转 RJ45 模块 × 2（[购买链接](https://item.taobao.com/item.htm?id=615471664761&skuId=4338443096690)）
      - 网线 × 3（一根连接 MGMT 口，另两根分别连接 SFP+4、SFP+5 口上的模块）

      **接线步骤**

      1. 将一根网线接入服务器的 MGMT 口，另一端接入上游交换机（Upstream Switch）。
      2. 将一个万兆光口转 RJ45 模块插入服务器的 SFP+4 光口，一根网线一端接入该模块，另一端接入上游交换机（Upstream Switch）。
      3. 将另一个万兆光口转 RJ45 模块插入服务器的 SFP+5 光口，最后一根网线一端接入该模块，另一端接入上游交换机（Upstream Switch）。

      该方式下，带外管理（MGMT 口）与业务网络（SFP+ 口）各自独立上联、互不影响；两台内部交换机分别经由 SFP+4、SFP+5 独立上联外网，无需交换机间跳线；上游交换机再连接个人电脑（Personal PC）等终端，服务器即可与外部网络通信。
    </CodeBlockTab>

    <CodeBlockTab value="InBand">
      ![带内管理接线示意](../../../servers_img/CSD2-Nx/start_server_network/in_band_management_wiring.png)

      **工具准备**

      - 万兆光口转 RJ45 模块 × 2（[购买链接](https://item.taobao.com/item.htm?id=615471664761&skuId=4338443096690)）
      - 网线 × 2（分别连接 SFP+2、SFP+5 口上的模块）

      **接线步骤**

      1. 将一个万兆光口转 RJ45 模块插入服务器的 SFP+2 光口，一根网线一端接入该模块，另一端接入上游交换机（Upstream Switch）。
      2. 将另一个万兆光口转 RJ45 模块插入服务器的 SFP+5 光口，另一根网线一端接入该模块，另一端接入同一台上游交换机（Upstream Switch）。

      说明：该方式下 MGMT 口无需接线，管理流量借道业务网络：两台内部交换机分别经由 SFP+2、SFP+5 独立上联外网，BMC 管理流量经由内部交换机与外部网络通信；上游交换机再连接个人电脑（Personal PC）等终端。
    </CodeBlockTab>
</CodeBlockTabs>