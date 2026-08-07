# 磁盘管理

## 简介

磁盘管理（Disk Management）是aBMC面向服务器存储设备设计的可视化管理系统。该系统由磁盘状态监测、物理磁盘管理和软件 RAID 管理三大模块组成。
1. 状态监测模块：负责展示硬盘槽位、容量、介质类型、健康状态和型号等信息。
2. 物理磁盘管理模块：负责管理磁盘分区、文件系统、卷标和挂载，并提供 Udev 与 SMART 信息查询能力。
3. 软件 RAID 管理模块：负责创建 RAID 阵列，并提供添加磁盘、更换硬盘、扩容、模式调整和删除等维护能力。

## 开发愿景

1. 为用户提供集中、可视化的磁盘状态和存储拓扑，帮助管理员快速识别硬盘异常、分区差异和 RAID 风险。
2. 通过统一的分区、文件系统、挂载和 RAID 操作入口，降低服务器现场存储运维的操作门槛和误操作概率。

# 功能使用

## 了解磁盘管理

<CodeBlockTabs defaultValue="WEB">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="WEB">WEB</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="WEB">
    ### 功能范围 [step]

    | 功能模块 | 功能说明 |
    | --- | --- |
    | 硬盘状态查看 | 查看槽位、容量、介质类型、健康状态和型号 |
    | 磁盘与分区查看 | 以图形分段方式展示磁盘、分区和未分配空间 |
    | 创建分区 | 在未分配空间上创建新分区，可设置大小、偏移、对齐、分区标签和文件系统 |
    | 格式化分区 | 将磁盘或分区格式化为 ext4、exfat、ntfs、ext2、ext3、fat32 等文件系统 |
    | 修改卷标 | 修改已有文件系统分区的 Label |
    | 挂载/取消挂载 | 将分区挂载到指定目录，支持临时挂载和持久化挂载 |
    | 信息查看 | 查看 Udev 属性和 SMART 诊断信息 |
    | 逻辑视图 | 创建 RAID 阵列，并维护已有 RAID 的加盘、换盘、扩容、模式调整和删除 |

    <Callout title="危险操作提示" type="warn">
      创建分区、删除分区、格式化分区、删除 RAID、RAID 扩容和 RAID 模式调整都可能影响磁盘数据。操作前请确认目标磁盘、分区或 RAID 阵列无业务占用，并已完成必要的数据备份。
    </Callout>

    ### 使用前准备 [step]

    1. 已使用具备系统配置权限的账号登录 aBMC。
    2. 确认目标硬盘、分区或 RAID 阵列没有正在运行的业务写入。
    3. 执行格式化、删除、RAID 变更前，确认数据已备份。
    4. 执行挂载前，确认挂载目录为绝对路径，且不是根目录 `/`。
    5. 执行 RAID 创建或扩容前，确认存在可用的空闲硬盘。

    ### 界面术语 [step]

    | 中文 | English |
    | --- | --- |
    | 磁盘管理 | Disk |
    | 状态视图 | Status View |
    | 物理视图 | Physical View |
    | 逻辑视图 | Logical View |
    | 创建分区 | Create Partition |
    | 格式化分区 | Format Partition |
    | 修改卷标 | Modify Label |
    | 删除分区 | Delete Partition |
    | 挂载 | Mount |
    | 取消挂载 | Unmount |
    | 临时挂载 | Temporary Mount |
    | 持久化挂载 | Persistent Mount |
    | 创建RAID阵列 | Create RAID Array |
    | 添加磁盘 | Add Disk |
    | 更换硬盘 | Replace Disk |
    | 添加硬盘扩容 | Expand Disk |
    | 修改RAID模式 | Modify RAID Mode |

    ### 打开磁盘管理页面 [step]

    1. 在左侧主导航中选择 **System**。
    2. 在系统二级导航中选择 **Disk**。
    3. 在页面顶部切换 **Status View**、**Physical View** 或 **Logical View**。

    ![进入磁盘管理状态视图](../../../img/aBMC_img/v3.0/diskManagement_img/01-enter-disk-manager-en-steps-v9.png)

  </CodeBlockTab>
</CodeBlockTabs>

## 查看磁盘状态

<CodeBlockTabs defaultValue="WEB">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="WEB">WEB</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="WEB">
    ### 查看槽位状态 [step]

    状态视图用于查看机箱硬盘槽位状态。页面会按槽位显示硬盘容量、介质类型、健康状态和型号。后端通过 `Drives` 集合读取槽位列表，再逐个读取单盘详情。

    1. 单击 **Status View**。
    2. 查看槽位卡片，确认当前识别到的硬盘槽位。
    3. 查看 **Total Capacity** 和 **Media Type**，确认容量和介质类型。
    4. 查看 **Health Status** 和 **Model**，判断硬盘健康状态并定位具体设备。

    ![查看磁盘状态](../../../img/aBMC_img/v3.0/diskManagement_img/00-status-view-en-steps-v2.png)

    <Callout title="示例环境说明" type="info">
      当前示例环境中，6 个槽位均显示为 **Abnormal**，容量显示为 `-`。这表示后端已经返回槽位和设备标识，但没有取得有效容量，并且健康状态不是正常值。实际现场应结合硬盘连接、SMART 信息和系统日志继续排查。
    </Callout>

    ### 状态字段说明 [step]

    | 字段 | 说明 |
    | --- | --- |
    | 总容量 | 当前槽位硬盘容量 |
    | 硬盘类型 | HDD、SSD、NVMe、MMC 等介质类型 |
    | 健康状态 | 正常或异常 |
    | 型号 | 硬盘型号或设备唯一标识 |

    ### 健康状态说明 [step]

    健康状态由后端 `DriveBase.Status.Health` 转换：

    | 后端值 | 页面显示 | 说明 |
    | --- | --- | --- |
    | `true` | Normal | 硬盘健康状态正常 |
    | `warning` | Warning | 硬盘存在风险，页面可能通过提示展示 `past_threshold`、`error_log`、`self_test_log` 或 `smartctl_warning` 等原因 |
    | 其他值或空值 | Abnormal | 硬盘健康状态异常，常见于 SMART 读取失败、硬盘异常或后端无法确认健康状态 |

    ### 槽位状态说明 [step]

    槽位状态由后端 `HDDSlot.LastSent` 表示。当前页面主要展示健康状态，前端仍保留了槽位状态和 LED 定义逻辑，可按以下含义理解：

    | LastSent | 槽位状态 | 说明 |
    | --- | --- | --- |
    | `0` | Disk Present | 硬盘在位，已被 BMC 识别 |
    | `1` | Disk Read/Write | 硬盘正在读写 |
    | `2` | Rebuild Slave | 硬盘正在参与 RAID 重构 |
    | `3` | Located by BMC | BMC 正在定位该硬盘 |
    | `4` | Disk Removed from RAID | RAID 组中的硬盘被拔出或热插拔异常 |
    | `5` | Disk Fault | 系统识别到硬盘故障 |
    | `6` | Disk Absent | 槽位未插盘或硬盘不在位 |
    | `7` | Init State | 硬盘处于初始化状态 |

    ### 槽位 LED 说明 [step]

    硬盘槽位 LED 与状态关系如下：

    | 槽位状态 | 绿色灯 | 红色灯 | 蓝色灯 |
    | --- | --- | --- | --- |
    | Located by BMC | 亮 | 灭 | 闪烁 |
    | Disk Present | 亮 | 灭 | 灭 |
    | Disk Read/Write | 闪烁 | 灭 | 灭 |
    | Rebuild Slave | 闪烁 | 闪烁 | 灭 |
    | Disk Absent | 灭 | 灭 | 灭 |
    | Disk Fault | 亮 | 亮 | 灭 |
    | Disk Removed from RAID | 灭 | 亮 | 灭 |

    如果槽位未插盘或后端没有返回数据，页面会显示空槽位或无数据状态。

  </CodeBlockTab>
</CodeBlockTabs>

## 管理物理磁盘

<CodeBlockTabs defaultValue="WEB">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="WEB">WEB</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="WEB">

    ### 查看物理视图 [step]

    物理视图是磁盘管理的主要操作页面。左侧展示磁盘和分区图形，表格展示分区字段，右侧展示当前选中对象的操作按钮和详细属性。

    1. 单击 **Physical View**。
    2. 在左侧磁盘区域选择整盘、分区或未分配空间。
    3. 在右侧操作区选择当前对象支持的操作。
    4. 在右侧属性区查看内核设备名、设备节点、型号、序列号、分区表、文件系统、挂载点等信息。

    ![物理视图总览](../../../img/aBMC_img/v3.0/diskManagement_img/02-physical-view-overview-en-steps-v9.png)

    | 表格字段 | 说明 |
    | --- | --- |
    | 分区 | 磁盘、分区或未分配空间名称 |
    | Partlabel | 分区标签 |
    | Label | 文件系统卷标 |
    | 文件系统 | 当前文件系统类型 |
    | 挂载点 | 当前挂载目录 |
    | 大小 | 磁盘或分区容量 |
    | 已用空间 | 文件系统已用比例或用量 |
    | UUID | 文件系统 UUID |

    ### 创建分区 [step]

    创建分区只在 **Unallocated** 空间上可用。前端先读取 `NewPartitionActionInfo` 获取可用大小、对齐方式、偏移范围和文件系统类型，再提交 `NewPartition` 创建分区。

    1. 进入 **Physical View**。
    2. 单击右侧 **Create Partition**。
    3. 选择目标磁盘上的 **Unallocated** 空间。
    4. 在弹窗中设置分区大小、分区标签、字节对齐、偏移量、文件系统格式和文件系统标签。
    5. 单击 **Confirm** 创建分区。

    ![创建分区](../../../img/aBMC_img/v3.0/diskManagement_img/03-create-partition-en-steps-v9.png)

    | 参数 | 说明 |
    | --- | --- |
    | 分区大小(MiB) | 新分区容量，必须在弹窗显示的最小值和最大值范围内 |
    | 分区标签 | 分区表中的 Partlabel，可选 |
    | 字节对齐(B) | 分区对齐参数，通常使用后端返回的默认值 |
    | 偏移量(MiB) | 从未分配空间起点开始的偏移 |
    | 文件系统格式 | 可选择 ext4、exfat、ntfs、ext2、ext3、fat32 或不创建文件系统 |
    | 文件系统标签 | 创建文件系统时写入的 Label |

    ### 格式化分区 [step]

    格式化用于为磁盘或分区创建新的文件系统。后端会拒绝已挂载对象的格式化请求，因此操作前必须先取消挂载。

    1. 进入 **Physical View**。
    2. 选择需要格式化的磁盘或分区。
    3. 单击右侧 **Format Partition**。
    4. 在弹窗中选择文件系统类型。
    5. 单击 **Confirm**，并在警告确认框中再次确认。

    ![格式化分区](../../../img/aBMC_img/v3.0/diskManagement_img/04-format-partition-en-steps-v9.png)

    <Callout title="格式化会清空文件系统数据" type="warn">
      格式化会重新创建文件系统，原分区中的数据将不可用。已挂载分区需要先执行 **Unmount**，否则后端会拒绝格式化。
    </Callout>

    ### 修改卷标 [step]

    修改卷标用于更新已有文件系统的 Label。该按钮仅在当前对象存在文件系统时可用。

    1. 在 **Physical View** 中选择目标分区。
    2. 单击右侧 **Modify Label**。
    3. 在弹窗中输入新的分区卷标。
    4. 单击 **Confirm**，并在确认框中确认修改。

    ![修改卷标](../../../img/aBMC_img/v3.0/diskManagement_img/05-modify-label-en-steps-v9.png)

    ### 挂载或取消挂载分区 [step]

    挂载用于将已有文件系统的分区挂载到指定目录。后端要求挂载目录必须是绝对路径，且不能是根目录 `/`。挂载方式包括：

    | 挂载方式 | 说明 |
    | --- | --- |
    | 临时挂载 | 当前系统运行期间生效，重启后需要重新挂载 |
    | 持久化挂载 | 后端会创建由 aBMC 管理的 systemd mount 单元，重启后自动挂载 |

    1. 进入 **Physical View**。
    2. 选择有文件系统的分区。
    3. 单击右侧 **Mount**。
    4. 输入挂载目录，并选择 **Temporary Mount** 或 **Persistent Mount**。
    5. 单击 **Confirm**。

    ![挂载分区](../../../img/aBMC_img/v3.0/diskManagement_img/06-mount-partition-en-steps-v9.png)

    取消挂载时选择已挂载分区，单击右侧 **Unmount**，在弹窗中确认要取消挂载的目录后提交。已挂载分区会启用取消挂载按钮，并禁用部分写入类操作。

    ### 查看 Udev 信息 [step]

    Udev 信息用于排查设备路径、总线、厂商、序列号和系统识别属性。

    1. 在 **Physical View** 中选择目标磁盘或分区。
    2. 单击右侧 **udevInfo**。
    3. 在弹窗中查看 JSON 格式的 Udev 属性。

    ![查看 Udev 信息](../../../img/aBMC_img/v3.0/diskManagement_img/07-udev-info-en-steps-v9.png)

    ### 查看 SMART 信息 [step]

    SMART 信息用于查看硬盘自检、错误计数、健康状态和设备诊断输出。前端会调用 `SmartInfo` 接口，后端读取对应块设备的 SMART 结果并展示。

    1. 在 **Physical View** 中选择目标硬盘或其分区。
    2. 单击右侧 **Smart Info**。
    3. 在弹窗中查看 SMART 输出。

    ![查看 SMART 信息](../../../img/aBMC_img/v3.0/diskManagement_img/08-smart-info-en-steps-v9.png)

  </CodeBlockTab>
</CodeBlockTabs>

## 管理 RAID 阵列

<CodeBlockTabs defaultValue="WEB">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="WEB">WEB</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="WEB">

    ### 了解逻辑视图 [step]

    逻辑视图用于创建和维护软件 RAID 阵列。页面的数据来自 Mdraid Redfish 资源，前端根据后端返回的可用硬盘、RAID Level 能力和 `RaidOps` 操作使能，决定哪些硬盘可选择、哪些 RAID 模式可创建，以及已有阵列可以执行哪些维护操作。

    RAID 操作属于高风险存储操作。创建、删除、扩容、换盘和模式调整都会修改磁盘元数据或触发阵列重构。执行前应确认目标硬盘无业务数据，必要时先完成数据备份。

    ### 创建 RAID 阵列 [step]

    创建 RAID 阵列时，页面左侧显示可用于组建阵列的硬盘，右侧显示当前硬盘数量可支持的 RAID 存储模式。选择硬盘后，页面会重新计算可用容量、理论读写性能和数据保护能力。

    1. 进入 **Logical View**。
    2. 单击右上角 **Create RAID Array**。
    3. 在弹窗中选择可用硬盘。
    4. 在右侧选择 RAID 存储模式。
    5. 单击 **Next** 进入下一步。

    ![创建 RAID 阵列](../../../img/aBMC_img/v3.0/diskManagement_img/09-create-raid-array-en-steps-v9.png)

    创建页面的关键字段说明如下：

    | 字段 | 说明 |
    | --- | --- |
    | Select Disks | 可用于创建 RAID 的硬盘列表。不可用硬盘不会作为可选项参与创建。 |
    | Select Storage Mode | 根据已选硬盘数量和后端能力计算出的 RAID 模式列表。 |
    | Available Capacity | 创建后预计可用于存储业务数据的容量。 |
    | Theoretical Performance | 后端根据 RAID Level 返回的理论读写性能参考值。 |
    | Data Protection | 当前 RAID 模式的数据保护能力，例如 None、Medium、Max。 |

    常见 RAID 模式要求如下：

    | RAID 模式 | 最少硬盘数 | 说明 |
    | --- | --- | --- |
    | Linear | 2 | 多盘容量串联，无冗余；任意成员盘异常都可能影响整体数据。 |
    | RAID 0 | 2 | 条带化读写，性能高，但无冗余保护。 |
    | RAID 1 | 2 | 镜像保护，数据安全性高，可用容量约等于最小成员盘容量。 |
    | RAID 4 | 3 | 独立校验盘，允许单盘故障，写性能受校验盘影响。 |
    | RAID 5 | 3 | 分布式校验，允许单盘故障，容量利用率高于 RAID 1。 |
    | RAID 6 | 4 | 双校验，允许双盘故障，写入开销更高。 |
    | RAID 10 | 4 且通常为偶数盘 | 镜像加条带，兼顾性能和冗余。 |

    <Callout title="当前设备无可用硬盘时" type="info">
      如果弹窗中没有可选硬盘，或目标 RAID 模式不可选，表示当前机器没有满足条件的空闲硬盘。需要释放磁盘、清除旧 RAID 元数据，或插入新的可用硬盘后再创建。
    </Callout>

    <Callout title="容量和重构时间说明" type="info">
      RAID 创建完成后可能进入 Resync 或 Recovering 状态。阵列容量越大，初始化或重构时间越长。重构期间部分维护操作会被禁用，建议等待状态恢复为 Clean 后再继续操作。
    </Callout>

    ### 查看 RAID 阵列卡片 [step]

    RAID 创建完成后，逻辑视图会以卡片形式显示阵列。卡片用于查看阵列基础信息、成员盘和当前状态。

    1. 进入 **Logical View**。
    2. 查看 RAID 名称、状态、RAID Level 和容量。
    3. 查看 **Used Disk** 成员盘列表。
    4. 单击卡片右上角 **More** 图标打开维护菜单。

    ![查看 RAID 阵列卡片](../../../img/aBMC_img/v3.0/diskManagement_img/10-raid-card-overview-en-steps-v9.png)

    RAID 卡片字段说明如下：

    | 字段 | 说明 |
    | --- | --- |
    | RAID 名称 | 创建阵列时写入的 MdName，用于区分不同阵列。 |
    | 状态 | 当前阵列状态，例如 Clean、Degraded、Resync、Recovering、Failed。 |
    | RAID Level | 当前阵列模式，例如 RAID1、RAID5、RAID10。 |
    | Capacity | 当前阵列可用容量。 |
    | Device Node | 系统中的 md 设备节点，例如 `/dev/md0`。 |
    | Creation Time / Update Time | 阵列创建时间和最近更新时间。 |
    | Used Disk | 当前参与阵列的成员盘。 |

    ### 使用 RAID 操作菜单 [step]

    RAID 卡片右上角的操作菜单由后端 `RaidOps` 字段控制。页面会根据阵列状态启用或禁用添加磁盘、更换硬盘、扩容、修改 RAID 模式、删除成员盘和删除阵列等操作。

    1. 单击 RAID 卡片右上角 **More** 图标。
    2. 选择 **Add Disk** 添加热备盘。
    3. 选择 **Replace Disk** 更换成员盘。
    4. 选择 **Expand with Disks** 添加硬盘扩容。
    5. 选择 **Modify RAID Mode** 修改 RAID Level。
    6. 选择 **Delete** 删除 RAID 阵列。

    ![查看 RAID 操作菜单](../../../img/aBMC_img/v3.0/diskManagement_img/11-raid-operation-menu-en-steps-v9.png)

    不同状态下的常见可用操作如下：

    | RAID 状态 | 可用操作说明 |
    | --- | --- |
    | Clean | 阵列正常。非 RAID 0/Linear 通常可执行加盘、换盘、扩容、改模式、删除成员盘和删除阵列。 |
    | Degraded | 阵列降级。通常可删除阵列或添加热备盘，不建议继续删除成员盘。 |
    | Resync / Recovering | 阵列正在同步或恢复。通常仅保留删除阵列入口，其他维护操作需等待完成。 |
    | Reshaping | 阵列正在扩容或变更模式。除删除阵列外，其他维护操作通常会被限制。 |
    | Failed / Inactive | 阵列异常或未激活。应先确认硬盘状态和数据风险，再执行后续处理。 |

    ### 添加磁盘 [step]

    添加磁盘用于向已有 RAID 阵列加入热备盘。热备盘加入后通常显示为 spare，不会立即增加业务可用容量。当阵列中的活动成员盘故障或被移除时，热备盘可参与自动恢复。

    1. 打开 **Add Disk** 弹窗。
    2. 确认当前 RAID 名称、已用成员盘、容量和存储模式。
    3. 选择要添加的可用硬盘。
    4. 单击 **Confirm** 提交。

    ![添加磁盘](../../../img/aBMC_img/v3.0/diskManagement_img/12-add-disk-en-steps-v9.png)

    添加磁盘后，建议返回 RAID 卡片查看成员盘状态。如果新盘显示为 `spare`，表示该磁盘作为热备盘存在；如果阵列处于降级恢复流程，则可能进入重构状态。

    ### 更换硬盘 [step]

    更换硬盘用于用新的可用硬盘替换 RAID 阵列中的旧成员盘。该操作分为两步：先选择旧盘，再选择新盘。提交后后端调用 `ReplaceDisk`，阵列会进入重构流程。

    1. 打开 **Replace Disk** 弹窗。
    2. 确认当前 RAID 信息。
    3. 选择要移除的旧成员盘。
    4. 单击 **Next (1/2)** 进入下一步。

    ![选择旧成员盘](../../../img/aBMC_img/v3.0/diskManagement_img/13-replace-disk-select-old-en-steps-v9.png)

    1. 在第二步中选择用于替换的新硬盘。
    2. 确认新硬盘与旧成员盘一一对应。
    3. 单击 **Confirm** 提交更换。

    ![选择新硬盘](../../../img/aBMC_img/v3.0/diskManagement_img/14-replace-disk-select-new-en-steps-v9.png)

    更换过程中 RAID 容量通常不会立即变化，重构时间与成员盘容量相关。更换完成后，旧盘可能仍以 fault、removed 或类似状态保留在阵列信息中，可根据页面提示再执行删除成员盘操作。

    ### 添加硬盘扩容 [step]

    扩容用于向 RAID 阵列加入新硬盘并扩大阵列容量。页面会显示当前 RAID 信息、可选硬盘和预计容量变化。提交后后端执行 `Reshape/Exec`，阵列进入 Reshaping 或 Resync 状态。

    1. 打开 **Expand with Disks** 弹窗。
    2. 确认当前 RAID 信息。
    3. 选择要加入阵列的可用硬盘。
    4. 查看预计容量变化。
    5. 单击 **Confirm** 提交扩容。

    ![添加硬盘扩容](../../../img/aBMC_img/v3.0/diskManagement_img/15-expand-disk-en-steps-v9.png)

    扩容规则与 RAID Level 有关。例如 RAID 10 扩容时，扩容后的活动盘数量需要满足偶数要求；如果选择的硬盘数量不满足后端规则，页面会拒绝提交或后端返回错误。

    <Callout title="扩容期间限制" type="warn">
      RAID 扩容会触发后台重构。重构期间请勿断电、拔盘或继续执行其他维护操作。建议等待 RAID 状态恢复为 Clean 后，再进行格式化、挂载或业务写入。
    </Callout>

    ### 修改 RAID 模式 [step]

    修改 RAID 模式用于将当前阵列切换到其他支持的 RAID Level。页面会先显示当前模式，再显示可选择的目标模式。目标模式可能是降级、平级变更或升级；如果升级需要新增硬盘，页面会进入硬盘选择步骤。

    1. 打开 **Modify RAID Mode** 弹窗。
    2. 查看当前 RAID 模式、容量、理论性能和数据保护能力。
    3. 选择目标 RAID 模式。
    4. 单击 **Next (1/2)** 或 **Confirm** 继续。

    ![修改 RAID 模式](../../../img/aBMC_img/v3.0/diskManagement_img/16-modify-raid-mode-en-steps-v9.png)

    如果目标 RAID 模式需要新增硬盘，页面会进入第二步。

    1. 在第二步中选择需要加入阵列的新硬盘。
    2. 查看预计容量变化。
    3. 单击 **Confirm** 提交模式调整。

    ![选择新增硬盘](../../../img/aBMC_img/v3.0/diskManagement_img/16-modify-raid-mode-select-disk-en-steps-v9.png)

    模式调整的常见结果如下：

    | 类型 | 说明 |
    | --- | --- |
    | 降级 | 从保护能力更高的 RAID Level 切换到保护能力更低的模式，可能减少冗余能力。 |
    | 升级 | 切换到保护能力更高或容量规则不同的模式，通常需要新增硬盘并触发重构。 |
    | 平级变更 | 部分模式之间可在满足硬盘数量和容量规则时直接变更。 |

    模式调整完成前，RAID 卡片可能显示 Reshaping、Resync 或 Recovering。等待状态恢复为 Clean 后，再确认容量、成员盘和 RAID Level 是否符合预期。

    ### 删除成员盘 [step]

    删除成员盘用于从 RAID 阵列中移除指定磁盘。该操作只删除 RAID 成员关系，不等同于删除整个 RAID 阵列。

    1. 单击成员盘标签上的 **X** 图标。
    2. 在删除确认框中确认目标成员盘和 RAID 阵列名称。
    3. 单击 **Delete** 删除成员盘。

    ![删除 RAID 成员盘](../../../img/aBMC_img/v3.0/diskManagement_img/17-remove-member-disk-en-steps-v10.png)

    删除 spare、fault 或 removed 成员盘通常用于清理阵列成员列表。删除 active 成员盘会导致阵列降级，存在数据风险；如果阵列已经处于 Degraded 状态，后端通常会限制继续删除成员盘。

    ### 删除 RAID 阵列 [step]

    删除 RAID 阵列会停止阵列，并清除成员盘上的 RAID superblock。删除后，原 RAID 设备不再可用，成员盘会恢复为可重新分区、格式化或重新创建 RAID 的普通块设备。

    1. 单击 RAID 卡片右上角 **More** 图标，并选择 **Delete**。
    2. 在删除确认框中确认 RAID 阵列名称。
    3. 单击 **Delete** 删除 RAID 阵列。

    ![删除 RAID 阵列](../../../img/aBMC_img/v3.0/diskManagement_img/18-delete-raid-array-en-steps-v9.png)

    <Callout title="删除 RAID 阵列会废弃当前阵列" type="warn">
      删除 RAID 阵列会导致该阵列上的文件系统和业务数据不可继续使用。执行前请确认目标 RAID 阵列无业务占用，并已完成必要的数据备份。
    </Callout>

  </CodeBlockTab>
</CodeBlockTabs>

## 验证配置结果

<CodeBlockTabs defaultValue="WEB">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="WEB">WEB</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="WEB">
    ### 确认磁盘与 RAID 状态 [step]

    操作完成后建议按以下方式验证：

    1. 返回 **Physical View**，确认磁盘分区、文件系统、挂载点和 UUID 是否更新。
    2. 执行格式化或检查文件系统后，查看日志弹窗中的任务进度和结果。
    3. 返回 **Logical View**，确认 RAID 状态是否为 **Clean**，成员盘状态是否为 `active sync`。
    4. 如果执行 RAID 扩容或模式调整，等待重构完成后再进行业务写入。
    5. 如页面未刷新，手动刷新页面后再次确认后端状态。

  </CodeBlockTab>
</CodeBlockTabs>

## 常见问题 FAQ

### 1.创建分区按钮不可用

当前选中的对象不是未分配空间。请在磁盘图形中选择 **Unallocated** 区块，再执行 **Create Partition**。

### 2.格式化按钮不可用或格式化失败

当前磁盘、分区或其子分区可能已经挂载。请先执行 **Unmount**，确认挂载点已经清除，再执行 **Format Partition**。

### 3.挂载按钮不可用

当前对象没有可挂载的文件系统。请先执行 **Format Partition** 创建受支持的文件系统，再执行 **Mount**。

### 4.取消挂载按钮不可用

当前分区没有挂载点。请选择已经挂载的分区，并确认详情区域已经显示挂载目录。

### 5.RAID 模式不可选

可用硬盘数量不足，或者当前硬盘数量不满足目标 RAID Level 的最低要求。请增加空闲硬盘，或选择硬盘数量要求更低的 RAID 模式。

### 6.RAID 维护菜单部分操作不可用

当前 RAID 状态不允许执行对应操作。请等待 Resync、Recovering 或 Reshaping 完成；阵列处于 Degraded 状态时，应先检查并处理故障盘。

### 7.SMART 信息为空

目标设备可能不支持 SMART，或者后端读取 SMART 信息失败。请检查硬盘连接和系统日志，并确认目标硬盘支持 SMART。
