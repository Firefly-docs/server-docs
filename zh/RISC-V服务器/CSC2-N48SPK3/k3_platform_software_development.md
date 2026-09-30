# K3平台软件开发

本文汇总 **K3 平台软件开发**相关的进迭时空（SpacemiT）官方文档链接，涵盖 SDK 获取、内核 / U-Boot / OpenSBI 编译、根文件系统（ROOTFS）制作、镜像下载与刷机，以及「x86 训练模型 → K3 部署」的完整教程。

下表中，**中文**列为中文文档，**English**列为对应的英文文档（同一文档的两种语言版本）。

## 官方入口

| 中文 | English |
| :--- | :--- |
| [文档中心](https://www.spacemit.com/community/document?lang=zh) | [Documentation Center](https://www.spacemit.com/community/document?lang=en) |
| [资源下载](https://www.spacemit.com/community/resources-download?lang=zh) | [Resources Download](https://www.spacemit.com/community/resources-download?lang=en) |
| [开发者中心](https://developer.spacemit.com/?lang=zh) | [Developer Center](https://developer.spacemit.com/?lang=en) |
| [K3 产品页](https://www.spacemit.com/products/keystone/k3?lang=zh) | [K3 Product](https://www.spacemit.com/products/keystone/k3?lang=en) |

## K3 SDK 与系统

| 中文 | English |
| :--- | :--- |
| [K3 SDK 概览](https://www.spacemit.com/community/document/info?lang=zh&nodepath=hardware/key_stone/k3/k3_sw/k3_sdk_user_guide.md) | [K3 SDK Overview](https://www.spacemit.com/community/document/info?lang=en&nodepath=hardware/key_stone/k3/k3_sw/k3_sdk_user_guide.md) |
| [Bianbu 简介](https://www.spacemit.com/community/document/info?lang=zh&nodepath=software/SDK/bianbu/root_overview.md) | [Bianbu Introduction](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/bianbu/root_overview.md) |
| [Bianbu 镜像](https://www.spacemit.com/community/document/info?lang=zh&nodepath=software/SDK/bianbu/image.md) | [Bianbu Images](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/bianbu/image.md) |
| [Buildroot K3 简介](https://www.spacemit.com/community/document/info?lang=zh&nodepath=software/SDK/buildroot/k3_buildroot/intro.md) | [Buildroot K3 Introduction](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/buildroot/k3_buildroot/intro.md) |
| [Buildroot K3 源码](https://www.spacemit.com/community/document/info?lang=zh&nodepath=software/SDK/buildroot/k3_buildroot/source.md) | [Buildroot K3 Source Code](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/buildroot/k3_buildroot/source.md) |
| [Buildroot K3 镜像](https://www.spacemit.com/community/document/info?lang=zh&nodepath=software/SDK/buildroot/k3_buildroot/image.md) | [Buildroot K3 Images](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/buildroot/k3_buildroot/image.md) |

## 编译内核 / U-Boot / OpenSBI

| 中文 | English |
| :--- | :--- |
| [内核编译（含 U-Boot / OpenSBI）](https://www.spacemit.com/community/document/info?lang=zh&nodepath=software/SDK/bianbu/development/kernel_compile.md) | [Kernel Compile (incl. U-Boot / OpenSBI)](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/bianbu/development/kernel_compile.md) |
| [交叉编译工具链使用手册](https://www.spacemit.com/community/document/info?lang=zh&nodepath=tools/user_guide/cross_compiler_user_guide.md) | [Cross-Compilation Toolchain Guide](https://www.spacemit.com/community/document/info?lang=en&nodepath=tools/user_guide/cross_compiler_user_guide.md) |
| [Buildroot K3 启动开发](https://www.spacemit.com/community/document/info?lang=zh&nodepath=software/SDK/buildroot/k3_buildroot/device/boot.md) | [Buildroot K3 Boot Development](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/buildroot/k3_buildroot/device/boot.md) |
| [外设驱动开发](https://www.spacemit.com/community/document/info?lang=zh&nodepath=software/SDK/buildroot/k3_buildroot/device/peripheral_driver/index.md) | [Peripheral Driver Development](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/buildroot/k3_buildroot/device/peripheral_driver/index.md) |

## 根文件系统 ROOTFS 下载与制作

| 中文 | English |
| :--- | :--- |
| [Bianbu 4.0 ROOTFS 制作（K3）](https://www.spacemit.com/community/document/info?lang=zh&nodepath=software/SDK/bianbu/system_integration/bianbu_4.0_rootfs_create.md) | [Bianbu 4.0 ROOTFS Creation (K3)](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/bianbu/system_integration/bianbu_4.0_rootfs_create.md) |
| [固件制作](https://www.spacemit.com/community/document/info?lang=zh&nodepath=software/SDK/bianbu/system_integration/image.md) | [Image (Firmware) Creation](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/bianbu/system_integration/image.md) |

官方基础 ROOTFS 下载地址（Bianbu 4.0 / K3）：

```text
https://archive.spacemit.com/bianbu-base/bianbu-base-26.04-base-riscv64.tar.gz
```

## 镜像下载与刷机

| 中文 | English |
| :--- | :--- |
| [K3 Bianbu 镜像下载](https://spacemit.com/community/resources-download/Images%20Collects/K3/Bianbu?lang=zh) | [K3 Bianbu Image](https://spacemit.com/community/resources-download/Images%20Collects/K3/Bianbu?lang=en) |
| [K3 Buildroot 镜像下载](https://spacemit.com/community/resources-download/Images%20Collects/K3/Buildroot?lang=zh) | [K3 Buildroot Image](https://spacemit.com/community/resources-download/Images%20Collects/K3/Buildroot?lang=en) |
| [刷机工具使用手册](https://www.spacemit.com/community/document/info?lang=zh&nodepath=tools/user_guide/flasher_user_guide.md) | [Flashing Tool Manual](https://www.spacemit.com/community/document/info?lang=en&nodepath=tools/user_guide/flasher_user_guide.md) |
| [K3 Pico-ITX 用户指南](https://www.spacemit.com/community/document/info?lang=zh&nodepath=hardware/eco/k3_pico/pico_user_guide.md) | [K3 Pico-ITX User Guide](https://www.spacemit.com/community/document/info?lang=en&nodepath=hardware/eco/k3_pico/pico_user_guide.md) |

## AI 模型部署（x86 训练 → K3）

整体流程：x86 + CUDA 训练 → 导出 ONNX → XSlim 量化 → K3 上使用 SpacemiT-ONNXRuntime 推理。
说明：K3 为 RISC-V AI CPU，不支持 CUDA；CUDA 仅用于 x86 训练端。

| 中文 | English |
| :--- | :--- |
| [SpacemiT-ONNXRuntime（推理部署）](https://www.spacemit.com/community/document/info?lang=zh&nodepath=ai/compute_stack/ai_compute_stack/onnxruntime.md) | [SpacemiT-ONNXRuntime (inference)](https://www.spacemit.com/community/document/info?lang=en&nodepath=ai/compute_stack/ai_compute_stack/onnxruntime.md) |
| [XSlim 量化工具](https://www.spacemit.com/community/document/info?lang=zh&nodepath=ai/compute_stack/ai_compute_stack/xslim.md) | [XSlim Quantization Tool](https://www.spacemit.com/community/document/info?lang=en&nodepath=ai/compute_stack/ai_compute_stack/xslim.md) |
| [llama.cpp（LLM 部署）](https://www.spacemit.com/community/document/info?lang=zh&nodepath=ai/compute_stack/ai_compute_stack/llama.cpp.md) | [llama.cpp (LLM deployment)](https://www.spacemit.com/community/document/info?lang=en&nodepath=ai/compute_stack/ai_compute_stack/llama.cpp.md) |
| [AI SDK（视觉 / 语音 / LLM / VLM）](https://www.spacemit.com/community/document/info?lang=zh&nodepath=ai/application_tools/ai-sdk.md) | [AI SDK (Vision / Speech / LLM / VLM)](https://www.spacemit.com/community/document/info?lang=en&nodepath=ai/application_tools/ai-sdk.md) |
| [ONNX 模型部署指南](https://www.spacemit.com/community/document/info?lang=zh&nodepath=courses/AI/01_AI%E5%9F%BA%E7%A1%80%E5%AD%A6%E4%B9%A0%E5%8F%8A%E5%AE%9E%E8%B7%B5/07_onnxruntime%E6%A8%A1%E5%9E%8B%E9%83%A8%E7%BD%B2.md) | [ONNX Model Deployment Guide](https://www.spacemit.com/community/document/info?lang=en&nodepath=courses/AI/01_AI%E5%9F%BA%E7%A1%80%E5%AD%A6%E4%B9%A0%E5%8F%8A%E5%AE%9E%E8%B7%B5/07_onnxruntime%E6%A8%A1%E5%9E%8B%E9%83%A8%E7%BD%B2.md) |

## 官方源码仓库

以下为中英文共用的代码仓库（无语言区分）。

- [SDK 清单 manifests](https://github.com/spacemit-com/manifests)
- [Linux 内核 linux-6.18（分支 k3-br-v1.0.y）](https://github.com/spacemit-com/linux-6.18)
- [U-Boot uboot-2022.10](https://github.com/spacemit-com/uboot-2022.10)
- [OpenSBI](https://github.com/spacemit-com/opensbi)
- [Buildroot](https://github.com/spacemit-com/buildroot)
- [交叉编译工具链](https://github.com/spacemit-com/toolchain)
- [XSlim 量化](https://github.com/spacemit-com/xslim)
- [ONNXRuntime](https://github.com/spacemit-com/onnxruntime)