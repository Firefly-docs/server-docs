# 投屏管理

## 简介

投屏管理是aBMC面向 Android 设备提供的集中投屏和运维管理功能。该功能由投屏控制和设备辅助管理两大模块组成。
1. 投屏控制：支持筛选 USB 或 Wi-Fi 连接的 Android 设备，对单台或多台设备启动投屏、停止投屏、放大预览和多设备控制。
2. 设备辅助管理：支持向已选设备发送文件、安装 APK、执行 ADB Shell、扫描 Wi-Fi 设备和设置设备分组。

## 开发愿景
1. 为多台 Android 设备提供统一的画面预览和控制入口，减少逐台连接、核对和操作设备的重复工作。
2. 通过设备分组、连接类型和在线状态筛选，降低批量投屏、应用部署和远程调试时误选设备的风险。


# 功能使用

## 选择设备并管理投屏

<CodeBlockTabs defaultValue="WEB">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="WEB">WEB</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="WEB">
    ### 打开投屏管理页面 [step]

    1. 在左侧主导航栏中选择 **Services**。
    2. 在二级导航栏中选择 **Casting**。
    3. 查看 **Send File**、**Install APK**、**Adb shell**、**Scan Wi-Fi** 和 **Set Group** 等功能入口。
    4. 等待 **Device Management** 加载完成，确认设备标签、UDID 和状态已经显示。
    5. 查看右侧投屏区域。当前没有投屏设备时，页面显示 **No Devices in Casting**。

    ![Open the Casting page](../../../aBMC_img/v3.0/casting_img/01-enter-casting-en-steps-v3.png)

    <Callout title="仅支持 Android 设备" type="warn">
      投屏管理仅支持 Android 设备。使用前应确认设备已开启开发者选项和 USB 或无线调试，已完成调试授权，并已通过 USB 或 Wi-Fi 被 aBMC 正确识别。
    </Callout>

    ### 筛选和选择设备 [step]

    1. 在 **Group** 中选择目标分组；选择 **All** 时显示全部设备。
    2. 在 **Type** 中选择 **All**、**USB** 或 **Wifi**。三个选项为单选关系，每次只显示一种连接范围。
    3. 使用 **Sel All**、**Dsel All**、**Casting**、**Online** 或 **Offline** 快速改变已选设备。
    4. 在设备列表中核对标签、UDID 和状态，然后单击设备行或复选框选择目标设备。USB 设备通常显示设备序列号，Wi-Fi 设备显示为 `IP:端口`。

    ![Filter and identify an Android device](../../../aBMC_img/v3.0/casting_img/02-select-android-device-en-steps-v3.png)

    **Device** 区域中的快捷项用于改变已选设备，不会隐藏设备列表。

    | 快捷项 | 选择结果 | 使用建议 |
    | --- | --- | --- |
    | Sel All | 选择当前分组和连接类型范围内的全部设备。 | 批量操作前先限定 Group 和 Type，避免扩大操作范围。 |
    | Dsel All | 清空当前全部选择。 | 完成批量操作后建议执行一次。 |
    | Casting | 选择当前已经投屏且在线的设备。 | 适合批量停止投屏或批量控制。 |
    | Online | 选择在线但尚未投屏的设备。 | 适合批量启动投屏。 |
    | Offline | 选择连接状态不是 online 的设备。 | 设备行可能显示 **Unauthorized**，应先恢复连接或完成授权。 |

    <Callout title="设备身份核对" type="info">
      设备标签可能被修改或重复。执行投屏、文件发送、APK 安装或 ADB Shell 前，应同时核对设备标签和 UDID，不要只根据设备序号或列表顺序判断目标设备。
    </Callout>

    ### 启动和停止单台投屏 [step]

    1. 选择一台状态为 **Online** 的设备。
    2. 单击 **Start Casting**。
    3. 确认设备状态变为 **Casting**，右侧生成包含序号、UDID 和实时画面的预览卡。
    4. 如需停止投屏，保持目标设备处于选中状态并单击 **Stop Casting**。

    ![Start and stop a live Casting session](../../../aBMC_img/v3.0/casting_img/03-start-stop-casting-en-steps-v3.png)

    <Callout title="投屏成功标志" type="info">
      应同时确认设备状态显示 **Casting**、右侧出现预览卡且画面能够持续刷新。只单击 **Start Casting**，不能作为投屏成功的判断依据。
    </Callout>

    也可以单击预览卡右下角的停止图标，仅停止该预览卡对应设备的投屏。

    ### 批量启动和停止投屏 [step]

    1. 使用 **Group** 和 **Type** 限定设备范围，然后逐台选择目标设备，或使用 **Online** 快速选择在线且尚未投屏的设备。
    2. 复核设备标签和 UDID 后，单击 **Start Casting**。
    3. 确认全部目标设备均显示 **Casting**，并在右侧逐一检查预览画面。
    4. 如需批量停止，选择当前投屏设备后单击 **Stop Casting**。

    ![Start and stop Casting for multiple Android devices](../../../aBMC_img/v3.0/casting_img/03-batch-casting-en-steps-v3.png)

    操作完成后单击 **Dsel All**，避免后续操作沿用原选择范围。

    <Callout title="批量投屏说明" type="warn">
      批量投屏会增加浏览器视频解码、aBMC 处理能力和管理网络带宽的占用。首次使用时建议从少量设备开始，确认画面和控制稳定后再逐步增加设备数量。
    </Callout>

    ### 使用右键快捷操作 [step]

    1. 在设备行上单击鼠标右键。
    2. 单台设备选中时，使用 **Start Casting** 或 **Stop Casting**。
    3. 多台设备选中时，右键菜单显示批量启动或批量停止操作。
    4. 操作后仍应通过设备状态和预览画面确认结果。
  </CodeBlockTab>
</CodeBlockTabs>


## 预览和控制 Android 设备

<CodeBlockTabs defaultValue="WEB">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="WEB">WEB</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="WEB">
    ### 使用预览卡 [step]

    投屏成功后，按照下图使用预览卡：

    1. 核对预览卡中的实时画面、设备序号和 UDID；单击卡片可以选择或取消选择该设备。
    2. 单击 **Multi Control**，将该设备标记为多控目标；启用后图标变为红色。
    3. 单击 **Preview** 打开放大预览，也可以双击预览卡。
    4. 单击 **Stop Casting** 停止该设备投屏并移除预览卡。

    ![Use Multi Control, Preview, and Stop Casting](../../../aBMC_img/v3.0/casting_img/04-live-preview-controls-en-steps-v4.png)

    | 操作 | 说明 |
    | --- | --- |
    | 选择预览卡 | 单击预览卡，选择或取消选择对应设备。选中后卡片边框高亮。 |
    | Multi Control | 将该设备标记为多控目标。启用后图标变为红色。 |
    | Preview | 打开放大预览。也可以双击预览卡打开。 |
    | Stop Casting | 停止该设备投屏并移除预览卡。 |

    ### 打开放大预览 [step]

    单击预览卡底部的 **Preview** 图标或双击预览卡。窗口打开后，按照下图操作：

    1. 核对窗口顶部显示的设备序号和 UDID。
    2. 等待实时画面完成加载，然后通过鼠标执行单击、滑动等触控操作。
    3. 使用 **Multi Control** 开启或关闭多控发送。
    4. 使用 **Power** 控制 Android 设备屏幕电源。
    5. 使用 **Volume Up** 和 **Volume Down** 调整音量。
    6. 使用 **Back**、**Home** 和 **Overview** 执行 Android 导航操作。
    7. 使用 **Screenshot** 保存当前设备画面。
    8. 使用 **Keyboard**、**Copy Content** 或 **Open App** 执行输入、剪贴板或应用操作。
    9. 单击右上角关闭按钮退出放大预览。关闭窗口不会停止原投屏。

    ![Use the expanded Casting preview and device quick controls](../../../aBMC_img/v3.0/casting_img/05-expanded-preview-toolbar-en-steps-v3.png)

    ### 使用快捷控制 [step]

    | 控件 | 作用 | 操作说明 |
    | --- | --- | --- |
    | Multi Control | 开启当前放大窗口的多控发送。 | 需与其他预览卡的 Multi Control 标记配合使用。 |
    | Power | 发送 Android 电源键。 | 可能点亮、熄灭或唤醒屏幕，取决于设备当前状态。 |
    | Volume Up / Volume Down | 调高或调低设备音量。 | 每次操作发送一次对应按键事件。 |
    | Back / Home / Overview | 执行 Android 返回、主屏幕或最近任务操作。 | 响应结果取决于设备当前页面和系统状态。 |
    | Screenshot | 保存当前设备画面截图。 | 下载文件由浏览器保存，应避免包含敏感信息。 |
    | Keyboard | 开启或关闭浏览器键盘事件转发。 | 图标高亮时，键盘输入会发送到 Android 设备。 |
    | Copy Content | 将输入的文本写入 Android 剪贴板。 | 输入文本后单击 **Copy**；完成后单击 **Cancel** 关闭输入框。 |
    | Open App | 显示设备应用列表并打开指定应用。 | 应确认应用已安装且当前账号具有启动权限。 |

    <Callout title="预览加载说明" type="info">
      放大预览建立新的视频显示区域时，画面可能短暂显示黑色，随后才开始刷新。不要在初始化阶段立即判断投屏失败。
    </Callout>

    ### 批量控制设备 [step]

    1. 为控制源设备和所有目标设备启动投屏，确认右侧显示对应的预览卡。
    2. 在需要同步接收操作的目标设备预览卡上单击 **Multi Control**，确认图标变为红色。
    3. 在作为控制源的设备预览卡上单击 **Preview**。

    ![Select the source and target devices for Multi Control](../../../aBMC_img/v3.0/casting_img/05-multi-control-source-target-en-steps-v3.png)

    4. 在放大预览底部单击 **Multi Control**，确认图标变为红色。
    5. 在控制源画面中执行触控、Android 导航或其他快捷操作。
    6. 观察已标记的目标设备是否同步响应，并逐台确认执行结果。
    7. 操作完成后，关闭放大预览中的 **Multi Control**，并取消目标预览卡上的 Multi Control 标记。

    <Callout title="Multi Control 使用范围" type="warn">
      Multi Control 只向已经投屏且已标记为多控目标的设备同步操作。不同设备的分辨率、系统版本、当前页面和响应速度可能不同，批量操作前应先用少量设备验证，重要操作后必须逐台检查结果。
    </Callout>

    ### 批量打开应用 [step]

    1. 确认目标设备已经进入 **Casting** 状态。
    2. 在左侧设备列表或右侧预览卡中选择一台或多台目标设备。
    3. 单击页面顶部的 **Open App**。
    4. 在应用列表中选择需要启动的应用。
    5. 逐台确认应用是否成功打开。

    <Callout title="应用一致性" type="info">
      只有当前已选择且正在投屏的设备会接收打开应用操作。批量操作前应确认目标设备均已安装同一应用；某台设备打开成功不代表其他设备也已成功。
    </Callout>
  </CodeBlockTab>
</CodeBlockTabs>


## 批量维护 Android 设备

<CodeBlockTabs defaultValue="WEB">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="WEB">WEB</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="WEB">
    ### 发送文件 [step]

    在设备列表中选择一台或多台目标设备，然后单击 **Send File**。窗口打开后，按照下图操作：

    1. 单击 **Selected File** 选择文件；每次最多选择 5 个文件。检查无误后单击 **Send File**。
    2. 在文件列表中核对等待发送的文件名称。
    3. 在 **Selected Device** 中复核全部目标设备序号。
    4. 在右侧任务树中查看每台设备、每个文件的处理状态；任务失败时可以重试单个失败项或全部失败项。

    ![Send files to selected Android devices](../../../aBMC_img/v3.0/casting_img/06-send-file-en-steps-v3.png)

    <Callout title="发送文件说明" type="warn">
      文件上传目录为 `/data/local/tmp`。发送操作会作用于全部已选设备，提交前应确认文件内容、文件名、目标设备和剩余空间。关闭窗口后，尚未开始的后续任务将不再执行。
    </Callout>

    ### 安装 APK [step]

    在设备列表中选择一台或多台目标设备，然后单击 **Install APK**。窗口打开后，按照下图操作：

    1. 单击 **Selected Apk** 选择扩展名为 `.apk` 的安装包；每次最多选择 5 个文件。检查无误后单击 **Install APK**。
    2. 在文件列表中核对等待安装的 APK 名称。
    3. 在 **Selected Device** 中复核全部目标设备序号。
    4. 在右侧任务树中查看每台设备、每个 APK 的安装状态；安装失败时可以重试失败任务。

    ![Install APK files on selected Android devices](../../../aBMC_img/v3.0/casting_img/07-install-apk-en-steps-v3.png)

    <Callout title="APK 安装说明" type="warn">
      安装前应确认 APK 的来源、包名、签名、Android 版本、处理器架构和升级覆盖关系。某一设备安装成功不表示其他设备也已成功。
    </Callout>

    ### 执行 ADB Shell 命令 [step]

    在设备列表中选择一台或多台目标设备，然后单击 **Adb shell**。窗口打开后，按照下图操作：

    1. 在 **Selected Device** 中核对全部目标设备序号。
    2. 在 **Command List** 中选择已有快捷命令。
    3. 也可以直接输入命令，然后单击 **Execute** 或按 Enter 执行。
    4. 使用 **Command Execution Result** 展开或收起结果，使用 **Clear** 清空本次窗口中的记录。
    5. 根据时间戳和 UDID 分别查看每台设备的返回内容。

    ![Execute and review an ADB Shell command](../../../aBMC_img/v3.0/casting_img/08-adb-shell-en-steps-v3.png)

    实际验证时，同时向 USB 设备和 `172.16.11.67:5555` 执行只读命令 `pwd`，两台设备均返回 `/`。

    ### 管理 ADB 快捷命令 [step]

    1. 在命令输入框中填写已经审核的命令。
    2. 单击 **Add**，填写快捷命令名称和命令值，然后确认保存。
    3. 如需批量维护快捷命令，单击 **Edit**。
    4. 在编辑窗口中修改名称或命令值，使用加号新增行、减号删除行。
    5. 单击 **Confirm** 保存修改。

    <Callout title="ADB Shell 命令说明" type="warn">
      同一命令会发送到全部已选设备。ADB Shell 命令可能修改 Android 系统配置、应用数据或运行状态，不要执行来源不明的命令；建议先使用 `pwd`、`getprop` 等只读命令验证目标设备和连接。
    </Callout>

    ### 文件和 APK 任务状态 [step]

    | 状态 | 说明 | 处理建议 |
    | --- | --- | --- |
    | Waiting for Upload / Installation | 任务已创建，正在等待处理。 | 保持窗口打开并等待前序任务完成。 |
    | Uploading / Installing | 文件正在发送或 APK 正在安装。 | 不要断开设备连接或刷新页面。 |
    | Upload Successful / Installation Successful | 当前设备上的当前任务已完成。 | 继续核对其他设备和文件任务。 |
    | Upload Failed / Installation Failed | 当前任务执行失败。 | 检查连接、存储空间、APK 兼容性或设备权限后重试。 |

  </CodeBlockTab>
</CodeBlockTabs>

## 管理 Wi-Fi 设备和分组

<CodeBlockTabs defaultValue="WEB">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="WEB">WEB</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="WEB">
    ### 扫描 Wi-Fi 设备 [step]

    单击 **Scan Wi-Fi** 打开扫描窗口，然后按照下图操作：

    1. 在 **Start IP** 和 **End IP** 中填写扫描地址范围。扫描单台设备时填写相同地址，例如 `172.16.11.67`。
    2. 在 **Start Port** 和 **End Port** 中填写无线 ADB 端口范围。扫描单个端口时填写相同端口，例如 `5555`。
    3. 单击 **Scan** 并等待扫描完成。扫描过程中可以单击 **Stop** 中止操作。
    4. 在扫描结果中勾选需要连接的 `IP:端口`。
    5. 单击 **Link**，进入设备标签设置窗口。

    ![Scan Wi-Fi Android devices](../../../aBMC_img/v3.0/casting_img/09-scan-wifi-en-steps-v3.png)

    ### 连接扫描到的设备 [step]

    打开设备标签设置窗口后，按照下图操作：

    1. 核对需要连接的设备地址，例如 `172.16.11.67:5555`。
    2. 为设备填写便于识别的标签，例如 `wifi1`。
    3. 确认标签符合页面提示的长度和字符要求。
    4. 单击 **Link**，等待设备加入列表。

    ![Set the label and link a Wi-Fi Android device](../../../aBMC_img/v3.0/casting_img/09-connect-wifi-en-steps-v4.png)

    连接完成后，返回设备列表，确认设备标签、`172.16.11.67:5555` 和 **Online** 状态均已显示。

    <Callout title="设备标签规则" type="info">
      四个扫描字段均为必填项。设备标签长度为 1–5 个字符，只能使用中文、英文字母或数字。扫描范围越大，完成时间越长；建议按实际地址规划缩小 IP 和端口范围。
    </Callout>

    ### 维护 Wi-Fi 设备 [step]

    Wi-Fi 设备行显示为 `IP:端口`，右侧提供 Wi-Fi 标识和删除入口。删除前应核对设备标签和地址；删除只移除 aBMC 中保存的无线 ADB 设备记录，不代替 Android 设备上的无线调试配置。

    ### 创建设备分组 [step]

    1. 单击 **Group** 右侧的加号。
    2. 在 **Add Group** 窗口中输入分组名称。
    3. 单击 **Confirm** 创建分组。

    ![Create a Casting device group](../../../aBMC_img/v3.0/casting_img/10-add-group-en-steps-v4.png)

    ### 修改或删除分组 [step]

    1. 展开 **Group** 下拉列表。
    2. 单击目标分组右侧的编辑图标，修改分组名称。
    3. 单击删除图标时，确认目标分组无误后再提交删除。
    4. 分组变更后重新选择目标分组，确认设备数量和列表范围正确。

    ### 设置分组成员 [step]

    单击 **Set Group** 打开分组设置窗口，然后按照下图操作：

    1. 在窗口顶部选择需要维护的分组。
    2. 在左侧 **Device List** 中选择需要加入分组的设备。
    3. 使用中间箭头将设备加入右侧，或将右侧设备移回左侧。
    4. 在右侧分组列表中核对当前分组成员。
    5. 单击 **Save** 保存分组成员关系。

    ![Assign selected devices to a Casting group](../../../aBMC_img/v3.0/casting_img/11-set-group-en-steps-v3.png)

    <Callout title="分组为空" type="info">
      如果 **Set Group** 窗口显示 **No data** 或无法选择分组，请先使用 **Group** 右侧的加号创建分组。分组会影响后续批量投屏和设备维护的选择范围，保存后应返回设备列表复核。
    </Callout>
  </CodeBlockTab>
</CodeBlockTabs>

## 常见问题 FAQ

### 1.Android 设备没有出现在列表中
检查设备供电、USB 或 Wi-Fi 连接、Android 调试设置和授权提示。确认设备能够被 aBMC 识别后，刷新页面。

### 2.设备显示 Unauthorized
设备已被识别，但 Android 调试授权尚未完成或已失效。检查设备端授权弹窗，重新连接 USB 或无线 ADB，确认状态恢复为 **Online** 后再投屏。

### 3.单击 Start Casting 后没有预览
重新核对设备选择和 UDID，确认设备在线、调试授权已完成，并检查 USB 或 Wi-Fi 连接。

### 4.预览画面短暂为黑色
视频连接可能正在初始化，或 Android 屏幕处于熄屏、锁定状态。等待数秒并检查设备屏幕和授权弹窗；长时间无画面时再重建投屏。

### 5.批量投屏时只有部分设备出现预览
单击 **Casting** 选择已成功投屏的设备，再单击 **Online** 检查仍在线但未投屏的设备。减少单次投屏数量，确认少量设备稳定后再逐步增加。

### 6.Send File 或 Install APK 不可用
先确认已经选择目标设备，再打开对应窗口并单击 **Selected File** 或 **Selected Apk** 选择文件。未选择文件时，提交按钮保持不可用。

### 7.ADB Shell 不可用
检查 Android 调试授权、ADB 连接、设备在线状态和当前账号权限，恢复连接后再执行已审核的命令。

### 8.Scan Wi-Fi 没有结果
检查 IP 和端口范围、无线调试配置和网络连通性。扫描完成后必须选择目标设备，**Link** 才会变为可用。

### 9.Set Group 显示 No data
当前可能尚未创建分组，或没有设备属于所选分组。先使用 **Group** 右侧的加号创建分组，再进入 **Set Group** 选择设备并保存。
