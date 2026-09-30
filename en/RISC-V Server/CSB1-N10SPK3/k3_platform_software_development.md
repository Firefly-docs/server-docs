# K3 Platform Software Development

This page collects the official **SpacemiT** documentation links for **K3 platform software development**, covering the SDK, Linux kernel / U-Boot / OpenSBI compilation, root filesystem (ROOTFS) creation, image download & flashing, and the full workflow for deploying an x86-trained model to K3.

## Portals

| Item | Chinese | English |
| :--- | :--- | :--- |
| Documentation Center | [中文](https://www.spacemit.com/community/document?lang=zh) | [English](https://www.spacemit.com/community/document?lang=en) |
| Resources Download | [中文](https://www.spacemit.com/community/resources-download?lang=zh) | [English](https://www.spacemit.com/community/resources-download?lang=en) |
| Developer Center | [中文](https://developer.spacemit.com/?lang=zh) | [English](https://developer.spacemit.com/?lang=en) |
| K3 Product | [中文](https://www.spacemit.com/products/keystone/k3?lang=zh) | [English](https://www.spacemit.com/products/keystone/k3?lang=en) |

## K3 SDK & System

| Item | Chinese | English |
| :--- | :--- | :--- |
| K3 SDK Overview | [中文](https://www.spacemit.com/community/document/info?lang=zh&nodepath=hardware/key_stone/k3/k3_sw/k3_sdk_user_guide.md) | [English](https://www.spacemit.com/community/document/info?lang=en&nodepath=hardware/key_stone/k3/k3_sw/k3_sdk_user_guide.md) |
| Bianbu Introduction | [中文](https://www.spacemit.com/community/document/info?lang=zh&nodepath=software/SDK/bianbu/root_overview.md) | [English](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/bianbu/root_overview.md) |
| Bianbu Images | [中文](https://www.spacemit.com/community/document/info?lang=zh&nodepath=software/SDK/bianbu/image.md) | [English](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/bianbu/image.md) |
| Buildroot K3 Introduction | [中文](https://www.spacemit.com/community/document/info?lang=zh&nodepath=software/SDK/buildroot/k3_buildroot/intro.md) | [English](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/buildroot/k3_buildroot/intro.md) |
| Buildroot K3 Source Code | [中文](https://www.spacemit.com/community/document/info?lang=zh&nodepath=software/SDK/buildroot/k3_buildroot/source.md) | [English](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/buildroot/k3_buildroot/source.md) |
| Buildroot K3 Images | [中文](https://www.spacemit.com/community/document/info?lang=zh&nodepath=software/SDK/buildroot/k3_buildroot/image.md) | [English](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/buildroot/k3_buildroot/image.md) |

## Kernel / U-Boot / OpenSBI Compilation

| Item | Chinese | English |
| :--- | :--- | :--- |
| Kernel Compile (incl. U-Boot / OpenSBI) | [中文](https://www.spacemit.com/community/document/info?lang=zh&nodepath=software/SDK/bianbu/development/kernel_compile.md) | [English](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/bianbu/development/kernel_compile.md) |
| Cross-Compilation Toolchain Guide | [中文](https://www.spacemit.com/community/document/info?lang=zh&nodepath=tools/user_guide/cross_compiler_user_guide.md) | [English](https://www.spacemit.com/community/document/info?lang=en&nodepath=tools/user_guide/cross_compiler_user_guide.md) |
| Buildroot K3 Boot Development | [中文](https://www.spacemit.com/community/document/info?lang=zh&nodepath=software/SDK/buildroot/k3_buildroot/device/boot.md) | [English](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/buildroot/k3_buildroot/device/boot.md) |
| Peripheral Driver Development | [中文](https://www.spacemit.com/community/document/info?lang=zh&nodepath=software/SDK/buildroot/k3_buildroot/device/peripheral_driver/index.md) | [English](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/buildroot/k3_buildroot/device/peripheral_driver/index.md) |

## RootFS Download & Creation

| Item | Chinese | English |
| :--- | :--- | :--- |
| Bianbu 4.0 ROOTFS Creation (K3) | [中文](https://www.spacemit.com/community/document/info?lang=zh&nodepath=software/SDK/bianbu/system_integration/bianbu_4.0_rootfs_create.md) | [English](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/bianbu/system_integration/bianbu_4.0_rootfs_create.md) |
| Image (Firmware) Creation | [中文](https://www.spacemit.com/community/document/info?lang=zh&nodepath=software/SDK/bianbu/system_integration/image.md) | [English](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/bianbu/system_integration/image.md) |

Official base ROOTFS download (Bianbu 4.0 / K3):

```text
https://archive.spacemit.com/bianbu-base/bianbu-base-26.04-base-riscv64.tar.gz
```

## Image Download & Flashing

| Item | Chinese | English |
| :--- | :--- | :--- |
| K3 Bianbu Image Download | [中文](https://spacemit.com/community/resources-download/Images%20Collects/K3/Bianbu?lang=zh) | [English](https://spacemit.com/community/resources-download/Images%20Collects/K3/Bianbu?lang=en) |
| K3 Buildroot Image Download | [中文](https://spacemit.com/community/resources-download/Images%20Collects/K3/Buildroot?lang=zh) | [English](https://spacemit.com/community/resources-download/Images%20Collects/K3/Buildroot?lang=en) |
| Flashing Tool User Manual | [中文](https://www.spacemit.com/community/document/info?lang=zh&nodepath=tools/user_guide/flasher_user_guide.md) | [English](https://www.spacemit.com/community/document/info?lang=en&nodepath=tools/user_guide/flasher_user_guide.md) |
| K3 Pico-ITX User Guide | [中文](https://www.spacemit.com/community/document/info?lang=zh&nodepath=hardware/eco/k3_pico/pico_user_guide.md) | [English](https://www.spacemit.com/community/document/info?lang=en&nodepath=hardware/eco/k3_pico/pico_user_guide.md) |

## AI Model Deployment

| Item | Chinese | English |
| :--- | :--- | :--- |
| SpacemiT-ONNXRuntime (inference) | [中文](https://www.spacemit.com/community/document/info?lang=zh&nodepath=ai/compute_stack/ai_compute_stack/onnxruntime.md) | [English](https://www.spacemit.com/community/document/info?lang=en&nodepath=ai/compute_stack/ai_compute_stack/onnxruntime.md) |
| XSlim Quantization Tool | [中文](https://www.spacemit.com/community/document/info?lang=zh&nodepath=ai/compute_stack/ai_compute_stack/xslim.md) | [English](https://www.spacemit.com/community/document/info?lang=en&nodepath=ai/compute_stack/ai_compute_stack/xslim.md) |
| llama.cpp (LLM deployment) | [中文](https://www.spacemit.com/community/document/info?lang=zh&nodepath=ai/compute_stack/ai_compute_stack/llama.cpp.md) | [English](https://www.spacemit.com/community/document/info?lang=en&nodepath=ai/compute_stack/ai_compute_stack/llama.cpp.md) |
| AI SDK (Vision / Speech / LLM / VLM) | [中文](https://www.spacemit.com/community/document/info?lang=zh&nodepath=ai/application_tools/ai-sdk.md) | [English](https://www.spacemit.com/community/document/info?lang=en&nodepath=ai/application_tools/ai-sdk.md) |
| ONNX Model Deployment Guide | [中文](https://www.spacemit.com/community/document/info?lang=zh&nodepath=courses/AI/01_AI%E5%9F%BA%E7%A1%80%E5%AD%A6%E4%B9%A0%E5%8F%8A%E5%AE%9E%E8%B7%B5/07_onnxruntime%E6%A8%A1%E5%9E%8B%E9%83%A8%E7%BD%B2.md) | [English](https://www.spacemit.com/community/document/info?lang=en&nodepath=courses/AI/01_AI%E5%9F%BA%E7%A1%80%E5%AD%A6%E4%B9%A0%E5%8F%8A%E5%AE%9E%E8%B7%B5/07_onnxruntime%E6%A8%A1%E5%9E%8B%E9%83%A8%E7%BD%B2.md) |

## Official Source Repositories

| Component | Repository |
| :--- | :--- |
| SDK manifest | [https://github.com/spacemit-com/manifests](https://github.com/spacemit-com/manifests) |
| Linux kernel linux-6.18 (branch k3-br-v1.0.y) | [https://github.com/spacemit-com/linux-6.18](https://github.com/spacemit-com/linux-6.18) |
| U-Boot uboot-2022.10 | [https://github.com/spacemit-com/uboot-2022.10](https://github.com/spacemit-com/uboot-2022.10) |
| OpenSBI | [https://github.com/spacemit-com/opensbi](https://github.com/spacemit-com/opensbi) |
| Buildroot | [https://github.com/spacemit-com/buildroot](https://github.com/spacemit-com/buildroot) |
| Cross-compilation toolchain | [https://github.com/spacemit-com/toolchain](https://github.com/spacemit-com/toolchain) |
| XSlim quantization | [https://github.com/spacemit-com/xslim](https://github.com/spacemit-com/xslim) |
| ONNXRuntime | [https://github.com/spacemit-com/onnxruntime](https://github.com/spacemit-com/onnxruntime) |