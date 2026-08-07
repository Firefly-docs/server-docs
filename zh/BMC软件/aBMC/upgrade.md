# 固件升级


## 简介
固件升级是阵列服务器面向内部子节点提供的统一远程固件运维服务，集成固件包上传、批量节点升级、全流程任务监控三大核心能力，支撑子节点固件版本迭代、降级回滚与标准化批量部署。
固件升级。
1. 固件上传：支持 Web 管理界面、CLI 命令行两种交互渠道，将固件镜像包上传至集群管理节点统一存储。
2. 批量升级：针对出厂固件版本老旧或业务适配需切换指定固件等场景，用户可从官方渠道获取标准固件包，或基于开源 SDK 自主编译定制固件；上传镜像后选定目标子节点批量下发升级，灵活完成固件版本切换、降级回滚操作。
3. 升级任务监控：可视化展示全部升级任务实时状态，包含单节点升级进度、全局成功 / 失败节点统计，完整留存全量升级日志，便于故障追溯。



## 开发愿景
1. 提供轻量化远程子节点固件交付能力，运维人员无需线下接触物理服务器，通过 Web 页面或终端命令即可完成固件上传、批量部署，简化底层硬件运维工作量，让研发与运维人员聚焦上层业务开发。
打造标准化、高效率、高安全的现场业务交付链路：推荐用户使用官方原版固件包，配套官方二次打包工具，将自研业务程序与底层固件整合，生成内置业务应用的一体化定制固件镜像；依托阵列服务器批量升级能力，一次性完成集群所有子节点同步部署。研发侧可在实验室完成全量固件调试验证后，再交付现场实施，核心价值如下：
1.1 大幅压缩现场部署周期，降低线下人工实施成本；
1.2 统一实验室调试环境与现场生产环境，消除版本不一致引发的适配故障；
1.3 从根源保障客户业务代码安全：传统外包交付模式下，客户需移交业务程序给设备厂商，为防止核心代码泄露，往往额外开发复杂网络 / 本地密钥激活机制，既增加客户研发成本，也抬高厂商生产流程管控复杂度。而通过官方打包工具，用户可本地独立完成固件与业务软件融合，全程无需向第三方暴露核心源码，彻底规避业务资产泄露风险。

# 功能使用

## 升级阵列子节点固件

<CodeBlockTabs defaultValue="WEB">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="WEB">WEB</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="CLI">CLI</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="API">API</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="WEB">
    <Callout title="升级前请确认" type="warn">
      固件升级会重写目标阵列子节点的底层软件，并可能触发设备重启。执行前必须确认固件包、固件类型、设备型号和硬件版本匹配，同时保证供电与管理网络稳定。升级过程中不要断电、重启设备或重复提交任务。
    </Callout>

    ### 打开固件升级页面 [step]

    1. 在左侧主导航中选择 **Operations**。
    2. 在二级导航中选择 **Firmware**。
    3. 在页面顶部查看各状态的任务数量。
    4. 在 **Upgrade Queue** 中查看正在处理的升级任务。
    5. 在 **Upgrade Progress** 和 **Upgrade Result** 中查看执行进度与最终结果。
    6. 单击 **Add Sub-board Firmware Upgrade**，打开升级配置窗口。

    ![打开固件升级页面](../../../aBMC_img/v3.0/upgrade_img/01-open-firmware-upgrade-en-steps-v2.png)

    <Callout title="空列表说明" type="info">
      页面显示 **No Data** 或 **No Data Available** 时，表示当前没有对应的队列、进度或结果记录，不代表页面加载失败。
    </Callout>

    ### 配置升级任务 [step]

    1. 在 **Select Upgrade Firmware** 中选择与目标设备匹配的固件；列表中没有所需固件时，单击 **Upload Firmware** 上传。
    2. 在 **Select Firmware Type** 中选择固件对应的平台类型。
    3. 在左侧设备列表中勾选目标设备，并使用右箭头将其移入右侧列表。
    4. 在 **Upgrade Devices** 中逐项复核目标设备，移除不应参与升级的节点。
    5. 再次核对固件、类型和目标设备后，单击 **Confirm** 提交任务。

    ![配置 sub01 固件升级任务](../../../aBMC_img/v3.0/upgrade_img/02-configure-sub01-upgrade-en-steps-v2.png)

    <Callout title="实机升级示例" type="info">
      本次实机操作选择 **CS-B1-3576-JD4-SUB-MINIMAL_Debian_debug_250428.img**，固件类型为 **Rockchip**，目标设备仅为 **sub01**。提交前页面显示 **Upgrade Devices 1/1**，未选择其他子板。
    </Callout>

    ### 升级参数说明 [step]

    | 参数或区域 | 说明 | 使用要求 |
    | --- | --- | --- |
    | Select Upgrade Firmware | 选择已经上传到 aBMC 的固件镜像。 | 必须核对完整文件名、适用型号、硬件版本和发布说明。 |
    | Upload Firmware | 打开固件上传窗口。 | 仅上传来源可信且完整性校验通过的固件。 |
    | Select Firmware Type | 选择目标硬件平台对应的固件类型。 | 必须与固件包和目标设备匹配，例如 Rockchip。 |
    | All Devices | 选择左侧列表中的全部设备。 | 只有全部设备均适用同一固件时才能使用。 |
    | Upgrade Devices | 显示本次任务实际升级的设备。 | 提交前必须核对设备数量和名称。 |
    | Confirm | 创建并执行升级任务。 | 单击后会产生真实升级操作，不要重复提交。 |

    ### 监控升级过程 [step]

    1. 在顶部状态汇总中确认任务进入 **Preparing** 或 **Starting**。
    2. 在 **Upgrade Queue** 中确认 **Board** 为计划升级的设备。
    3. 持续观察 **Status**、**Progress** 和 **Message**，确认进度正常增长。
    4. 任务执行期间不要关闭设备电源、重启目标设备或再次创建相同任务。

    ![监控 sub01 固件升级进度](../../../aBMC_img/v3.0/upgrade_img/03-monitor-sub01-upgrade-en-steps-v2.png)

    本次实机升级依次经过 **Preparing** 和 **Starting**。任务进入 **Preparing** 时，页面显示 **Try to Loader 1/3**；进入 **Starting** 后进度从 0% 持续增长至 96%，随后转入 **Completed**。

    ### 升级状态说明 [step]

    | Status | 说明 | 处理建议 |
    | --- | --- | --- |
    | New | 升级任务已创建，通常会很快进入 **Pending**。 | 确认目标设备和固件信息正确。 |
    | Pending | 任务已进入调度队列，正在等待执行或所需资源。 | 继续等待；长时间无变化时，检查其他升级任务和资源占用情况。 |
    | Preparing | 正在检查固件、准备升级环境并使目标设备进入升级模式。 | 保持供电和网络稳定，不要重复提交升级任务。 |
    | Starting | 已开始向目标设备刷写固件。 | 持续观察 **Progress** 和 **Message**，不要重启或断电。 |
    | Completed | 固件刷写流程已成功结束，任务进度为 100%。 | 继续等待设备启动，并验证在线状态、固件信息、告警和业务功能。 |
    | Failed | 升级任务失败。 | 核对固件兼容性、设备状态、网络、供电和错误日志。 |

    ### 确认升级结果 [step]

    1. 确认页面顶部 **Completed** 数量增加，且 **Failed** 数量没有增加。
    2. 在 **Upgrade Result** 中确认目标 **Board**、**Start Time** 和 **Status**。
    3. 阅读 **Message**。如果页面提示等待系统启动，应按提示继续等待，不要立即断电或重复升级。
    4. 设备恢复后，检查电源状态、系统状态、固件版本、告警、日志和关键业务功能。

    ![确认 sub01 固件升级结果](../../../aBMC_img/v3.0/upgrade_img/04-verify-sub01-upgrade-result-en-steps-v2.png)

    本次实机升级结果如下：

    | 检查项 | 实际结果 |
    | --- | --- |
    | 目标设备 | sub01 |
    | 任务开始时间 | 2026-07-24T23:49:28.198+08:00 |
    | 最终状态 | Completed |
    | 结果消息 | Wait for the system to start normally, this process usually does not exceed 5 minutes |
    | PowerState | On |
    | StateSoc | Ready |
    | ComputerSystem Health | Critical |
    | 操作系统 | Debian GNU/Linux 12 (bookworm) |
    | KernelVersion | 6.1.84 |
    | 固件日期 | 2025-04-28T12:44:54.000Z |

    <Callout title="Completed 后仍需验证设备" type="warn">
      **Completed** 表示升级任务流程已经结束，不代表目标设备和业务已经立即恢复。本次实机验证中，sub01 已恢复为 **PowerState: On** 和 **StateSoc: Ready**，但 **ComputerSystem Health** 仍为 **Critical**。应继续检查设备告警和日志，不能仅根据任务完成和系统可访问判断设备整体健康。
    </Callout>

    ### 批量升级设备 [step]

    1. 仅在多台设备的型号、硬件版本和目标固件完全一致时执行批量升级。
    2. 使用搜索框逐台选择设备，或在确认全部设备均适用时选择 **All Devices**。
    3. 将设备移入 **Upgrade Devices** 后，核对设备数量和名称。
    4. 提交后在队列和结果区域逐台检查状态，不要只根据总体进度判断全部设备成功。

    <Callout title="建议先单节点验证" type="info">
      批量升级前，建议先选择一台非关键节点进行实机验证。确认固件兼容、设备能够正常启动且业务验证通过后，再扩大升级范围。
    </Callout>
  </CodeBlockTab>

  <CodeBlockTab value="CLI">
    <Callout title="升级前请确认" type="warn">
      `bmc upgrade update` 会立即向指定子节点提交真实固件升级任务。执行前必须核对固件文件名、平台类型和目标节点，并保证供电与管理网络稳定。
    </Callout>

    ### 上传固件 [step]

    指定本地固件路径后，CLI 会自动计算文件 MD5，按 10 MiB 分片上传，并在所有分片上传完成后合并固件。

    ```bash
    bmc upgrade upload --protocol <PROTOCOL> --ip <BMC_IP> --port <BMC_PORT> --user <USERNAME> --password <PASSWORD> --image <LOCAL_IMAGE_PATH>
    ```

    <Callout title="上传命令说明" type="info">
      当前 CLI 的帮助示例可能显示 `bmc upload`，但实际可用命令为 `bmc upgrade upload`。
    </Callout>

    ### 查看已上传固件 [step]

    创建升级任务前，先确认固件文件已完整保存在 aBMC 中。

    ```bash
    bmc upgrade image --protocol <PROTOCOL> --ip <BMC_IP> --port <BMC_PORT> --user <USERNAME> --password <PASSWORD>
    ```

    ### 创建单节点升级任务 [step]

    通过 `--core` 指定目标节点，通过 `--platform` 指定固件平台，`--image` 只填写已上传的固件文件名。

    ```bash
    bmc upgrade update --protocol <PROTOCOL> --ip <BMC_IP> --port <BMC_PORT> --user <USERNAME> --password <PASSWORD> --core <NODE_NAME> --platform <PLATFORM> --image <IMAGE_NAME>
    ```

    ### 创建批量升级任务 [step]

    使用半角逗号分隔多个节点名。只有所有节点均与固件和平台匹配时，才能执行批量升级。

    ```bash
    bmc upgrade update --protocol <PROTOCOL> --ip <BMC_IP> --port <BMC_PORT> --user <USERNAME> --password <PASSWORD> --core <NODE_1>,<NODE_2> --platform <PLATFORM> --image <IMAGE_NAME>
    ```

    ### 查询升级进度 [step]

    该命令返回全部节点的任务状态、进度和消息。创建任务后应持续查询，直到目标节点进入 **Completed** 或 **Failed**。

    ```bash
    bmc upgrade progress --protocol <PROTOCOL> --ip <BMC_IP> --port <BMC_PORT> --user <USERNAME> --password <PASSWORD>
    ```

    ### Demo [step]

    以地址为 `172.16.100.173`、端口为 `443`、账号为 `admin` 的实机为例：

    ```bash
    # 查看实机固件列表
    bmc upgrade image --protocol http --ip 172.16.100.173 --port 443 --user admin --password admin
    # 升级 Rockchip 节点
    bmc upgrade update --protocol http --ip 172.16.100.173 --port 443 --user admin --password admin --core sub01 --platform Rockchip --image CS-B1-3576-JD4-SUB-MINIMAL_Debian_debug_250428.img
    # 查询实机升级结果
    bmc upgrade progress --protocol http --ip 172.16.100.173 --port 443 --user admin --password admin
    ```

    <Callout title="凭据安全" type="warn">
      命令行参数可能被 Shell 历史记录或进程列表保存。请避免在共享环境中直接使用真实密码，并根据部署环境采用安全的凭据传递方式。
    </Callout>
  </CodeBlockTab>

  <CodeBlockTab value="API">
    <Callout title="升级操作风险" type="warn">
      调用 `UpdateFwService.SimpleUpdate` 会立即创建真实升级任务。调用前必须确认固件、平台和目标节点匹配，并建议先在单个非关键节点上验证。
    </Callout>

    | 操作 | 方法 | URI |
    | --- | --- | --- |
    | 查询升级动作参数 | GET | `/redfish/v1/UpdateFwService/Actions/UpdateFwServiceActionInfo` |
    | 查看已上传固件 | GET | `/redfish/v1/UpdateFwService/LocalFirmwareLists` |
    | 删除已上传固件 | DELETE | `/redfish/v1/UpdateFwService/LocalFirmwareLists/{FirmwareID}` |
    | 上传固件分片 | POST | `/redfish/v1/UpdateFwService/Actions/UpdateFwService.UploadFirmwareChunck` |
    | 合并固件分片 | POST | `/redfish/v1/UpdateFwService/Actions/UpdateFwService.FirmwareChunckMerage` |
    | 创建子节点升级任务 | POST | `/redfish/v1/UpdateFwService/Actions/UpdateFwService.SimpleUpdate` |
    | 查询全部节点升级进度 | GET | `/redfish/v1/UpdateFwService/UpdateFwServiceTasksLists` |
    | 查询单节点升级详情 | GET | `/redfish/v1/UpdateFwService/{ComputerSystemId}/Actions/Oem/Firefly/UpdateFwService.ServiceInfo` |

    <Callout title="提示" type="info">
      关于接口认证、权限、完整请求定义和错误码的详细说明，请查看 Redfish API 文档。
    </Callout>
  </CodeBlockTab>
</CodeBlockTabs>


## 常见问题 FAQ

### 1.Select Upgrade Firmware 中没有目标固件

确认固件是否已经上传并被页面识别。没有可用固件时，单击 **Upload Firmware** 上传来源可信且与设备匹配的镜像，然后重新打开升级窗口。

### 2.任务长时间停留在 Pending 或 Preparing

检查是否存在其他任务、目标设备是否可以进入升级模式、管理网络是否稳定，并查看 **Message** 和相关日志。原因未确认前不要重复提交。

## 固件预处理

`firmware-kits` 用于在正式固件升级前，对 BM1684 和 RK3588 固件包进行预处理。它可以按指定流程解包原始固件、调整 ROOTFS/DATA/CUSTOM 等分区大小、挂载 rootfs 供人工修改系统内容，并在修改完成后重新打包生成可用于交付或升级的固件包。

该工具适用于需要在升级前定制固件内容的场景，例如扩容分区、预置文件、调整 rootfs 配置、生成 TFTP 或 SD 卡升级包。普通固件升级仍应优先使用本页面前文的升级管理功能；只有在固件需要先被修改或重新打包时，才使用 `firmware-kits`。

下面说明如何使用 `firmware-kits` 对 BM1684 和 RK3588 固件进行解包、修改和重新打包。请严格按步骤操作。

所有命令都在 `firmware-kits` 目录下执行。

### 1. 准备工作

#### 1.1 操作系统要求

推荐使用 Ubuntu 20.04/22.04 x86_64 主机。

#### 1.2 安装依赖

```bash
sudo apt-get update
sudo apt-get install -y u-boot-tools qemu-user-static p7zip-full unzip e2fsprogs
```

#### 1.3 检查工具包目录

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

### 2. 基本命令

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

### 3. 重要注意事项

1. 不要在流程暂停时删除 `out/`、`.firmware-kits_state.json` 或 `tools/.env`。
2. 不要关闭已经挂载 rootfs 后的主机，除非已经确认卸载完成。
3. 每次只处理一个固件流程，不要同时开多个终端跑多个 `firmware-kits run/resume`。
4. 输出目录是 `out/`。确认不需要产物后，可以用 `sudo rm -rf out/` 清理空间。
5. 如果流程提示存在中断记录，优先执行 `sudo ./firmware-kits resume`，不要直接重新 `run`。

### 4. BM1684 固件操作

#### 4.1 输入文件

BM1684 使用 zip 固件包，例如：

```text
Public-All-1684-Ubuntu2004-Sdk2309LTS_SP5-Build20260522.zip
```

把固件文件放到 `firmware-kits` 目录下后执行后续命令。

#### 4.2 选择输出类型和 flow

BM1684 有多个 flow。不同 flow 的区别主要是输出类型、是否处理 BOOT/DATA 分区、是否处理 CUSTOM 分区。

| flow 文件 | 适用场景 | 输出产物 | 暂停点 | 关键参数 |
| ---- | ---- | ---- | ---- | ---- |
| `flow/bm1684.yaml` | 基础 SD 卡升级包；只处理 ROOTFS | `out/bm1684/ubuntu20/update/sdcard-YYYY-MM-DD.zip` | ROOTFS 分区大小、rootfs 内容修改 | 打包参数 `sdcard` |
| `flow/bm1684_tftp.yaml` | 基础 TFTP 升级包；只处理 ROOTFS | `out/bm1684/ubuntu20/update/tftp-YYYY-MM-DD.zip` | ROOTFS 分区大小、rootfs 内容修改 | 打包参数 `tftp` |
| `flow/bm1684_sdcard_data.yaml` | SD 卡升级包；处理 ROOTFS、BOOT、DATA | `out/bm1684/ubuntu20/update/sdcard-YYYY-MM-DD.zip` | ROOTFS 分区大小、DATA 分区大小、rootfs 内容修改 | 打包参数 `sdcard`；BOOT 自动处理 |
| `flow/bm1684_tftp_data.yaml` | TFTP 升级包；处理 ROOTFS、BOOT、DATA | `out/bm1684/ubuntu20/update/tftp-YYYY-MM-DD.zip` | ROOTFS 分区大小、DATA 分区大小、rootfs 内容修改 | 打包参数 `tftp`；BOOT 自动处理 |
| `flow/bm1684_custom_overlay.yaml` | TFTP 升级包；增加或扩容 CUSTOM，并将 CUSTOM/DATA bind 到 rootfs | `out/bm1684/ubuntu20/update/tftp-YYYY-MM-DD.zip` | ROOTFS 分区大小、CUSTOM 分区大小、DATA 分区大小、rootfs 内容修改 | CUSTOM bind 参数中默认大小为 `20`；打包参数 `tftp` |
| `flow/bm1684_custom_ro.yaml` | TFTP 升级包；CUSTOM 按只读方案处理，同时处理 BOOT/DATA | `out/bm1684/ubuntu20/update/tftp-YYYY-MM-DD.zip` | ROOTFS 分区大小、DATA 分区大小、rootfs 内容修改 | 打包参数 `tftp custom_ro`；CUSTOM/BOOT 自动处理 |

说明：

1. 输入固件都是 BM1684 zip 包，例如 `Public-All-1684-Ubuntu2004-Sdk2309LTS_SP5-Build20260522.zip`。
2. `ROOTFS 分区大小` 暂停点可以查看或扩容 ROOTFS。
3. `DATA 分区大小` 暂停点可以查看或扩容 DATA。只有带 `_data` 或 custom 的 flow 才会出现。
4. `CUSTOM 分区大小` 暂停点只在 `bm1684_custom_overlay.yaml` 中出现。
5. 如果不需要修改某个暂停点的内容，直接执行 `sudo ./firmware-kits resume` 即可。

常用命令示例：

生成 TFTP 升级包，处理 ROOTFS/BOOT/DATA：

```bash
sudo ./firmware-kits run -l flow/bm1684_tftp_data.yaml -f ./Public-All-1684-Ubuntu2004-Sdk2309LTS_SP5-Build20260522.zip
```

生成 SD 卡升级包，处理 ROOTFS/BOOT/DATA：

```bash
sudo ./firmware-kits run -l flow/bm1684_sdcard_data.yaml -f ./Public-All-1684-Ubuntu2004-Sdk2309LTS_SP5-Build20260522.zip
```

只处理 ROOTFS 并生成 TFTP 升级包：

```bash
sudo ./firmware-kits run -l flow/bm1684_tftp.yaml -f ./Public-All-1684-Ubuntu2004-Sdk2309LTS_SP5-Build20260522.zip
```

只处理 ROOTFS 并生成 SD 卡升级包：

```bash
sudo ./firmware-kits run -l flow/bm1684.yaml -f ./Public-All-1684-Ubuntu2004-Sdk2309LTS_SP5-Build20260522.zip
```

如果没有特别要求，按交付人员指定的 flow 执行。

#### 4.3 第一次暂停：修改 ROOTFS 分区大小

程序会显示类似信息：

```text
[Step 3/13] 修改根文件系统大小
固件处理已暂停，客户完成上述手动操作后，可输入 `sudo ./firmware-kits resume` 进行继续处理
```

查看 ROOTFS 当前大小：

```bash
sudo ./tools/chroot-p-bm1684.sh -p ROOTFS -r
```

如果需要扩容，例如扩容到 4000 MB：

```bash
sudo ./tools/chroot-p-bm1684.sh -p ROOTFS -w 4000
```

如果不需要修改大小，可以直接继续：

```bash
sudo ./firmware-kits resume
```

如果已经修改完大小，也执行同一个继续命令：

```bash
sudo ./firmware-kits resume
```

#### 4.4 第二次暂停：修改 DATA 分区大小

程序会显示类似信息：

```text
[Step 7/13] 修改 DATA 分区的大小
固件处理已暂停，客户完成上述手动操作后，可输入 `sudo ./firmware-kits resume` 进行继续处理
```

查看 DATA 当前大小：

```bash
sudo ./tools/chroot-p-bm1684.sh -p DATA -r
```

如果需要扩容，例如扩容到 6000 MB：

```bash
sudo ./tools/chroot-p-bm1684.sh -p DATA -w 6000
```

如果不需要修改大小，可以直接继续：

```bash
sudo ./firmware-kits resume
```

#### 4.5 第三次暂停：修改 rootfs 内容

程序会显示类似信息：

```text
[Step 9/13] 修改根文件系统内容
固件处理已暂停，客户完成上述手动操作后，可输入 `sudo ./firmware-kits resume` 进行继续处理
```

在 rootfs 内执行命令：

```bash
sudo ./tools/chroot-e.sh 'apt-get update'
```

安装软件包示例：

```bash
sudo ./tools/chroot-e.sh 'apt-get install -y figlet'
```

复制本地文件到 rootfs，例如把当前目录的 `a.txt` 复制到目标系统的 `/home/`：

```bash
sudo ./tools/chroot-c.sh -s ./a.txt -d /home/
```

说明：

| 操作 | 命令 |
| ---- | ---- |
| 执行一条 rootfs 内命令 | `sudo ./tools/chroot-e.sh 'COMMAND'` |
| 复制文件或目录到 rootfs | `sudo ./tools/chroot-c.sh -s <本地路径> -d <rootfs内目标路径>` |

修改完成后继续：

```bash
sudo ./firmware-kits resume
```

之后程序会自动卸载 DATA、BOOT、ROOTFS，并重新打包固件。

#### 4.6 BM1684 输出文件

TFTP flow 输出：

```text
out/bm1684/ubuntu20/update/tftp-YYYY-MM-DD.zip
```

SD 卡 flow 输出：

```text
out/bm1684/ubuntu20/update/sdcard-YYYY-MM-DD.zip
```

实际验证示例：

```text
out/bm1684/ubuntu20/update/tftp-2026-07-28.zip
```

输出文件可能属于 root 用户。如果复制或删除时权限不足，请使用 `sudo`。

### 5. RK3588 固件操作

#### 5.1 输入文件

RK3588 支持直接输入 `.7z`、`.zip` 或 `.img`。例如：

```text
CS-B1-N10-SUB-3588JD4_Debian12-Xfce-r1275_debug_260710.7z
```

工具会自动从 `.7z/.zip` 中解出 `.img` 后继续处理。

#### 5.2 开始处理

```bash
sudo ./firmware-kits run -l flow/rk3588.yaml -f ./CS-B1-N10-SUB-3588JD4_Debian12-Xfce-r1275_debug_260710.7z
```

#### 5.3 第一次暂停：修改 ROOTFS 分区大小

程序会显示类似信息：

```text
[Step 3/7] 修改根文件系统大小
固件处理已暂停，客户完成上述手动操作后，可输入 `sudo ./firmware-kits resume` 进行继续处理
```

查看 rootfs 当前大小：

```bash
sudo ./tools/chroot-p-rk3588.sh -p rootfs -r
```

如果需要扩容，例如扩容到 4000 MB：

```bash
sudo ./tools/chroot-p-rk3588.sh -p rootfs -w 4000
```

如果不需要修改大小，可以直接继续：

```bash
sudo ./firmware-kits resume
```

#### 5.4 第二次暂停：修改 rootfs 内容

程序会显示类似信息：

```text
[Step 5/7] 修改根文件系统内容
固件处理已暂停，客户完成上述手动操作后，可输入 `sudo ./firmware-kits resume` 进行继续处理
```

在 rootfs 内执行命令：

```bash
sudo ./tools/chroot-e.sh 'apt-get update'
```

复制文件到 rootfs：

```bash
sudo ./tools/chroot-c.sh -s ./a.txt -d /home/
```

修改完成后继续：

```bash
sudo ./firmware-kits resume
```

之后程序会自动卸载 rootfs，并重新打包 RK3588 固件。

#### 5.5 RK3588 输出文件

输出文件位置：

```text
out/rk3588/ubuntu20/update/rockdev/rk3588-YYYY-MM-DD.img
```

实际验证示例：

```text
out/rk3588/ubuntu20/update/rockdev/rk3588-2026-07-28.img
```

输出文件可能属于 root 用户。如果复制或删除时权限不足，请使用 `sudo`。

### 6. 中断恢复

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

### 7. 执行记录

随包提供两份实际执行记录，可下载后查看完整操作过程：

- [下载 BM1684 实际执行记录](/docs-assets/server-docs/aBMC_img/v3.0/firmware-kits/bm1684-run-2026-07-28.zip)
- [下载 RK3588 实际执行记录](/docs-assets/server-docs/aBMC_img/v3.0/firmware-kits/rk3588-run-2026-07-28.zip)

日志中只保留客户关心的固件处理命令、终端输出、暂停点操作、rootfs 内执行命令演示、拷贝文件演示、最终产物路径和挂载检查结果。

### 8. firmware-kits 常见问题

#### 8.1 提示权限不足

固件处理涉及挂载、chroot 和 root-owned 文件。请确认命令使用了 `sudo`。

如果删除输出目录失败，执行：

```bash
sudo rm -rf out/
```

#### 8.2 不确定当前该执行什么

如果流程暂停，终端通常会提示下一步命令。一般只需要：

1. 按提示查看或修改分区大小，或修改 rootfs 内容。
2. 完成后执行 `sudo ./firmware-kits resume`。

#### 8.3 不需要修改分区大小怎么办

在分区大小暂停点，如果不需要修改，直接执行：

```bash
sudo ./firmware-kits resume
```

#### 8.4 不需要修改 rootfs 内容怎么办

在 rootfs 内容修改暂停点，如果不需要修改，直接执行：

```bash
sudo ./firmware-kits resume
```

#### 8.5 apt 命令出现 locale 或 /dev/pts 警告

部分 rootfs 环境可能显示 locale 或 `/dev/pts` 相关警告。只要命令最终执行成功，一般不影响固件制作。

#### 8.6 如何确认没有残留挂载

正常流程完成后通常不会残留挂载。需要检查时执行：

```bash
mount | grep firmware-kits/out
```

没有输出表示没有残留挂载。

如果有输出，请联系交付人员处理，不要随意删除正在挂载的目录。
