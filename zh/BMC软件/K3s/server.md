# Server 部署

> 本文档中的所有命令均在 `bmc` 节点上执行。K3s Server 将以 Docker 容器方式运行在 `bmc` 上。

## 创建 K3s 数据和配置目录

在 `bmc` 节点上创建 K3s 的持久化数据目录和配置目录：

```bash
sudo mkdir -p /userdata/k3s
sudo mkdir -p /etc/rancher/k3s
```

## 创建 K3s Server 配置文件

将以下内容写入 `/etc/rancher/k3s/config.yaml`：

```bash
sudo tee /etc/rancher/k3s/config.yaml >/dev/null <<'EOF'
data-dir: /var/lib/rancher/k3s
snapshotter: native

node-name: bmc
node-ip: 172.22.10.0
flannel-iface: nic0
tls-san:
  - 172.22.10.0
  - 172.16.100.170
EOF
```

配置说明：

- `data-dir`：K3s 在容器内使用的数据目录，该目录会映射到宿主机的 `/userdata/k3s`。
- `snapshotter: native`：使用 native snapshotter，适用于当前容器化部署环境。
- `node-name`：设置节点名称，集群中的每个节点都必须使用唯一名称。
- `node-ip`：设置节点在集群中的内部 IP 地址，请根据实际网络配置修改。
- `flannel-iface`：指定 K3s 集群网络使用的网卡，请确认 `nic0` 为实际存在且可通信的网卡。
- `tls-san`：添加 K3s API Server 证书中的访问地址。通过其他地址访问 API Server 时，应将对应地址加入此列表。

## 配置 Docker Hub 镜像源

如果节点访问 Docker Hub 较慢或受限，可以配置镜像源：

```bash
sudo tee /etc/rancher/k3s/registries.yaml >/dev/null <<'EOF'
mirrors:
  docker.io:
    endpoint:
      - https://docker.m.daocloud.io
EOF

sudo chmod 600 /etc/rancher/k3s/registries.yaml
```

## 调整 LightDM 实时调度权限

部分设备上的图形服务可能使用较高的实时调度权限，导致 K3s 容器无法正常创建 CPU cgroup。可以通过 systemd 覆盖配置限制 LightDM 的实时调度权限：

```bash
sudo systemctl edit lightdm.service
```

在打开的编辑器中写入：

```ini
[Service]
RestrictRealtime=yes
CPUSchedulingPolicy=other
CPUSchedulingPriority=0
```

保存后重新加载 systemd 配置并重启 LightDM：

```bash
sudo systemctl daemon-reload
sudo systemctl restart lightdm.service
```

如果设备未安装或未启用 LightDM，可跳过此步骤。

## 启动 K3s Server

使用 Docker 以特权容器方式启动 K3s Server：

```bash
sudo docker run -d \
  --name k3s-server \
  --restart=unless-stopped \
  --privileged \
  --network host \
  --cgroupns host \
  -v /etc/rancher/k3s/config.yaml:/etc/rancher/k3s/config.yaml:ro \
  -v /etc/rancher/k3s/registries.yaml:/etc/rancher/k3s/registries.yaml:ro \
  -v /userdata/k3s:/var/lib/rancher/k3s \
  -v /sys/fs/cgroup:/sys/fs/cgroup:rw \
  -v /lib/modules:/lib/modules:ro \
  rancher/k3s:v1.36.3-k3s1 \
  server
```

在 `bmc` 节点启动 K3s Server 后，可以使用以下命令查看容器状态和启动日志：

```bash
sudo docker ps --filter name=k3s-server
sudo docker logs --tail=100 k3s-server
```

## 验证节点状态

等待 K3s 完成初始化后，查看 Server 节点状态：

```bash
sudo docker exec k3s-server kubectl get nodes -o wide
```

当节点状态显示为 `Ready` 时，表示 K3s Server 已正常启动。示例：

```text
NAME   STATUS   ROLES           AGE     VERSION        INTERNAL-IP   EXTERNAL-IP   OS-IMAGE           KERNEL-VERSION   CONTAINER-RUNTIME
bmc    Ready    control-plane   6m11s   v1.36.3+k3s1   172.22.10.0   <none>        K3s v1.36.3+k3s1   6.1.118 (arm64)   containerd://2.3.2-k3s2
```

## 验证系统 Pod

查看 K3s 系统命名空间中的 Pod：

```bash
sudo docker exec k3s-server kubectl get pods -A -o wide
```

首次启动时，CoreDNS、Traefik、Local Path Provisioner 和 Metrics Server 等组件可能处于 `ContainerCreating` 状态。等待镜像拉取和容器启动完成后，相关 Pod 应显示为 `Running`，并且 `READY` 列应显示为完整就绪状态，例如 `1/1`。

如果 Pod 长时间处于 `ContainerCreating`、`ImagePullBackOff` 或 `CrashLoopBackOff` 状态，请查看 Pod 事件和 K3s 日志：

```bash
sudo docker exec k3s-server kubectl get events -A --sort-by=.lastTimestamp
sudo docker logs --tail=200 k3s-server
```
