# K3 Platform Software Development

This page collects the official **SpacemiT** documentation links for **K3 platform software development**, covering the SDK, Linux kernel / U-Boot / OpenSBI compilation, root filesystem (ROOTFS) creation, image download & flashing, and the full workflow for deploying an x86-trained model to K3.

## Portals

- [Documentation Center](https://www.spacemit.com/community/document?lang=en)
- [Resources Download](https://www.spacemit.com/community/resources-download?lang=en)
- [Developer Center](https://developer.spacemit.com/?lang=en)
- [K3 Product](https://www.spacemit.com/products/keystone/k3?lang=en)

## K3 SDK & System

- [K3 SDK Overview](https://www.spacemit.com/community/document/info?lang=en&nodepath=hardware/key_stone/k3/k3_sw/k3_sdk_user_guide.md)
- [Bianbu Introduction](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/bianbu/root_overview.md)
- [Bianbu Images](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/bianbu/image.md)
- [Buildroot K3 Introduction](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/buildroot/k3_buildroot/intro.md)
- [Buildroot K3 Source Code](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/buildroot/k3_buildroot/source.md)
- [Buildroot K3 Images](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/buildroot/k3_buildroot/image.md)

## Kernel / U-Boot / OpenSBI Compilation

- [Kernel Compile (incl. U-Boot / OpenSBI)](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/bianbu/development/kernel_compile.md)
- [Cross-Compilation Toolchain Guide](https://www.spacemit.com/community/document/info?lang=en&nodepath=tools/user_guide/cross_compiler_user_guide.md)
- [Buildroot K3 Boot Development](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/buildroot/k3_buildroot/device/boot.md)
- [Peripheral Driver Development](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/buildroot/k3_buildroot/device/peripheral_driver/index.md)

## RootFS Download & Creation

- [Bianbu 4.0 ROOTFS Creation (K3)](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/bianbu/system_integration/bianbu_4.0_rootfs_create.md)
- [Image (Firmware) Creation](https://www.spacemit.com/community/document/info?lang=en&nodepath=software/SDK/bianbu/system_integration/image.md)

Official base ROOTFS download (Bianbu 4.0 / K3):

```text
https://archive.spacemit.com/bianbu-base/bianbu-base-26.04-base-riscv64.tar.gz
```

## Image Download & Flashing

- [K3 Bianbu Image](https://spacemit.com/community/resources-download/Images%20Collects/K3/Bianbu?lang=en)
- [K3 Buildroot Image](https://spacemit.com/community/resources-download/Images%20Collects/K3/Buildroot?lang=en)
- [Flashing Tool Manual](https://www.spacemit.com/community/document/info?lang=en&nodepath=tools/user_guide/flasher_user_guide.md)
- [K3 Pico-ITX User Guide](https://www.spacemit.com/community/document/info?lang=en&nodepath=hardware/eco/k3_pico/pico_user_guide.md)

## AI Model Deployment

- [SpacemiT-ONNXRuntime (inference)](https://www.spacemit.com/community/document/info?lang=en&nodepath=ai/compute_stack/ai_compute_stack/onnxruntime.md)
- [XSlim Quantization Tool](https://www.spacemit.com/community/document/info?lang=en&nodepath=ai/compute_stack/ai_compute_stack/xslim.md)
- [llama.cpp (LLM deployment)](https://www.spacemit.com/community/document/info?lang=en&nodepath=ai/compute_stack/ai_compute_stack/llama.cpp.md)
- [AI SDK (Vision / Speech / LLM / VLM)](https://www.spacemit.com/community/document/info?lang=en&nodepath=ai/application_tools/ai-sdk.md)
- [ONNX Model Deployment Guide](https://www.spacemit.com/community/document/info?lang=en&nodepath=courses/AI/01_AI%E5%9F%BA%E7%A1%80%E5%AD%A6%E4%B9%A0%E5%8F%8A%E5%AE%9E%E8%B7%B5/07_onnxruntime%E6%A8%A1%E5%9E%8B%E9%83%A8%E7%BD%B2.md)

## Official Source Repositories

- [SDK manifest](https://github.com/spacemit-com/manifests)
- [Linux kernel linux-6.18 (branch k3-br-v1.0.y)](https://github.com/spacemit-com/linux-6.18)
- [U-Boot uboot-2022.10](https://github.com/spacemit-com/uboot-2022.10)
- [OpenSBI](https://github.com/spacemit-com/opensbi)
- [Buildroot](https://github.com/spacemit-com/buildroot)
- [Cross-compilation toolchain](https://github.com/spacemit-com/toolchain)
- [XSlim quantization](https://github.com/spacemit-com/xslim)
- [ONNXRuntime](https://github.com/spacemit-com/onnxruntime)