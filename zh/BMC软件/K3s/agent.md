# Agent 部署

> 本文档中的所有命令均在 `sub04` 节点上执行。K3s Agent 将以 Docker 容器方式运行在 `sub04` 上，并加入运行在 `bmc` 节点上的 K3s Server。
>
> 本示例使用以下节点配置：
>
> - Server：`bmc`，API Server 地址为 `172.22.10.0:6443`
> - Agent：`sub04`，节点地址为 `172.22.14.0`

## 创建 K3s Agent 数据和配置目录

登录 `sub04` 节点，创建 K3s 数据目录和配置目录：

```bash
sudo mkdir -p /userdata/k3s
sudo mkdir -p /etc/rancher/k3s
```

## 创建 K3s Agent 配置文件

将以下内容写入 `/etc/rancher/k3s/config.yaml`：

```bash
sudo tee /etc/rancher/k3s/config.yaml >/dev/null <<'EOF'
server: https://172.22.10.0:6443
token-file: /run/secrets/k3s-token

data-dir: /var/lib/rancher/k3s
snapshotter: native

node-name: sub04
node-ip: 172.22.14.0
flannel-iface: eth1
EOF
```

配置说明：

- `server`：K3s Server 的 API Server 地址，请根据实际 Server 地址修改。
- `token-file`：指定 Agent 使用的注册 Token 文件路径。
- `data-dir`：K3s 在容器内使用的数据目录，该目录会映射到宿主机的 `/userdata/k3s`。
- `snapshotter: native`：使用 native snapshotter，适用于当前容器化部署环境。
- `node-name`：设置 Agent 节点名称，必须确保集群中的节点名称唯一。
- `node-ip`：设置 Agent 节点在集群中的内部 IP 地址，请根据实际网络配置修改。
- `flannel-iface`：指定 K3s 集群网络使用的网卡，请确认 `eth1` 为实际存在且可通信的网卡。

## 获取 K3s 注册 Token

在 K3s Server 节点上执行以下命令获取 Agent 注册所需的 Token：

```bash
sudo docker exec k3s-server cat /var/lib/rancher/k3s/server/node-token
```

请妥善保管该 Token，不要将真实 Token 提交到代码仓库或公开文档中。

## 配置 Agent Token

将从 `bmc` 节点获取的 Token 复制到 `sub04` 节点，并创建 Token 文件。将 `<K3S_NODE_TOKEN>` 替换为实际 Token：

```bash
sudo tee /etc/rancher/k3s/token >/dev/null <<'EOF'
<K3S_NODE_TOKEN>
EOF

sudo chmod 600 /etc/rancher/k3s/token
```

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

## 启动 K3s Agent

在 `sub04` 节点上使用 Docker 以特权容器方式启动 K3s Agent：

```bash
sudo docker run -d \
  --name k3s-agent \
  --restart=unless-stopped \
  --privileged \
  --network host \
  --cgroupns host \
  -v /etc/rancher/k3s/config.yaml:/etc/rancher/k3s/config.yaml:ro \
  -v /etc/rancher/k3s/registries.yaml:/etc/rancher/k3s/registries.yaml:ro \
  -v /etc/rancher/k3s/token:/run/secrets/k3s-token:ro \
  -v /etc/rancher/k3s/node:/etc/rancher/node \
  -v /userdata/k3s:/var/lib/rancher/k3s \
  -v /sys/fs/cgroup:/sys/fs/cgroup:rw \
  -v /lib/modules:/lib/modules:ro \
  rancher/k3s:v1.36.3-k3s1 \
  agent
```

启动后，查看 Agent 容器状态和日志：

```bash
sudo docker ps --filter name=k3s-agent
sudo docker logs --tail=100 k3s-agent
```

## 验证 Agent 节点

回到 K3s Server 节点，查看集群节点状态：

```bash
sudo docker exec k3s-server kubectl get nodes -o wide
```

当新节点状态显示为 `Ready` 时，表示 Agent 已成功加入集群。确认节点信息中的 `INTERNAL-IP`、节点名称和版本符合预期。

同时查看所有系统 Pod 的状态：

```bash
sudo docker exec k3s-server kubectl get pods -A -o wide
```

如果 Agent 长时间未显示为 `Ready`，或者 Pod 处于 `ContainerCreating`、`ImagePullBackOff` 或 `CrashLoopBackOff` 状态，请分别查看节点信息、事件和容器日志：

```bash
sudo docker exec k3s-server kubectl describe node sub04
sudo docker exec k3s-server kubectl get events -A --sort-by=.lastTimestamp
sudo docker logs --tail=200 k3s-agent
```
