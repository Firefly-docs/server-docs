# 设备 rootfs 导出与固件重新打包

> 版本：正式版
>
> 本手册配套：
> - 基础固件：客户当前烧录的基础镜像
> - recovery 内置 `exportctl`（位于 `/usr/bin/exportctl`）
> - PC 工具：`firmware-kits`
>
> 本文用于完成以下工作：
> 1. 使用提供的固件烧录设备
> 2. 在设备上部署环境
> 3. 进入 recovery 导出当前 rootfs
> 4. 回到 PC，用 `firmware-kits` 重新打包固件

---

## 目录

1. [总体流程](#一总体流程)
2. [烧录固件](#二烧录固件)
3. [设备端部署环境](#三设备端部署环境)
4. [进入 recovery 并导出 rootfs](#四进入-recovery-并导出-rootfs)
5. [PC 端：用 firmware-kits 重新打包固件](#五pc-端用-firmware-kits-重新打包固件)
6. [FAQ](#六faq)
7. [附录：exportctl 参数速查](#七附录exportctl-参数速查)

---

## 一、总体流程

```text
1. 烧录基础固件（recovery 分区已内置 exportctl）
2. 在正常系统中部署软件、文件和配置
3. 进入 recovery，执行 exportctl 导出 rootfs
4. 将导出的 rootfs.img 拷贝到 PC
5. PC 使用 firmware-kits 解包基础固件
6. 在 rootfs 步骤覆盖导出的 rootfs / 调整分区大小 / 放入内容
7. 打包生成新固件并烧录验证
```

---

## 二、烧录固件

使用客户侧已有的烧录工具完成基础固件烧录。

```text
<基础固件>.img
```

- recovery 分区已内置 `exportctl`，无需额外安装；
- 烧录完成后正常启动进入系统。

---

## 三、设备端部署环境

在设备正常系统中完成你的部署工作，例如：

```bash
sudo apt-get install -y <你的软件>
sudo cp <你的文件> /opt/
```

说明：部署产生的修改会保留在可写层，后续导出时会一起带走。

---

## 四、进入 recovery 并导出 rootfs

### 4.1 进入 recovery

在设备正常系统中执行：

```bash
sudo reboot recovery
```

进入后可通过串口或 SSH 登录设备，确认处于 recovery：

```bash
uname -a
```

### 4.2 查看可导出平台

```bash
exportctl list
```

预期会看到类似：

```text
rk3588-firefly
bm1684
```

### 4.3 导出 rootfs

导出完整合并 rootfs：

```bash
exportctl -o /dev/mmcblk0p7 -m merged
```

`-o` 也可以直接指定 ext4 分区，例如 `/dev/sda1`。如果希望导出到目录，则使用已挂载目录：

```bash
exportctl -o /dev/sda1 -m merged

mkdir -p /mnt/usb
mount /dev/sda1 /mnt/usb
exportctl -o /mnt/usb -m merged
umount /mnt/usb
```

说明：

- `-o` 可以直接指定 ext4 块设备，也可以指定已挂载目录；
- `-m merged` 导出完整 rootfs，是默认推荐模式；
- 命令会自动识别平台、挂载、导入并生成 `rootfs.img`。

> 导出目标只支持 ext4 格式分区，正式交付请按 ext4 使用。

### 4.4 导出结果

产物目录格式：

```text
<输出目录>/<平台>-<系统>-<版本>-<时间戳>/
```

默认生成：

```text
rootfs.img
```

如果需要目录树，可加 `--no-img`。

### 4.5 其他可选参数

| 参数 | 说明 |
| ---- | ---- |
| `-m ro` | 只导出只读基础层 |
| `-m rw` | 只导出可写层 |
| `-m userdata` | 只导出数据分区 |
| `--no-img` | 只导出目录树，不打包 img |
| `--keep-identity` | 保留 machine-id / SSH 主机密钥 |

### 4.6 常见报错

| 报错 | 处理 |
| ---- | ---- |
| `不是 ext4 文件系统` | 只使用 ext4 分区作为导出目标 |
| `输出设备已被挂载` | 先卸载其它挂载点，或改用目录方式 |
| `输出空间不足` | 清理空间或换更大存储 |

---

## 五、PC 端：用 firmware-kits 重新打包固件

### 5.1 准备

- 基础固件包
- 第 4 步导出的 `rootfs.img`
- PC 环境：Ubuntu 20.04/22.04 x86_64，已安装依赖

### 5.2 启动流程

在 `firmware-kits` 目录执行：

```bash
cd firmware-kits
sudo ./firmware-kits run -l flow/rk3588.yaml -f ./<基础固件>.img
```

流程会在需要人工处理的位置暂停。通常会先询问是否使用 recovery 导出的 `rootfs.img` 覆盖解包出的 rootfs：

1. 选择 `y` 时，按提示提供导出的 `rootfs.img` 路径
2. 选择 `N` 时，继续使用基础固件中解包出来的 rootfs
3. 随后按提示调整分区大小
4. 再进入 rootfs 完成文件放入或配置修改

### 5.3 分区调整

如果不需要修改分区大小，按提示直接继续：

```bash
sudo ./firmware-kits resume
```

如果需要修改，按流程提示在暂停点完成后再继续。

### 5.4 内容替换

分区调整完成后，进入 rootfs 执行文件放入或配置修改；完成后继续：

```bash
sudo ./firmware-kits resume
```

### 5.5 打包与产物

流程完成后会生成新的固件包。

---

## 六、FAQ

**Q1：导出一定要进 recovery 吗？**
是。recovery 下更适合导出一致、干净的 rootfs。

**Q2：导出的 rootfs.img 为什么比系统分区小？**
因为导出后会做收缩处理，属于正常现象。

**Q3：默认会不会把每台机器做成一样？**
不会。默认会重置 machine-id 和 SSH host keys。

**Q4：recovery 里 /tmp 能放导出文件吗？**
可以临时使用，但不建议作为正式导出目标。优先使用 ext4 块设备或已挂载目录。

**Q5：U 盘提示不是 ext4 怎么办？**
把 U 盘分区格式化成 ext4 再试。

**Q6：导出途中中断了怎么办？**
重新执行 `exportctl` 即可，输出目录会按新的时间戳重新生成。

**Q7：默认导出只有 rootfs.img 吗？**
是，默认直接生成 `rootfs.img`。

**Q8：导出会不会占用设备 userdata 空间？**
会。导出到设备分区时，请提前确认目标分区空间充足。

**Q9：如何确认新固件升级成功？**
烧录后确认系统正常启动，且部署内容存在。

**Q10：如何确认新固件里包含我部署的内容？**
烧录后检查对应路径即可，例如 `/opt/` 下的文件。

---

## 七、附录：exportctl 参数速查

```text
exportctl [-p <平台>] -o <目标> [-m 模式] [--no-img] [--keep-identity]
exportctl list
exportctl pack -i <树目录> -o <img路径> [-l 标签]

-o <目标>   ext4 块设备或已挂载目录
-p <平台>   手动指定平台（rk3588-firefly / bm1684）
-m <模式>   merged(默认) | ro | rw | userdata
--no-img    只出目录树，不打包 img
--keep-identity  不重置机器身份
```

---