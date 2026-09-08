# 系统需求

K3s 是一款轻量级 Kubernetes 发行版，但运行 K3s 及其组件仍需要满足一定的系统资源要求。

无论是在容器中运行 K3s，还是将其配置为原生 Linux 系统服务，每个节点都应满足以下最低要求。表中的资源仅用于 K3s 及其组件，不包括业务工作负载所需的额外资源。

## 前提条件

- 集群中的每个节点必须使用唯一的主机名。
- 如果多个节点使用相同的主机名，或者主机名可能被自动配置系统重复使用，请为每个节点配置唯一名称。添加节点时，也应使用对应的唯一主机名。

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

K3s 支持以容器方式运行，也支持作为原生 Linux 系统服务运行。采用容器方式部署时，每个节点都必须预先安装 Docker，并完成内核参数和容器运行环境检查。

### Docker

- 已安装 Docker Engine，并确保 Docker 服务已启动。
- 当前设备应使用与系统架构匹配的 Docker 版本和容器镜像。
- Docker 的存储目录应确保有足够的可用空间，用于保存镜像、容器和运行时数据。
- 安装或升级 Docker 前，请先按照官方文档完成内核兼容性检查：
  [Docker 内核检查与安装](https://community.t-firefly.com/docs/software/other/Docker/docker-kernel-check)。

可使用以下命令确认 Docker 已正常运行：

```bash
docker --version
sudo systemctl is-active docker
sudo docker info
```

如果使用非 root 用户执行 Docker 命令，请确保该用户已加入 `docker` 用户组，或在命令前使用 `sudo`。

### 部署前检查

在安装 K3s 前，请确认：

- 所有节点的主机名必须唯一，并且节点之间可以通过网络互相访问。如果节点之间无法通信，请参阅[网络设置](https://community.t-firefly.com/docs/server/bmc-software/aBMC/subNetwork)进行配置。
- 节点必须安装与部署方式匹配的容器运行时。
- Docker 服务必须处于运行状态，并且能够正常拉取或加载 K3s 所需的镜像。
- 防火墙和其他安全策略不得阻断 K3s API Server、节点间通信以及业务服务所需的端口。
- 所有节点的系统时间必须保持同步。如果时间不一致，请参阅[时间管理](https://community.t-firefly.com/docs/server/bmc-software/aBMC/timeManager)进行配置。

## 当前适配硬件

当前适配的服务器型号及其内部计算单元如下：

- **服务器**：CSB1-N10R3576 v1.3 × 1
  - **BMC**：Rockchip RK3588，Debian 12，8 GB 内存，64 GB 存储 × 1
  - **子板**：Rockchip RK3576，Debian 12，8 GB 内存，64 GB 存储 × 10
