# AIC 部署

# 部署 AIC

本章基于 [K3s 部署](install.md) 中已经完成在线部署的 K3s 集群，介绍如何在 K3s 上部署 AIC（Android in Container），实现 Android 实例的容器化运行与统一管理。

- **AIC 是什么**：AIC（Android in Container）是一种在 Linux 容器中运行 Android 的方案。在 Linux 上，可以使用 Docker 启动 Android，并支持同时运行多个 Android 实例。

- **为什么使用 K3s**：将 AIC 实例作为 Kubernetes 的 `Deployment` 进行管理，可以利用 K3s 提供的调度、重启和故障恢复能力。多节点部署时，还可以通过 `nodeSelector` 将实例调度到指定节点。

- **适用场景**：通过 K3s 统一管理多个节点上的 AIC 实例，可以提高 Android 实例的部署密度，适用于云手机、云游戏等场景。

| 操作位置 | 机器 | 地址 | 本页要做什么 |
|---|---|---|---|
| K3s Server 节点 | `bmc` | `172.16.100.176` | 执行 `kubectl`，创建与查看 AIC 资源 |
| Agent 节点 | `sub11` | `172.16.100.177` | 准备镜像与容器配置文件；AIC 实例运行在这里 |
| 客户端（你的电脑） | — | — | 用 `adb` 连接实例 |


## 环境准备

### 准备资料 [step]

资料包解压后有两个目录：`container/` 是关于安卓容器的脚本，配置与镜像，最终要放到 Agent 节点的 `/userdata/` 下；`host_fw/` 是 Agent 节点所烧录固件，下一步会用到。

```text
AIC/
├── container  ## 容器配置
│   ├── aic.sh
│   ├── android_config
│   │   ├── README.txt
│   │   ├── container_0.conf
│   │   ├── container_1.conf
│   │   ├── container_2.conf
│   │   ├── container_3.conf
│   │   ├── container_4.conf
│   │   ├── container_5.conf
│   │   ├── container_6.conf
│   │   ├── container_7.conf
│   │   └── container_common.conf
│   ├── daemon.json
│   └── rk3588_docker-android12-userdebug-super.img-20250117.1531.tgz
└── host_fw
    └── ROC-RK3588S-PC_Ubuntu20.04-Minimal-r3104_v1.3.0c_241107.7z
```

资料包[下载地址](https://pan.baidu.com/s/1sbZwOc4peZfn0HDX9O0lsg)（提取码：`1234`）

### 烧录 Agent 固件 [step]

Agent 节点需先烧录定制固件，即资料包 `host_fw/` 中的 `ROC-RK3588S-PC_Ubuntu20.04-Minimal-…7z`，步骤见[烧录固件](https://community.t-firefly.com/docs/server/bmc-software/aBMC/upgrade)。

### 部署 Agent [step]

Server 与 Agent 节点分别按 [K3s部署](install.md) 完成在线部署，等 Agent 加入集群后继续下面操作。

### 制作镜像 [step]

先把资料包中的 `container/` 目录传到 Agent 节点（本案例 `sub11`）的 `/userdata/` 下，镜像只需制作一次：生成后推送到私有仓库，其余节点部署时直接从仓库拉取，不必重复操作。`-c` 会把固件包（`*.tgz`，内含 Android 各分区镜像）制作成 Docker 镜像，过程记录在同目录的 `build_image.log`：

```bash
cd /userdata/container

sudo bash aic.sh -c /userdata/container/rk3588_docker-android12-userdebug-super.img-20250117.1531.tgz
```

镜像生成后按私有仓库地址打标签并推送（本案例仓库为 `172.16.55.55:5000`）：

```bash
sudo docker tag <本地镜像> 172.16.55.55:5000/aic/rk3588-android12:<标签>

sudo docker push 172.16.55.55:5000/aic/rk3588-android12:<标签>
```


```text
The push refers to repository [172.16.55.55:5000/aic/rk3588-android12]
ANDROID12_RKR14-zzz01171531: digest: sha256:9f926da5... size: 529
PUSH-OK
```
<Callout>
 仓库是 HTTP 服务，节点上的 `/etc/docker/daemon.json` 必须把地址加入 `insecure-registries`，否则推送与拉取都会失败：

 ```json
 {
   "insecure-registries": ["http://172.16.55.55:5000"]
 }
 ```
</Callout>

## 应用清单

以下清单在 Server 节点 `bmc` 上保存为 `aic.yml`，「部署」一节里的 `kubectl` 命令也在该节点执行。清单为单文件，同时包含 `ConfigMap`、`PVC`、`Deployment` 与 `Service`；示例只启用 2 个实例，`ConfigMap` 中预置了 8 组配置，需要更多实例时按编号取用。

```yml
# AIC Android 容器 K3s 部署清单（2 实例版，含 ConfigMap 配置，单文件）
#
# 本文件 = ConfigMap(container_0~7.conf + container_common.conf) + PVC + Deployment + Service
# 配置文件来源: sub11:/userdata/container/android_config/*.conf
# 对应关系: 实例 N -> PVC my-dataN + Service 110N->5555 + container_N.conf
# sub11 内存 3.8G, 实际同时运行 <= 3 个实例; 扩更多实例前先加节点
# 用法: sudo k3s kubectl apply -f aic.yml
apiVersion: v1
data:
  container_0.conf: |
    # config for container 0
    container_id=0
    ### display
    # 绑定到哪个屏幕，默认使用VVOP:Virtual-1~Virtual-8
    # 如果有实际屏幕，可以配置为实际屏幕的设备节点
    # ls /sys/class/drm/card0 可以查看支持的设备
    primary_type=Virtual-1
  container_1.conf: |
    # config for container 1
    container_id=1
    ### display
    # 绑定到哪个屏幕，默认使用VVOP:Virtual-1~Virtual-8
    # 如果有实际屏幕，可以配置为实际屏幕的设备节点
    # ls /sys/class/drm/card0 可以查看支持的设备
    primary_type=Virtual-2
  container_2.conf: |
    # config for container 2
    container_id=2
    ### display
    # 绑定到哪个屏幕，默认使用VVOP:Virtual-1~Virtual-8
    # 如果有实际屏幕，可以配置为实际屏幕的设备节点
    # ls /sys/class/drm/card0 可以查看支持的设备
    primary_type=Virtual-3
  container_3.conf: |
    # config for container 3
    container_id=3
    ### display
    # 绑定到哪个屏幕，默认使用VVOP:Virtual-1~Virtual-8
    # 如果有实际屏幕，可以配置为实际屏幕的设备节点
    # ls /sys/class/drm/card0 可以查看支持的设备
    primary_type=Virtual-4
  container_4.conf: |
    # config for container 4
    container_id=4
    ### display
    # 绑定到哪个屏幕，默认使用VVOP:Virtual-1~Virtual-8
    # 如果有实际屏幕，可以配置为实际屏幕的设备节点
    # ls /sys/class/drm/card0 可以查看支持的设备
    primary_type=Virtual-5
  container_5.conf: |
    # config for container 5
    container_id=5
    ### display
    # 绑定到哪个屏幕，默认使用VVOP:Virtual-1~Virtual-8
    # 如果有实际屏幕，可以配置为实际屏幕的设备节点
    # ls /sys/class/drm/card0 可以查看支持的设备
    primary_type=Virtual-6
  container_6.conf: |
    # config for container 6
    container_id=6
    ### display
    # 绑定到哪个屏幕，默认使用VVOP:Virtual-1~Virtual-8
    # 如果有实际屏幕，可以配置为实际屏幕的设备节点
    # ls /sys/class/drm/card0 可以查看支持的设备
    primary_type=Virtual-7
  container_7.conf: |
    # config for container 7
    container_id=7
    ### display
    # 绑定到哪个屏幕，默认使用VVOP:Virtual-1~Virtual-8
    # 如果有实际屏幕，可以配置为实际屏幕的设备节点
    # ls /sys/class/drm/card0 可以查看支持的设备
    primary_type=Virtual-8
  container_common.conf: |
    # default config for all container
    # 该配置为所有容器生效的默认配置，
    # 如需修改，请在对应容器的配置文件中修改
    # 除非希望在所有容器中生效，否则不要修改该配置文件
    # 必须打开，init.redroid.rc中使用
    enable.container.config=true
    # 配置容器id，该配置必须在应容器的配置文件中修改为对应容器id
    container_id=0
    ### network
    # 网络类型:docker0 / macvlan_static / macvlan_dhcp / host
    network.type=docker0
    # dns 配置，没有配置，默认8.8.8.8
    # 只有docker0 / macvlan_static 配置dns才能生效
    # macvlan_dhcp / host 配置dns参数不会生效
    net_dns.num=2
    net_dns1=114.114.114.114
    net_dns2=8.8.8.8
    ### HWC
    # 配置默认显示屏，必须在容器私有配置中重新配置
    # 如果需要多个屏幕显示配置成primary_type=DSI-1,HDMI-A-1
    primary_type=DSI-1
    # 配置默认分辨率
    default.resolution=1080x1920@60
    ### audio
    # 关闭音频输出到宿主机
    disable.audio.output=true
    ### input event
    # 关闭input输入事件,
    # 如需容器支持鼠标键盘，可以配置不关闭输入事件
    # 不关闭输入事件，配置为 false
    disable.input.event=true
    ### ueventd
    # ueventd 冷启动，默认不开启冷启动
    # 如果需要使用U盘等外设，建议开启冷启动
    # 开启冷启动，将该配置设置为false
    disable.ueventd.cold_boot=true
    ### usb
    # 默认不关闭usb.configfs，
    # 如无需使用USB，可以关闭，关闭请配置为true
    disable.usb.configfs=false
kind: ConfigMap
metadata:
  name: aic-android-config
  namespace: default
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-data0
spec:
  accessModes: ["ReadWriteOnce"]
  storageClassName: longhorn
  resources:
    requests:
      storage: 5Gi
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aic-android-0
  labels:
    app: aic-android
    instance: "0"
spec:
  replicas: 1
  strategy:
    type: Recreate # Android 启动耗时长,避免更新期间新旧实例并存
  selector:
    matchLabels:
      app: aic-android
      instance: "0"
  template:
    metadata:
      labels:
        app: aic-android
        instance: "0"
    spec:
      nodeSelector:
        kubernetes.io/hostname: sub11
      hostname: android-0
      terminationGracePeriodSeconds: 60
      containers:
        - name: android
          image: 172.16.55.55:5000/aic/rk3588-android12:ANDROID12_RKR14-zzz01171531
          imagePullPolicy: IfNotPresent
          securityContext:
            privileged: true
          resources:
            requests:
              cpu: 500m
              memory: 1Gi
          ports:
            - name: adb
              containerPort: 5555
          volumeMounts:
            - name: data
              mountPath: /data
            - name: config
              mountPath: /vendor/etc/container/container_common.conf
              subPath: container_common.conf
            - name: config
              mountPath: /vendor/etc/container/container.conf
              subPath: container_0.conf # 每实例不同配置
      volumes:
        - name: data
          persistentVolumeClaim:
            claimName: my-data0
        - name: config
          configMap:
            name: aic-android-config # 8 实例 + common 配置全在这里
---
# 每实例一个 LoadBalancer Service(k3s ServiceLB), 所有节点绑定端口, adb connect <任意节点IP>:1100
apiVersion: v1
kind: Service
metadata:
  name: aic-android-0
  labels:
    app: aic-android
    instance: "0"
spec:
  type: LoadBalancer
  selector:
    app: aic-android
    instance: "0"
  ports:
    - name: adb
      port: 1100
      targetPort: 5555
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-data1
spec:
  accessModes: ["ReadWriteOnce"]
  storageClassName: longhorn
  resources:
    requests:
      storage: 5Gi
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aic-android-1
  labels:
    app: aic-android
    instance: "1"
spec:
  replicas: 1
  strategy:
    type: Recreate # Android 启动耗时长,避免更新期间新旧实例并存
  selector:
    matchLabels:
      app: aic-android
      instance: "1"
  template:
    metadata:
      labels:
        app: aic-android
        instance: "1"
    spec:
      nodeSelector:
        kubernetes.io/hostname: sub11
      hostname: android-1
      terminationGracePeriodSeconds: 60
      containers:
        - name: android
          image: 172.16.55.55:5000/aic/rk3588-android12:ANDROID12_RKR14-zzz01171531
          imagePullPolicy: IfNotPresent
          securityContext:
            privileged: true
          resources:
            requests:
              cpu: 500m
              memory: 1Gi
          ports:
            - name: adb
              containerPort: 5555
          volumeMounts:
            - name: data
              mountPath: /data
            - name: config
              mountPath: /vendor/etc/container/container_common.conf
              subPath: container_common.conf
            - name: config
              mountPath: /vendor/etc/container/container.conf
              subPath: container_1.conf # 每实例不同配置
      volumes:
        - name: data
          persistentVolumeClaim:
            claimName: my-data1
        - name: config
          configMap:
            name: aic-android-config # 8 实例 + common 配置全在这里
---
# 每实例一个 LoadBalancer Service(k3s ServiceLB), 所有节点绑定端口, adb connect <任意节点IP>:1101
apiVersion: v1
kind: Service
metadata:
  name: aic-android-1
  labels:
    app: aic-android
    instance: "1"
spec:
  type: LoadBalancer
  selector:
    app: aic-android
    instance: "1"
  ports:
    - name: adb
      port: 1101
      targetPort: 5555
```

## 部署

### 执行清单 [step]

在 Server 节点 `bmc` 上执行清单：

```bash
sudo k3s kubectl apply -f aic.yml
```

### 确认实例状态 [step]

确认实例、卷与 Service 的状态：

```bash
sudo k3s kubectl get pods -o wide

sudo k3s kubectl get pvc

sudo k3s kubectl get svc
```

两个实例运行在 `sub11`，Service 由 K3s 内置的 ServiceLB 在所有节点上绑定 `1100` / `1101`：

```text
NAME                             READY   STATUS    IP           NODE
aic-android-0-5b89c454d8-wl9kp   1/1     Running   10.42.1.80   sub11
aic-android-1-6c794d5f4-5gsqx    1/1     Running   10.42.1.81   sub11

NAME            TYPE           CLUSTER-IP      EXTERNAL-IP                     PORT(S)
aic-android-0   LoadBalancer   10.43.111.178   172.16.100.176,172.16.100.177   1100:30536/TCP
aic-android-1   LoadBalancer   10.43.182.80    172.16.100.176,172.16.100.177   1101:30242/TCP
```


<Callout>
安卓实例启动较慢（镜像 1.67 GB，启动后还要等安卓系统起来），首次拉取镜像时 Pod 会先处于 `Pending` 或 `ContainerCreating`。
</Callout>


## 访问实例

每个实例对应一个 `LoadBalancer` Service，`110N` 转发到容器的 `5555`（adb 端口）。ServiceLB 在所有节点上都绑定了该端口，这里统一用 Server 节点 `bmc`（`172.16.100.176`）连接实例，装好 `adb` 的机器都可以照此执行：

```bash
adb connect 172.16.100.176:1100   # 实例 0

adb connect 172.16.100.176:1101   # 实例 1
```

```text
connected to 172.16.100.176:1100
connected to 172.16.100.176:1101
```

连接完成后，用 `-s` 指定要操作的实例：

```bash

adb devices

adb -s 172.16.100.176:1100 shell

```

## 清单说明

| 资源 | 作用 |
|---|---|
| `ConfigMap aic-android-config` | 存放各实例的 `container_N.conf` 与公共的 `container_common.conf`，通过 `subPath` 分别挂载到各容器 |
| `PersistentVolumeClaim my-dataN` | 每个实例一个 5Gi 卷（`storageClassName: longhorn`），挂载到容器的 `/data` |
| `Deployment aic-android-N` | 每个实例一个副本，`nodeSelector` 固定运行节点，以特权模式运行；`Recreate` 策略避免更新期间新旧实例并存 |
| `Service aic-android-N` | `LoadBalancer` 类型，`110N → 5555`，供 `adb connect` 使用 |

几点注意：

* 实例 N 使用 `container_N.conf`，`container_common.conf` 对所有容器生效；
* `container_common.conf` 中的 `container_id` 会被各实例私有配置覆盖，改动公共配置前先确认影响范围；
* `nodeSelector` 把实例运行到指定节点上，节点不可用时实例不会漂移 —— 目标节点处于 `NotReady` 时 Pod 会一直停在 `Pending`；

## 实例数据存储优化

### 频繁装卸应用与 eMMC 老化 [step]

AIC 的每个 Android 实例都会使用一个 Longhorn 卷保存 `/data` 数据。当前配置中，每个实例使用 5Gi 的 Longhorn 卷，卷副本默认存放在节点的 `/var/lib/longhorn` 目录，而该目录位于 eMMC 上。

Android 应用的安装、卸载、更新以及运行过程中都会产生磁盘写入。对于频繁创建和销毁实例、安装和卸载应用的场景，长期的大量写入可能增加 eMMC 的损耗。

系统分区和容器本身仍会产生一定写入，无法完全避免。为了减少实例数据对 eMMC 的写入，可以将 Longhorn 的存储目录迁移到 SATA 或 NVMe 等更适合高频读写的存储设备。

整体思路是：**Android 实例仍然运行在子板节点上，但实例的 `/data` 卷改为存放在指定的 SATA 或 NVMe 存储盘上。**

常见有以下两种部署方式。

### 方案一：集中使用 Server 节点的 SATA 存储 [step]

如果子板数量较多，但对存储性能要求不高，可以将一块容量较大的 SATA 盘接入 `bmc`，由 `bmc` 统一提供 Longhorn 存储。

数据路径可以理解为：

`子板节点上的 Android 实例 → Longhorn → 网络 → bmc 的 SATA 盘`

这种方式可以集中管理存储容量，也方便后续扩容，适合实例数量较少或更关注存储成本的场景。

配置步骤如下：

1. 在 `bmc` 上挂载 SATA 盘，并创建 Longhorn 数据目录，例如 `/sata/longhorn`。
2. 将该目录配置为 Longhorn 磁盘，并开启 `allowScheduling`。
3. 为该磁盘添加存储池标签，例如 `pool-sata`。
4. 创建一个 `StorageClass`，通过 `nodeSelector` 指定 `pool-sata`。
5. 在 AIC 清单中，将实例 `my-dataN` 的 `storageClassName` 设置为该 `StorageClass`。

这样，使用该 `StorageClass` 创建的实例数据卷就会调度到 `bmc` 的 SATA 存储上，而不是使用子板节点上的 eMMC。

### 方案二：多个子板节点共用一块 NVMe [step]

如果子板数量较多，或者希望降低单个存储节点故障对所有实例的影响，可以按照节点分组，为每组节点配置一块 NVMe。

例如，每 3 个子板节点组成一个存储组，其中一台节点安装 NVMe，并将该 NVMe 配置为该组节点的 Longhorn 存储池。

数据路径可以理解为：

`子板节点上的 Android 实例 → Longhorn → 网络 → 同组节点的 NVMe`

例如：

- `sub1`、`sub2`、`sub3` 使用 `pool-nvme-1`
- `sub4`、`sub5`、`sub6` 使用 `pool-nvme-2`

配置步骤与 SATA 方案基本一致：

1. 在存储节点上挂载 NVMe，并创建 Longhorn 数据目录，例如 `/nvme/longhorn`。
2. 将该目录配置为 Longhorn 磁盘，并开启 `allowScheduling`。
3. 为磁盘添加对应的存储池标签，例如 `pool-nvme-1`。
4. 为该存储池创建对应的 `StorageClass`。
5. 将该组节点上的 AIC 实例配置为使用对应的 `StorageClass`。

这种方式可以将存储负载分散到多个节点，同时避免所有实例的数据都集中在 `bmc` 上。

### 两种方案的区别 [step]

| 对比项 | Server 节点 SATA | 每 3 个节点一块 NVMe |
|---|---|---|
| 存储位置 | 集中在 `bmc` | 分散在各存储组 |
| 存储容量 | 可使用大容量 SATA 盘 | 取决于每组 NVMe 容量 |
| 存储成本 | 较低 | 较高 |
| 网络访问 | 实例通过网络访问 `bmc` 上的存储 | 实例通过网络访问组内 NVMe |
| 存储性能 | 受网络和 SATA 性能影响 | 通常具有更高的存储性能 |
| 故障影响范围 | `bmc` 存储故障可能影响全部实例 | 单个存储节点故障主要影响对应分组 |
| 管理方式 | 存储集中，管理简单 | 存储分散，需要按组管理 |
| 适用场景 | 实例数量较少、注重容量和成本 | 实例数量较多、需要分散存储负载 |

无论采用哪种方式，**AIC 实例本身的运行节点不需要改变，只需要调整实例 PVC 使用的 `StorageClass`**。Longhorn 会根据 `StorageClass` 的存储池选择规则，将实例数据卷放置到指定的 SATA 或 NVMe 存储上。

具体的磁盘配置、存储目录和 `StorageClass` 创建方法，请参考 [longhorn](longhorn.md) 中的「配置存储目录」和「使用案例」。
