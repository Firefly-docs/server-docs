# aBMC

## 简介
萤火服务器高级管理系统（Firefly Advanced Baseboard Management Controller，以下简称 aBMC）是部署在BMC上专为高密度阵列式服务器设计的带外集中管理软件解决方案。它提供从单节点硬件监控到整阵列资源池化的全生命周期管理，支持包括 KVM over IP-KVM（网络远程桌面）、虚拟介质挂载等远程运维能力。aBMC 提供标准的 Redfish 接口与完善的 CLI 命令行，方便用户将系统无缝集成至现有自动化运维平台，助力二次开发与智能运维转型。

![aBMC Backend Core Framework](../../../aBMC_img/common/aBMC-backend_core.png)

## 愿景
1. 业务侧：帮助客户在 10 天内完成从设备上架到业务系统正式上线的全流程部署，大幅缩短阵列服务器的业务交付周期。
2. 运维侧：攻克传统刀片及阵列服务器在多节点分散管理、批量运维效率低下、硬件监控粒度不足等方面的长期痛点，实现集中化、可视化的高效运维。
3. 开发侧：开放标准化的 Redfish 与 CLI 接口，屏蔽底层硬件差异，让开发者像操作单机一样操作整列资源
4. 生态侧：全系自研阵列服务器硬件原生适配 aBMC 管控框架。客户业务软件接入萤火硬件生态后，后续算力扩容、设备迭代升级均无需重新适配管理软件，规避兼容性改造风险，持续缩减迭代开发周期。

![aBMC Backend Core Framework](../../../aBMC_img/common/aBMC-Framework.png)

## 核心术语
1. aBMC: 萤火服务器高级管理系统（Firefly Advanced Baseboard Management Controller，以下简称 aBMC）是部署在BMC上专为高密度阵列式服务器设计的带外集中管理软件解决方案。
2. BMC: （Baseboard Management Controller，基板管理控制器）是服务器主板上独立于主CPU和操作系统运行的一块专用控制器，负责服务器的带外硬件监控、电源管理与远程运维。
3. 带外管理：不依赖服务器操作系统，独立硬件通道实现设备远程管控
4. Redfish：是服务器管理的“标准化REST API”，让不同厂商的硬件能用同一套接口被管理（Redfish 是一种基于 RESTful API 的现代服务器管理标准，由 DMTF 组织制定，用于替代传统 IPMI 协议。它通过 JSON 格式的 HTTP/HTTPS 接口，实现对服务器硬件（如温度、电源、固件版本、网络配置等）的远程发现、监控和管理，更易于开发集成且安全性更高）。
5. CLI：命令行交互工具，本地快速完成 BMC 批量运维操作。
6. KVM：KVM 是 Keyboard、Video、Mouse 的缩写，区别于机房中通过 VGA（极少数为 HDMI）和 USB 线缆连接的硬件 KVM 切换器。
7. IP-KVM：远程虚拟控制台，远程操作服务器显示器、键鼠。