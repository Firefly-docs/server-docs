# 系统环境

安装 K3s 前，需要先把各节点的基础环境准备好。

本章节中的检查和配置都需要在 **所有 K3s 节点** 上执行，包括 Server 和 Agent。

如果节点比较多，可以先完成一台节点的配置并确认没有问题，再按相同方式处理其他节点。

## 集群规划

### Server 硬件要求 [step]

K3s Server 负责运行 Kubernetes API、调度器以及其他集群管理组件。随着节点数量和 Pod 数量增加，Server 的 CPU 和内存占用也会随之增加。

可以根据集群规模参考下面的配置：

| 部署规模     |      节点数 | vCPU |    内存 |
| -------- | -------: | ---: | ----: |
| Small    |  最多 10 个 |  2 核 |  4 GB |
| Medium   | 最多 100 个 |  4 核 |  8 GB |
| Large    | 最多 250 个 |  8 核 | 16 GB |
| X-Large  | 最多 500 个 | 16 核 | 32 GB |
| XX-Large |  500 个以上 | 32 核 | 64 GB |

> 以上配置主要用于确定 Server 的基础资源。实际部署时，还需要结合 Pod 数量、业务负载、监控、存储等组件的资源占用进行调整。

### 节点配置 [step]

当前环境使用 1 个 K3s Server 和 2 个 K3s Agent，具体配置如下：

| 主机名   | 角色                        | 管理 IP          | 架构    | 操作系统                           | 内核       | Docker Engine  | K3s 版本       |
| ----- | ------------------------- | -------------- | ----- | ------------------------------ | -------- | -------------- | ------------ |
| bmc   | K3s Server（control-plane） | 172.16.100.176 | arm64 | Debian GNU/Linux 12 (bookworm) | 6.1.118  | 20.10.24+dfsg1 | v1.36.4+k3s1 |
| sub11 | K3s Agent                 | 172.16.100.177 | arm64 | Ubuntu 20.04.6 LTS             | 5.10.160 | 26.1.3         | v1.36.4+k3s1 |
| sub12 | K3s Agent                 | 172.16.100.178 | arm64 | Debian GNU/Linux 12 (bookworm) | 6.1.141  | 20.10.24+dfsg1 | v1.36.4+k3s1 |

配置节点时注意以下几点：

* 各节点的 Docker 版本可以不同，例如 `bmc`、`sub12` 使用 `20.10.24+dfsg1`，`sub11` 使用 `26.1.3`。
* K3s Server 和 Agent 建议使用相同版本，本环境统一使用 `v1.36.4+k3s1`。
* 每个节点都需要配置唯一的节点名称，可以在 K3s 配置文件中通过 `node-name` 指定。
* 根据实际网络环境修改节点 IP，并确保各节点之间可以正常通信。

## 部署前检查 [step]

正式安装 K3s 前，先逐台检查节点环境。

### 检查节点网络 [step]

所有节点都必须使用静态 IP，并确保节点之间可以互相访问。K3s 使用节点 IP 作为集群内部地址，如果 IP 发生变化，节点可能会异常或无法加入集群。

**确认地址是否为静态配置**

```bash
# 查看节点的 IPv4 地址
ip -4 addr

# 查看默认路由
ip route show default
```

- 地址后面为 `valid_lft forever`，说明地址是静态配置的；如果出现 `valid_lft 3512sec` 之类的倒计时，说明地址来自 DHCP，需要先改为静态地址。
- 默认路由显示 `proto static`，说明网关也是静态配置的。

实测输出示例：

```text
inet 172.16.100.176/16 brd 172.16.255.255 scope global MGMT
   valid_lft forever preferred_lft forever

default via 172.16.0.1 dev MGMT proto static metric 100
```

确认地址为静态配置后，在 `bmc` 节点上测试与其他节点的连通性：

```bash
ping -c 4 172.16.100.177
ping -c 4 172.16.100.178
```

如果节点之间无法通信，或者地址不是静态配置，请先参考[网络设置](https://community.t-firefly.com/docs/server/bmc-software/aBMC/subNetwork)完成配置。

### 检查防火墙端口 [step]

确保防火墙以及其他安全策略不会拦截 K3s 所需的流量，同时确认这些端口没有被其他程序占用，也没有与其冲突的端口映射规则。

K3s 默认使用以下端口：

| 端口          | 协议  | 用途                                      |
| ----------- | --- | --------------------------------------- |
| 6443        | TCP | K3s API Server，Server 与 Agent 节点间通信    |
| 8472        | UDP | Flannel VXLAN，节点之间通信                   |
| 10250       | TCP | Kubelet，Server 节点访问 Agent 节点           |
| 80 / 443    | TCP | Traefik Ingress（启用 Traefik/ServiceLB 时） |
| 30000-32767 | TCP | NodePort 服务端口                           |

### 检查系统时间 [step]

所有节点的系统时间应保持同步。

时间偏差过大时，可能会导致 TLS 证书校验失败、节点加入集群异常，也会让不同节点的日志时间对不上，增加问题排查的难度。

检查当前时间：

```bash
date
```

如果时间没有同步，请先参考[时间管理](https://community.t-firefly.com/docs/server/bmc-software/aBMC/timeManager)完成配置。

### 检查内核配置 [step]

完成网络、端口和时间配置后，还需要确认当前 Linux 内核是否满足 K3s 的运行要求。K3s 运行容器依赖 namespace、cgroup、网络和文件系统等内核能力，这些能力由内核配置决定，缺少时无法通过安装软件包补充，因此需要在安装 K3s 前检查，必要时重新编译内核。

K3s 自带 `check-config` 子命令，可以自动检查内核配置、cgroup、iptables 等条件，无需手工逐项比对。下载对应架构的 k3s 二进制（本环境为 `arm64`）并执行检查：

```bash
curl -sfLO https://github.com/k3s-io/k3s/releases/download/v1.36.4%2Bk3s1/k3s-arm64
chmod +x k3s-arm64
sudo ./k3s-arm64 check-config
```

节点上已经安装 K3s 时，也可以直接使用已安装的版本：

```bash
sudo k3s check-config
```

输出会逐项列出检查结果，最后一行是总体结论：

```text
System:
- /usr/sbin iptables v1.8.9 (legacy): ok
- swap: disabled
- routes: default CIDRs 10.42.0.0/16 or 10.43.0.0/16 already routed

info: reading kernel config from /proc/config.gz ...

Generally Necessary:
- cgroup hierarchy: cgroups V2 mounted, cpu|cpuset|memory controllers status: good
- CONFIG_NAMESPACES: enabled
- CONFIG_NET_NS: enabled
...

Storage Drivers:
- "overlay":
  - CONFIG_OVERLAY_FS: enabled

STATUS: pass
```

`STATUS: pass` 表示内核满足 K3s 的运行要求。如果为 `fail`，需要按输出中标记为 `missing` 的项调整内核配置，重新编译后再烧写；`Optional Features` 下缺失的项属于可选能力（例如加密网络相关内容），不影响总体结论。

## 容器配置

### Docker [step]

K3s 默认使用自带的 containerd 作为容器运行时。当前环境改用 Docker 作为容器运行时，并通过 `docker: true` 启用，因此**所有 K3s 节点都必须安装 Docker**。

**安装**

具体安装步骤请参阅 [Docker 安装](https://community.t-firefly.com/docs/software/other/Docker/docker-install)。

安装完成后，执行以下命令，确认 Docker 已正确安装且服务正常运行：

```bash
docker --version
sudo systemctl is-active docker
```

预期输出：

```text
Docker version 20.10.24+dfsg1, build 297e128
active
```

**迁移数据目录**

Firefly 设备默认启用 overlayroot。此时，`/` 由只读的根文件系统（`/root-ro`）和位于 `/userdata/rootfs_overlay` 的可写层组成，设备上只有 `/userdata` 是独立的 ext4 分区。

可以执行以下命令确认当前文件系统配置：

```bash
mount | grep overlayroot
```

Docker 默认将数据存储在 `/var/lib/docker`，该目录位于 overlayfs 的可写层中。而 Docker 的 `overlay2` 存储驱动不能直接建立在 overlayfs 之上，启动时可能出现 `failed to mount overlay: invalid argument` 错误，并尝试退回到已废弃的 devicemapper 驱动，甚至导致 Docker 启动失败。

因此，需要将 Docker 的数据目录迁移到独立的 `/userdata` 分区，例如 `/userdata/docker`。

创建数据目录：

```bash
sudo mkdir -p /userdata/docker
```

编辑 `/etc/docker/daemon.json`：

```bash
sudo tee /etc/docker/daemon.json >/dev/null <<'EOF'
{
  "data-root": "/userdata/docker",
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "50m",
    "max-file": "3"
  },
  "registry-mirrors": [
    "https://docker.m.daocloud.io",
    "https://docker.1ms.run",
    "https://docker-0.unsee.tech"
  ],
  "insecure-registries": [
    "172.16.55.55:5000"
  ],
  "experimental": true
}
EOF
```

主要配置项说明：

| 配置项                   | 说明                                                       |
| --------------------- | -------------------------------------------------------- |
| `data-root`           | 将 Docker 数据保存到 `/userdata/docker`，避免使用根文件系统的 overlay 可写层 |
| `log-driver`          | 使用 `json-file` 保存容器日志                                    |
| `max-size`            | 单个容器日志文件最大为 50 MB                                        |
| `max-file`            | 最多保留 3 个日志文件                                             |
| `registry-mirrors`    | Docker Hub 镜像加速地址，可根据实际网络环境配置                            |
| `insecure-registries` | 配置允许通过 HTTP 访问的私有镜像仓库，例如 `172.16.55.55:5000`             |
| `experimental`        | 开启 Docker 实验性功能                                          |

如果不需要使用私有镜像仓库，可以删除 `insecure-registries` 配置。

如果当前网络环境不需要 Docker Hub 镜像加速，也可以删除 `registry-mirrors` 配置。

**重启并验证**

配置完成后，重启 Docker：

```bash
sudo systemctl restart docker
sudo docker info
```

在输出中确认：

```text
Docker Root Dir: /userdata/docker
```

如果 `Docker Root Dir` 显示为 `/userdata/docker`，说明 Docker 数据目录配置已经生效。


### Containerd [step]

系统 containerd 默认的数据目录是 `/var/lib/containerd`，同样位于 overlayfs 上；containerd 的 overlay 快照器与 Docker 的 overlay2 驱动受同样的限制，无法建立在 overlayfs 之上，因此也需要把数据目录放到 ext4 分区 `/userdata`。

K3s 自带 containerd（内嵌在 k3s 进程中运行，不依赖系统 containerd），系统 containerd 只用于支撑 Docker，因此还需要禁用它的 CRI 插件，避免两者同时提供 CRI 而冲突。

**修改配置**

创建目录：

```bash
sudo mkdir -p /etc/containerd
sudo mkdir -p /userdata/containerd
```

编辑 `/etc/containerd/config.toml`：

```bash
sudo tee /etc/containerd/config.toml >/dev/null <<'EOF'
disabled_plugins = ["cri"]

root = "/userdata/containerd"
EOF
```

主要配置项说明：

| 配置项                | 说明                                                        |
| ------------------ | --------------------------------------------------------- |
| `disabled_plugins` | 禁用系统 containerd 的 CRI 插件，避免与 K3s 自带的 containerd 同时提供 CRI 而冲突 |
| `root`             | 将 containerd 数据目录指向 ext4 分区 `/userdata/containerd`，避免建立在 overlayfs 之上 |

**重启并验证**

配置完成后，重启 containerd：

```bash
sudo systemctl restart containerd
sudo systemctl is-active containerd
```

在输出中确认：

```text
active
```

## 特殊情况

### 限制 LightDM 实时调度权限 [step]

如果设备同时运行图形桌面环境，LightDM 可能使用较高的实时调度权限。

部分环境下，这会导致 K3s 创建容器时无法正常设置 CPU cgroup，Pod 可能一直处于 `CreateContainerError` 状态。

**只有在安装 K3s 后确实遇到这类问题时，才需要执行下面的配置。**

编辑 LightDM 服务：

```bash
sudo systemctl edit lightdm.service
```

添加：

```ini
[Service]
RestrictRealtime=yes
CPUSchedulingPolicy=other
CPUSchedulingPriority=0
```

保存后重新加载配置并重启 LightDM：

```bash
sudo systemctl daemon-reload
sudo systemctl restart lightdm.service
```

重启后再次检查 K3s Pod 状态。

如果设备没有安装 LightDM，或者 K3s 运行正常，则不需要进行这项配置。