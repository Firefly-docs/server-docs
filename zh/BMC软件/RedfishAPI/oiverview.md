# 介绍

萤火服务器高级管理系统（Firefly Advanced Baseboard Management Controller，以下简称 aBMC）提供了一套符合Redfish™的DMTF（分布式管理任务组）标准的易用RESTful API，可用于访问运行在aBMC框架外的应用程序中的aBMC数据和服务。本文档简要介绍了 Redfish 及使用 aBMC Redfish API 的指南。

## Redfish API 是什么

Redfish是一种基于HTTPs服务的管理标准，利用RESTful接口实现设备管理。每个HTTPs操作都以UTF-8编码的JSON的形式，提交或返回一个资源。就像Web应用程序向浏览器返回HTML一样，RESTful接口会通过同样的传输机制(HTTPS)，以JSON的形式向客户端返回数据。
当前，整个互联网正逐渐向通用的新软件接口模式发展，Redfish无疑契合了这一趋势。相比之前的技术，它们易于实施、易于使用而且提供了可扩展性优势。Redfish的同一个数据模型既可以用于传统机架安装式服务器、刀片，也可以用于新型系统。此优势源自于数据模型设计用来向客户端自我描述服务功能，而且从一开始便为设计灵活性预留了足够空间

## 文档范围

本手册按业务资源组织接口：

| 章节 | 内容 |
| --- | --- |
| [Basic资源](docs/server/bmc-software/RedfishAPI/basicResources) | Redfish 版本入口和 Service Root。 |
| [Chassis资源](docs/server/bmc-software/RedfishAPI/chassisResources) | 机箱状态、风扇、UID 和总电源管理。 |
| [Systems资源](docs/server/bmc-software/RedfishAPI/systemResources) | 系统状态、网络、时间、磁盘、电源、端口映射和服务管理。 |
| [Manage资源](docs/server/bmc-software/RedfishAPI/managerResources) | BMC 管理资源和日志资源。 |
| [OEM 资源](docs/server/bmc-software/RedfishAPI/oemResources) | 远程管理和告警等 Firefly 扩展能力。 |
| [Users资源](docs/server/bmc-software/RedfishAPI/accountResources) | 角色、账号和用户服务管理。 |
| [Switchs资源](docs/server/bmc-software/RedfishAPI/switchResources) | 交换机二层、三层、VLAN 和聚合管理。 |
| [Upgrades管理资源](docs/server/bmc-software/RedfishAPI/upgradesResources) | 固件查询、升级状态和升级操作。 |

