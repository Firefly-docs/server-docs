# 系统需求

K3s 是一款轻量级 Kubernetes 发行版，但运行 K3s 及其组件仍需要满足一定的系统资源要求。

无论是在容器中运行 K3s，还是将其配置为原生 Linux 系统服务，每个节点都应满足以下最低要求。表中的资源仅用于 K3s 及其组件，不包括业务工作负载所需的额外资源。

## 前提条件

- 集群中的每个节点都必须使用唯一的主机名。如果多个节点的主机名相同，或者主机名可能被自动配置系统重复使用，请为每个节点配置唯一名称，添加节点时也应使用对应的唯一主机名。

## CPU 和内存需求

以下为高可用 K3s Server 节点的最低 CPU 和内存需求：

| 部署规模 | 节点数 | vCPU | 内存 |
|---|---:|---:|---:|
| Small | 最多 10 个 | 2 核 | 4 GB |
| Medium | 最多 100 个 | 4 核 | 8 GB |
| Large | 最多 250 个 | 8 核 | 16 GB |
| X-Large | 最多 500 个 | 16 核 | 32 GB |
| XX-Large | 500 个以上 | 32 核 | 64 GB |

> 以上配置为 K3s Server 节点的最低资源基线。实际部署时，还应根据节点数量、Pod 数量、业务负载和监控组件等因素适当增加资源。

## 软件需求

K3s 运行 Pod 需要容器运行时。本方案使用 Docker 作为容器运行时，因此每个节点都必须安装 Docker，安装步骤请参阅[Docker 安装](https://community.t-firefly.com/docs/software/other/Docker/docker-install)。

### Docker

- Docker Engine 已安装，并且 Docker 服务处于运行状态。
- Docker 版本和使用的容器镜像与节点的 CPU 架构匹配，本环境为 `arm64`。
- Docker 的数据目录有足够的可用空间，用于保存镜像、容器和运行时数据。
- 使用非 root 用户执行 Docker 命令时，该用户需要先加入 `docker` 用户组，或者在命令前使用 `sudo`。

安装完成后，可使用以下命令确认 Docker 正常运行：

```bash
docker --version
sudo systemctl is-active docker
```

### 部署前检查

在安装 K3s 前，请确认：

- 每个节点的主机名唯一，并且节点之间可以互相访问。如果无法通信，请参阅[网络设置](https://community.t-firefly.com/docs/server/bmc-software/aBMC/subNetwork)进行配置。
- 所有节点都已安装 Docker，并且 Docker 服务处于运行状态。
- 节点能够正常拉取 K3s 和业务所需的容器镜像。
- 防火墙和其他安全策略不会阻断 K3s API Server、节点间通信以及业务服务所需的端口。
- 所有节点的系统时间保持同步。如果时间不一致，请参阅[时间管理](https://community.t-firefly.com/docs/server/bmc-software/aBMC/timeManager)进行配置。