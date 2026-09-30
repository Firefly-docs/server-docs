# K3 Platform Software Development

This page collects the official **SpacemiT** documentation links for **K3 platform software development**, covering the SDK, Linux kernel / U-Boot / OpenSBI compilation, root filesystem (ROOTFS) creation, image download & flashing, and the full workflow for deploying an x86-trained model to K3.

In the tables below, the **Chinese** column links to the Chinese document and the **English** column links to the corresponding English document.

## Portals

| Chinese | English |
| :--- | :--- |
| [文档中心](https://www.spacemit.com/community/document?lang=zh) | [Documentation Center](https://www.spacemit.com/community/document?lang=en) |
| [资源下载](https://www.spacemit.com/community/resources-download?lang=zh) | [Resources Download](https://www.spacemit.com/community/resources-download?lang=en) |
| [开发者中心](https://developer.spacemit.com/?lang=zh) | [Developer Center](https://developer.spacemit.com/?lang=en) |
| [K3 产品页](https://www.spacemit.com/products/keystone/k3?lang=zh) | [K3 Product](https://www.spacemit.com/products/keystone/k3?lang=en) |

## K3 SDK & System

| Chinese | English |
| :--- | :--- |
| [K3 SDK 概览](https://www.spacemit.com/community/document/info?lang=zh&nodepath=hardware/key_stone/k3/k3_sw/k3_sdk_user_guide.md) | [K3 SDK Overview](https://www.spacemit.com/community/document/info?lang=en&nodepath=hardware/key_stone/k3/k3_sw/k3_sdk_user_guide.md) |
| [Bianbu 简介](https://www.spacemit.com/community/document/info?lang=zh&nodepath=software/SDK/bianbu/root_overview.md) | [Bianbu Introduction](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/bianbu/root_overview.md) |
| [Bianbu 镜像](https://www.spacemit.com/community/document/info?lang=zh&nodepath=software/SDK/bianbu/image.md) | [Bianbu Images](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/bianbu/image.md) |
| [Buildroot K3 简介](https://www.spacemit.com/community/document/info?lang=zh&nodepath=software/SDK/buildroot/k3_buildroot/intro.md) | [Buildroot K3 Introduction](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/buildroot/k3_buildroot/intro.md) |
| [Buildroot K3 源码](https://www.spacemit.com/community/document/info?lang=zh&nodepath=software/SDK/buildroot/k3_buildroot/source.md) | [Buildroot K3 Source Code](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/buildroot/k3_buildroot/source.md) |
| [Buildroot K3 镜像](https://www.spacemit.com/community/document/info?lang=zh&nodepath=software/SDK/buildroot/k3_buildroot/image.md) | [Buildroot K3 Images](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/buildroot/k3_buildroot/image.md) |

## Kernel / U-Boot / OpenSBI Compilation

| Chinese | English |
| :--- | :--- |
| [内核编译（含 U-Boot / OpenSBI）](https://www.spacemit.com/community/document/info?lang=zh&nodepath=software/SDK/bianbu/development/kernel_compile.md) | [Kernel Compile (incl. U-Boot / OpenSBI)](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/bianbu/development/kernel_compile.md) |
| [交叉编译工具链使用手册](https://www.spacemit.com/community/document/info?lang=zh&nodepath=tools/user_guide/cross_compiler_user_guide.md) | [Cross-Compilation Toolchain Guide](https://www.spacemit.com/community/document/info?lang=en&nodepath=tools/user_guide/cross_compiler_user_guide.md) |
| [Buildroot K3 启动开发](https://www.spacemit.com/community/document/info?lang=zh&nodepath=software/SDK/buildroot/k3_buildroot/device/boot.md) | [Buildroot K3 Boot Development](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/buildroot/k3_buildroot/device/boot.md) |
| [外设驱动开发](https://www.spacemit.com/community/document/info?lang=zh&nodepath=software/SDK/buildroot/k3_buildroot/device/peripheral_driver/index.md) | [Peripheral Driver Development](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/buildroot/k3_buildroot/device/peripheral_driver/index.md) |

## RootFS Download & Creation

| Chinese | English |
| :--- | :--- |
| [Bianbu 4.0 ROOTFS 制作（K3）](https://www.spacemit.com/community/document/info?lang=zh&nodepath=software/SDK/bianbu/system_integration/bianbu_4.0_rootfs_create.md) | [Bianbu 4.0 ROOTFS Creation (K3)](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/bianbu/system_integration/bianbu_4.0_rootfs_create.md) |
| [固件制作](https://www.spacemit.com/community/document/info?lang=zh&nodepath=software/SDK/bianbu/system_integration/image.md) | [Image (Firmware) Creation](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/bianbu/system_integration/image.md) |

Official base ROOTFS download (Bianbu 4.0 / K3):

```text
https://archive.spacemit.com/bianbu-base/bianbu-base-26.04-base-riscv64.tar.gz
```

## Image Download & Flashing

| Chinese | English |
| :--- | :--- |
| [K3 Bianbu 镜像下载](https://spacemit.com/community/resources-download/Images%20Collects/K3/Bianbu?lang=zh) | [K3 Bianbu Image](https://spacemit.com/community/resources-download/Images%20Collects/K3/Bianbu?lang=en) |
| [K3 Buildroot 镜像下载](https://spacemit.com/community/resources-download/Images%20Collects/K3/Buildroot?lang=zh) | [K3 Buildroot Image](https://spacemit.com/community/resources-download/Images%20Collects/K3/Buildroot?lang=en) |
| [刷机工具使用手册](https://www.spacemit.com/community/document/info?lang=zh&nodepath=tools/user_guide/flasher_user_guide.md) | [Flashing Tool Manual](https://www.spacemit.com/community/document/info?lang=en&nodepath=tools/user_guide/flasher_user_guide.md) |
| [K3 Pico-ITX 用户指南](https://www.spacemit.com/community/document/info?lang=zh&nodepath=hardware/eco/k3_pico/pico_user_guide.md) | [K3 Pico-ITX User Guide](https://www.spacemit.com/community/document/info?lang=en&nodepath=hardware/eco/k3_pico/pico_user_guide.md) |

## AI Model Deployment (x86 training → K3)

Workflow: train on x86 + CUDA → export ONNX → quantize with XSlim → run on K3 via SpacemiT-ONNXRuntime.
Note: K3 is a RISC-V AI CPU and does not support CUDA; CUDA is only used on the x86 training side.

| Chinese | English |
| :--- | :--- |
| [SpacemiT-ONNXRuntime（推理部署）](https://www.spacemit.com/community/document/info?lang=zh&nodepath=ai/compute_stack/ai_compute_stack/onnxruntime.md) | [SpacemiT-ONNXRuntime (inference)](https://www.spacemit.com/community/document/info?lang=en&nodepath=ai/compute_stack/ai_compute_stack/onnxruntime.md) |
| [XSlim 量化工具](https://www.spacemit.com/community/document/info?lang=zh&nodepath=ai/compute_stack/ai_compute_stack/xslim.md) | [XSlim Quantization Tool](https://www.spacemit.com/community/document/info?lang=en&nodepath=ai/compute_stack/ai_compute_stack/xslim.md) |
| [llama.cpp（LLM 部署）](https://www.spacemit.com/community/document/info?lang=zh&nodepath=ai/compute_stack/ai_compute_stack/llama.cpp.md) | [llama.cpp (LLM deployment)](https://www.spacemit.com/community/document/info?lang=en&nodepath=ai/compute_stack/ai_compute_stack/llama.cpp.md) |
| [AI SDK（视觉 / 语音 / LLM / VLM）](https://www.spacemit.com/community/document/info?lang=zh&nodepath=ai/application_tools/ai-sdk.md) | [AI SDK (Vision / Speech / LLM / VLM)](https://www.spacemit.com/community/document/info?lang=en&nodepath=ai/application_tools/ai-sdk.md) |
| [ONNX 模型部署指南](https://www.spacemit.com/community/document/info?lang=zh&nodepath=courses/AI/01_AI%E5%9F%BA%E7%A1%80%E5%AD%A6%E4%B9%A0%E5%8F%8A%E5%AE%9E%E8%B7%B5/07_onnxruntime%E6%A8%A1%E5%9E%8B%E9%83%A8%E7%BD%B2.md) | [ONNX Model Deployment Guide](https://www.spacemit.com/community/document/info?lang=en&nodepath=courses/AI/01_AI%E5%9F%BA%E7%A1%80%E5%AD%A6%E4%B9%A0%E5%8F%8A%E5%AE%9E%E8%B7%B5/07_onnxruntime%E6%A8%A1%E5%9E%8B%E9%83%A8%E7%BD%B2.md) |

## Official Source Repositories

The following repositories are shared by Chinese and English (no language variants).

- [SDK manifest](https://github.com/spacemit-com/manifests)
- [Linux kernel linux-6.18 (branch k3-br-v1.0.y)](https://github.com/spacemit-com/linux-6.18)
- [U-Boot uboot-2022.10](https://github.com/spacemit-com/uboot-2022.10)
- [OpenSBI](https://github.com/spacemit-com/opensbi)
- [Buildroot](https://github.com/spacemit-com/buildroot)
- [Cross-compilation toolchain](https://github.com/spacemit-com/toolchain)
- [XSlim quantization](https://github.com/spacemit-com/xslim)
- [ONNXRuntime](https://github.com/spacemit-com/onnxruntime)