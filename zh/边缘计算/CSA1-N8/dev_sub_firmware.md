# 二次定制化固件


## 1. 简介
`firmware-kits` 是我司开发的一个二次定制化固件辅助工具。该工具具有以下功能：
1. 固件升级包二次解升级包
2. 修改或替换文件系统
3. 替换分区
4. 合成升级包辅助工具

## 2. 开发愿景
1. 在工厂量产交付场景中，用户往往希望将开发、生产工具固化至固件升级包内，保障量产版本一致性。本工具旨在提供一套便捷实现方案，降低版本标准化落地成本。
2. 搭建脱离真机硬件的固件定制与预调试环境。基于 X86‑ARM64 QEMU 虚拟化技术，可直接运行目标文件系统；开发者能够在虚拟环境中完成应用预装、自定义配置与调试工作，无需依赖真机硬件，减少硬件资源占用，有效降低开发与硬件投入成本。



## 3. 固件预处理

`firmware-kits` 用于在正式固件升级前，对 BM1684 和 RK3588 固件包进行预处理。它可以按指定流程解包原始固件、调整 ROOTFS/DATA/CUSTOM 等分区大小、挂载 rootfs 供人工修改系统内容，并在修改完成后重新打包生成可用于交付或升级的固件包。

该工具适用于需要在升级前定制固件内容的场景，例如扩容分区、预置文件、调整 rootfs 配置、生成 TFTP 或 SD 卡升级包。普通固件升级仍应优先使用本页面前文的升级管理功能；只有在固件需要先被修改或重新打包时，才使用 `firmware-kits`。

如果你的流程包含“设备侧导出 rootfs + PC 端重新打包固件”，请同时参考 [设备 rootfs 导出与固件重新打包](recovery_export.mdpp)。

下面说明如何使用 `firmware-kits` 对 BM1684 和 RK3588 固件进行解包、修改和重新打包。请严格按步骤操作。

所有命令都在 `firmware-kits` 目录下执行。

## 4. 环境准备

### 4.1 操作系统要求

推荐使用 Ubuntu 20.04/22.04 x86_64 主机。

### 4.2 安装依赖
<CodeBlockTabs defaultValue="debian_ubuntu">
    <CodeBlockTabsList>
        <CodeBlockTabsTrigger value="debian_ubuntu">Debian / Ubuntu</CodeBlockTabsTrigger>
        <CodeBlockTabsTrigger value="opensuse">openSUSE</CodeBlockTabsTrigger>
        <CodeBlockTabsTrigger value="fedora_rhel">Fedora / RHEL</CodeBlockTabsTrigger>
    </CodeBlockTabsList>

    <CodeBlockTab value="debian_ubuntu">
    ```bash
    sudo apt update
    sudo apt install -y u-boot-tools qemu-user-static p7zip-full unzip e2fsprogs
    ```
    </CodeBlockTab>
    <CodeBlockTab value="opensuse">
    ```bash
    sudo zypper refresh
    sudo zypper install -y u-boot-tools qemu-user-static p7zip unzip e2fsprogs
    ```
    </CodeBlockTab>

    <CodeBlockTab value="fedora_rhel">
    ```bash
    sudo dnf refresh
    sudo dnf install -y u-boot-tools qemu-user-static p7zip unzip e2fsprogs
    ```
    </CodeBlockTab>
</CodeBlockTabs>
### 4.3 下载工具包
### 4.4 检查工具包目录

进入工具包目录：

```bash
cd firmware-kits
```

确认目录中至少有这些内容：

```text
firmware-kits
flow/
scripts/
tools/
bin/
```

确认主程序可以执行：

```bash
./firmware-kits --help
```

固件处理会执行挂载、chroot、文件系统调整和重新打包，所以正式操作时需要使用 `sudo`。

## 5. 基本命令

从头开始处理固件：

```bash
sudo ./firmware-kits run -l <流程文件> -f <固件文件>
```

流程暂停后继续：

```bash
sudo ./firmware-kits resume
```

参数说明：

| 参数 | 说明 |
| ---- | ---- |
| `run` | 从头开始执行一个固件处理流程 |
| `resume` | 从上一次暂停的位置继续执行 |
| `-l` | 指定流程文件，例如 `flow/bm1684_tftp_data.yaml` |
| `-f` | 指定原始固件文件 |
| `-o` | 高级参数，追加传给处理脚本；没有特别说明时不要使用 |

工具执行到需要人工处理的位置时会自动暂停，并提示下一步要执行什么命令。人工操作完成后，执行 `sudo ./firmware-kits resume` 继续。

<Callout type="info" title="重要注意事项">
  1. 不要在流程暂停时删除 `out/`、`.firmware-kits_state.json` 或 `tools/.env`。
  2. 不要关闭已经挂载 rootfs 后的主机，除非已经确认卸载完成。
  3. 每次只处理一个固件流程，不要同时开多个终端跑多个 `firmware-kits run/resume`。
  4. 输出目录是 `out/`。确认不需要产物后，可以用 `sudo rm -rf out/` 清理空间。
  5. 如果流程提示存在中断记录，优先执行 `sudo ./firmware-kits resume`，不要直接重新 `run`。
</Callout>





## 6. 固件制作操作

### 6.1 获取升级固件

### 6.2 解开升级包
<CodeBlockTabs defaultValue="Rockchip">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="Rockchip">Rockchip</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="Sophgo">Sophgo</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="Rockchip">
  </CodeBlockTab>
  <CodeBlockTab value="Sophgo">
  </CodeBlockTab>
</CodeBlockTabs>


### 6.3 对 ROOTFS 进行扩容

<Callout type="info" title="提示">
  在对 ROOTFS 执行内容修改、新增文件部署之前，需要预先扩容 ROOTFS 分区与文件系统大小，预留足够存储空间，避免后续新增程序、配置文件、资源数据时出现空间不足，导致打包失败或固件运行异常。
</Callout>

<CodeBlockTabs defaultValue="Rockchip">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="Rockchip">Rockchip</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="Sophgo">Sophgo</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="Rockchip">
  </CodeBlockTab>
  <CodeBlockTab value="Sophgo">
  </CodeBlockTab>
</CodeBlockTabs>

### 6.4 替换文件系统


### 6.5 重新生成升级固件包
<CodeBlockTabs defaultValue="Rockchip">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="Rockchip">Rockchip</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="Sophgo">Sophgo</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="Rockchip">
  </CodeBlockTab>
  <CodeBlockTab value="Sophgo">
  </CodeBlockTab>
</CodeBlockTabs>





<CodeBlockTabs defaultValue="Rockchip">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="Rockchip">Rockchip</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="Sophgo">Sophgo</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="Rockchip">
  </CodeBlockTab>
  <CodeBlockTab value="Sophgo">
  </CodeBlockTab>
</CodeBlockTabs>




## FAQ

### 1. 提示权限不足

固件处理涉及挂载、chroot 和 root-owned 文件。请确认命令使用了 `sudo`。

如果删除输出目录失败，执行：

```bash
sudo rm -rf out/
```

### 2. 不确定当前该执行什么

如果流程暂停，终端通常会提示下一步命令。一般只需要：

1. 按提示查看或修改分区大小，或修改 rootfs 内容。
2. 完成后执行 `sudo ./firmware-kits resume`。

### 3. 不需要修改分区大小怎么办

在分区大小暂停点，如果不需要修改，直接执行：

```bash
sudo ./firmware-kits resume
```

### 4. 不需要修改 rootfs 内容怎么办

在 rootfs 内容修改暂停点，如果不需要修改，直接执行：

```bash
sudo ./firmware-kits resume
```

### 5. apt 命令出现 locale 或 /dev/pts 警告

部分 rootfs 环境可能显示 locale 或 `/dev/pts` 相关警告。只要命令最终执行成功，一般不影响固件制作。

### 6. 如何确认没有残留挂载

正常流程完成后通常不会残留挂载。需要检查时执行：

```bash
mount | grep firmware-kits/out
```

没有输出表示没有残留挂载。

如果有输出，请联系交付人员处理，不要随意删除正在挂载的目录。


### 7. 执行记录

随包提供两份实际执行记录，可下载后查看完整操作过程：

- [下载 BM1684 实际执行记录](/docs-assets/server-docs/aBMC_img/v3.0/firmware-kits/bm1684-run-2026-07-28.zip)
- [下载 RK3588 实际执行记录](/docs-assets/server-docs/aBMC_img/v3.0/firmware-kits/rk3588-run-2026-07-28.zip)

日志中只保留客户关心的固件处理命令、终端输出、暂停点操作、rootfs 内执行命令演示、拷贝文件演示、最终产物路径和挂载检查结果。


### 8. 执行异常中断恢复

如果执行 `run` 时看到类似提示：

```text
发现中断记录，请使用 `sudo ./firmware-kits resume` 继续执行
```

说明上一次流程没有完成。请执行：

```bash
sudo ./firmware-kits resume
```

如果确认上一次流程不要了，再清理状态和输出：

```bash
sudo rm -f .firmware-kits_state.json tools/.env
sudo rm -rf out/
```

清理后可以重新执行 `run`。