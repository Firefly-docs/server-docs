# K3平台软件开发

本文汇总 **K3 平台软件开发**相关的进迭时空（SpacemiT）官方文档链接，涵盖 SDK 获取、内核 / U-Boot / OpenSBI 编译、根文件系统（ROOTFS）制作、镜像下载与刷机，以及「x86 训练模型 → K3 部署」的完整教程。

## 官方入口

| 内容 | 中文 | English |
| :--- | :--- | :--- |
| 文档中心 | [中文](https://www.spacemit.com/community/document?lang=zh) | [English](https://www.spacemit.com/community/document?lang=en) |
| 资源下载 | [中文](https://www.spacemit.com/community/resources-download?lang=zh) | [English](https://www.spacemit.com/community/resources-download?lang=en) |
| 开发者中心 | [中文](https://developer.spacemit.com/?lang=zh) | [English](https://developer.spacemit.com/?lang=en) |
| K3 产品页 | [中文](https://www.spacemit.com/products/keystone/k3?lang=zh) | [English](https://www.spacemit.com/products/keystone/k3?lang=en) |

## K3 SDK 与系统

| 内容 | 中文 | English |
| :--- | :--- | :--- |
| K3 SDK 概览 | [中文](https://www.spacemit.com/community/document/info?lang=zh&nodepath=hardware/key_stone/k3/k3_sw/k3_sdk_user_guide.md) | [English](https://www.spacemit.com/community/document/info?lang=en&nodepath=hardware/key_stone/k3/k3_sw/k3_sdk_user_guide.md) |
| Bianbu 简介 | [中文](https://www.spacemit.com/community/document/info?lang=zh&nodepath=software/SDK/bianbu/root_overview.md) | [English](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/bianbu/root_overview.md) |
| Bianbu 镜像 | [中文](https://www.spacemit.com/community/document/info?lang=zh&nodepath=software/SDK/bianbu/image.md) | [English](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/bianbu/image.md) |
| Buildroot K3 简介 | [中文](https://www.spacemit.com/community/document/info?lang=zh&nodepath=software/SDK/buildroot/k3_buildroot/intro.md) | [English](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/buildroot/k3_buildroot/intro.md) |
| Buildroot K3 源码 | [中文](https://www.spacemit.com/community/document/info?lang=zh&nodepath=software/SDK/buildroot/k3_buildroot/source.md) | [English](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/buildroot/k3_buildroot/source.md) |
| Buildroot K3 镜像 | [中文](https://www.spacemit.com/community/document/info?lang=zh&nodepath=software/SDK/buildroot/k3_buildroot/image.md) | [English](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/buildroot/k3_buildroot/image.md) |

## 编译内核 / U-Boot / OpenSBI

| 内容 | 中文 | English |
| :--- | :--- | :--- |
| 内核编译（含 U-Boot / OpenSBI） | [中文](https://www.spacemit.com/community/document/info?lang=zh&nodepath=software/SDK/bianbu/development/kernel_compile.md) | [English](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/bianbu/development/kernel_compile.md) |
| 交叉编译工具链使用手册 | [中文](https://www.spacemit.com/community/document/info?lang=zh&nodepath=tools/user_guide/cross_compiler_user_guide.md) | [English](https://www.spacemit.com/community/document/info?lang=en&nodepath=tools/user_guide/cross_compiler_user_guide.md) |
| Buildroot K3 启动开发 | [中文](https://www.spacemit.com/community/document/info?lang=zh&nodepath=software/SDK/buildroot/k3_buildroot/device/boot.md) | [English](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/buildroot/k3_buildroot/device/boot.md) |
| 外设驱动开发 | [中文](https://www.spacemit.com/community/document/info?lang=zh&nodepath=software/SDK/buildroot/k3_buildroot/device/peripheral_driver/index.md) | [English](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/buildroot/k3_buildroot/device/peripheral_driver/index.md) |

## 根文件系统 ROOTFS 下载与制作

| 内容 | 中文 | English |
| :--- | :--- | :--- |
| Bianbu 4.0 ROOTFS 制作（K3） | [中文](https://www.spacemit.com/community/document/info?lang=zh&nodepath=software/SDK/bianbu/system_integration/bianbu_4.0_rootfs_create.md) | [English](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/bianbu/system_integration/bianbu_4.0_rootfs_create.md) |
| 固件制作 | [中文](https://www.spacemit.com/community/document/info?lang=zh&nodepath=software/SDK/bianbu/system_integration/image.md) | [English](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/bianbu/system_integration/image.md) |

官方基础 ROOTFS 下载地址（Bianbu 4.0 / K3）：

```text
https://archive.spacemit.com/bianbu-base/bianbu-base-26.04-base-riscv64.tar.gz
```

## 镜像下载与刷机

| 内容 | 中文 | English |
| :--- | :--- | :--- |
| K3 Bianbu 镜像下载 | [中文](https://spacemit.com/community/resources-download/Images%20Collects/K3/Bianbu?lang=zh) | [English](https://spacemit.com/community/resources-download/Images%20Collects/K3/Bianbu?lang=en) |
| K3 Buildroot 镜像下载 | [中文](https://spacemit.com/community/resources-download/Images%20Collects/K3/Buildroot?lang=zh) | [English](https://spacemit.com/community/resources-download/Images%20Collects/K3/Buildroot?lang=en) |
| 刷机工具使用手册 | [中文](https://www.spacemit.com/community/document/info?lang=zh&nodepath=tools/user_guide/flasher_user_guide.md) | [English](https://www.spacemit.com/community/document/info?lang=en&nodepath=tools/user_guide/flasher_user_guide.md) |
| K3 Pico-ITX 用户指南 | [中文](https://www.spacemit.com/community/document/info?lang=zh&nodepath=hardware/eco/k3_pico/pico_user_guide.md) | [English](https://www.spacemit.com/community/document/info?lang=en&nodepath=hardware/eco/k3_pico/pico_user_guide.md) |

## AI 模型部署

| 内容 | 中文 | English |
| :--- | :--- | :--- |
| SpacemiT-ONNXRuntime（推理部署） | [中文](https://www.spacemit.com/community/document/info?lang=zh&nodepath=ai/compute_stack/ai_compute_stack/onnxruntime.md) | [English](https://www.spacemit.com/community/document/info?lang=en&nodepath=ai/compute_stack/ai_compute_stack/onnxruntime.md) |
| XSlim 量化工具 | [中文](https://www.spacemit.com/community/document/info?lang=zh&nodepath=ai/compute_stack/ai_compute_stack/xslim.md) | [English](https://www.spacemit.com/community/document/info?lang=en&nodepath=ai/compute_stack/ai_compute_stack/xslim.md) |
| llama.cpp（LLM 部署） | [中文](https://www.spacemit.com/community/document/info?lang=zh&nodepath=ai/compute_stack/ai_compute_stack/llama.cpp.md) | [English](https://www.spacemit.com/community/document/info?lang=en&nodepath=ai/compute_stack/ai_compute_stack/llama.cpp.md) |
| AI SDK（视觉 / 语音 / LLM / VLM） | [中文](https://www.spacemit.com/community/document/info?lang=zh&nodepath=ai/application_tools/ai-sdk.md) | [English](https://www.spacemit.com/community/document/info?lang=en&nodepath=ai/application_tools/ai-sdk.md) |
| ONNX 模型部署指南 | [中文](https://www.spacemit.com/community/document/info?lang=zh&nodepath=courses/AI/01_AI%E5%9F%BA%E7%A1%80%E5%AD%A6%E4%B9%A0%E5%8F%8A%E5%AE%9E%E8%B7%B5/07_onnxruntime%E6%A8%A1%E5%9E%8B%E9%83%A8%E7%BD%B2.md) | [English](https://www.spacemit.com/community/document/info?lang=en&nodepath=courses/AI/01_AI%E5%9F%BA%E7%A1%80%E5%AD%A6%E4%B9%A0%E5%8F%8A%E5%AE%9E%E8%B7%B5/07_onnxruntime%E6%A8%A1%E5%9E%8B%E9%83%A8%E7%BD%B2.md) |

## 官方源码仓库

| 组件 | 仓库地址 |
| :--- | :--- |
| SDK 清单 manifests | [https://github.com/spacemit-com/manifests](https://github.com/spacemit-com/manifests) |
| Linux 内核 linux-6.18（分支 k3-br-v1.0.y） | [https://github.com/spacemit-com/linux-6.18](https://github.com/spacemit-com/linux-6.18) |
| U-Boot uboot-2022.10 | [https://github.com/spacemit-com/uboot-2022.10](https://github.com/spacemit-com/uboot-2022.10) |
| OpenSBI | [https://github.com/spacemit-com/opensbi](https://github.com/spacemit-com/opensbi) |
| Buildroot | [https://github.com/spacemit-com/buildroot](https://github.com/spacemit-com/buildroot) |
| 交叉编译工具链 | [https://github.com/spacemit-com/toolchain](https://github.com/spacemit-com/toolchain) |
| XSlim 量化 | [https://github.com/spacemit-com/xslim](https://github.com/spacemit-com/xslim) |
| ONNXRuntime | [https://github.com/spacemit-com/onnxruntime](https://github.com/spacemit-com/onnxruntime) |