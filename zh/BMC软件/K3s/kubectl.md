# kubectl 常用命令

以下命令均在运行 K3s Server 的 `bmc` 节点上执行。由于 K3s Server 运行在 Docker 容器中，命令统一使用以下前缀：

```bash
sudo docker exec k3s-server kubectl
```

## 集群信息

| 命令 | 用途 |
|---|---|
| `sudo docker exec k3s-server kubectl cluster-info` | 查看集群 API Server 和核心组件信息 |
| `sudo docker exec k3s-server kubectl get nodes` | 查看节点列表、状态、角色和版本 |
| `sudo docker exec k3s-server kubectl get nodes -o wide` | 查看节点 IP、操作系统和容器运行时等详细信息 |
| `sudo docker exec k3s-server kubectl describe node <节点名称>` | 查看节点资源、标签、污点和事件 |

## Pod 管理

| 命令 | 用途 |
|---|---|
| `sudo docker exec k3s-server kubectl get pods` | 查看当前命名空间中的 Pod |
| `sudo docker exec k3s-server kubectl get pods -n <命名空间>` | 查看指定命名空间中的 Pod |
| `sudo docker exec k3s-server kubectl get pods -A` | 查看所有命名空间中的 Pod |
| `sudo docker exec k3s-server kubectl get pods -o wide` | 查看 Pod 的 IP 和所在节点 |
| `sudo docker exec k3s-server kubectl describe pod <Pod 名称>` | 查看 Pod 详情、事件和容器配置 |
| `sudo docker exec k3s-server kubectl logs <Pod 名称>` | 查看 Pod 日志 |
| `sudo docker exec k3s-server kubectl logs <Pod 名称> -f` | 持续跟踪 Pod 日志 |
| `sudo docker exec k3s-server kubectl logs <Pod 名称> -c <容器名称>` | 查看指定容器的日志 |
| `sudo docker exec k3s-server kubectl delete pod <Pod 名称>` | 删除指定 Pod；由 Deployment 管理的 Pod 会自动重建 |

## Service 管理

Service 用于为一组 Pod 提供稳定的访问入口。Pod 创建、删除或重启后，IP 地址可能发生变化，其他应用不应直接依赖 Pod IP。Service 会根据标签选择器找到对应的 Pod，并将请求转发到可用的 Pod。

简单来说：

- **Pod**：实际运行应用的容器实例。
- **Service**：为 Pod 提供稳定的名称和访问地址。
- **客户端**：通过 Service 访问应用，不需要关注 Pod 的具体 IP。

常见的 Service 类型如下：

| 类型 | 访问范围 | 适用场景 |
|---|---|---|
| `ClusterIP` | 仅集群内部 | 服务之间相互调用，默认类型 |
| `NodePort` | 集群外部通过节点 IP 和端口访问 | 测试或简单对外提供服务 |
| `LoadBalancer` | 通过负载均衡器对外提供服务 | 云环境或已配置负载均衡器的集群 |

在当前 K3s 环境中，可以使用 `NodePort` 将 Web 服务暴露给集群外部。例如，Service 将容器的 `80` 端口映射到节点上的一个端口后，可以通过 `http://<节点 IP>:<NodePort>` 访问应用。

| 命令 | 用途 |
|---|---|
| `sudo docker exec k3s-server kubectl get svc` | 查看当前命名空间中的 Service |
| `sudo docker exec k3s-server kubectl get svc -n <命名空间>` | 查看指定命名空间中的 Service |
| `sudo docker exec k3s-server kubectl get svc -A` | 查看所有命名空间中的 Service |
| `sudo docker exec k3s-server kubectl describe svc <Service 名称>` | 查看 Service 端口映射和后端 Pod |
| `sudo docker exec k3s-server kubectl expose deployment <部署名称> --type=NodePort --port=80 --target-port=80 --name=<Service 名称>` | 根据 Deployment 创建 NodePort Service |

## Deployment 管理

`Deployment` 用于管理需要持续运行的应用，例如 Web 服务、API 服务和后台服务。它通过 ReplicaSet 维护指定数量的 Pod 副本，并支持滚动更新、故障自动恢复和副本扩缩容。

适用场景：

- 长期运行的无状态服务
- 需要多个 Pod 副本的应用
- 需要滚动更新或快速回滚的应用

| 命令 | 用途 |
|---|---|
| `sudo docker exec k3s-server kubectl get deploy` | 查看当前命名空间中的 Deployment |
| `sudo docker exec k3s-server kubectl get deploy -n <命名空间>` | 查看指定命名空间中的 Deployment |
| `sudo docker exec k3s-server kubectl apply -f <YAML 文件>` | 根据 YAML 文件创建或更新资源 |
| `sudo docker exec k3s-server kubectl delete -f <YAML 文件>` | 根据 YAML 文件删除资源 |
| `sudo docker exec k3s-server kubectl scale deploy <部署名称> --replicas=<数量>` | 调整 Deployment 的 Pod 副本数量 |
| `sudo docker exec k3s-server kubectl rollout status deploy/<部署名称>` | 查看 Deployment 更新状态 |
| `sudo docker exec k3s-server kubectl rollout restart deploy/<部署名称>` | 重启 Deployment 管理的 Pod |

## Job 管理

`Job` 用于执行一次性或有限时长的任务，例如数据处理、初始化操作和批量任务。Job 会创建 Pod，并持续重试，直到任务成功完成或达到配置的重试次数。

适用场景：

- 一次性数据处理
- 数据库初始化或迁移
- 批量文件处理
- 集群或应用初始化任务

| 命令 | 用途 |
|---|---|
| `sudo docker exec k3s-server kubectl create job <Job 名称> --image=<镜像名称>` | 创建一次性 Job |
| `sudo docker exec k3s-server kubectl get jobs` | 查看当前命名空间中的 Job |
| `sudo docker exec k3s-server kubectl get jobs -A` | 查看所有命名空间中的 Job |
| `sudo docker exec k3s-server kubectl describe job <Job 名称>` | 查看 Job 状态、Pod 和事件 |
| `sudo docker exec k3s-server kubectl wait --for=condition=complete job/<Job 名称> --timeout=180s` | 等待 Job 执行完成 |
| `sudo docker exec k3s-server kubectl logs job/<Job 名称>` | 查看 Job 生成的 Pod 日志 |
| `sudo docker exec k3s-server kubectl delete job <Job 名称>` | 删除 Job 及其关联的 Pod |
| `sudo docker exec k3s-server kubectl apply -f <Job YAML 文件>` | 根据 YAML 文件创建或更新 Job |

## CronJob 管理

`CronJob` 用于按照预设的时间计划周期性创建 Job，例如定时备份、日志清理和周期性数据同步。每次调度会创建一个 Job，再由 Job 创建并运行 Pod。

适用场景：

- 定时备份
- 定时清理临时文件或日志
- 周期性数据同步
- 定时生成报表

`CronJob` 使用标准 Cron 表达式设置执行时间，例如 `*/5 * * * *` 表示每 5 分钟执行一次。

| 命令 | 用途 |
|---|---|
| `sudo docker exec k3s-server kubectl create cronjob <CronJob 名称> --image=<镜像名称> --schedule="*/5 * * * *"` | 创建按计划执行的 CronJob |
| `sudo docker exec k3s-server kubectl get cronjobs` | 查看当前命名空间中的 CronJob |
| `sudo docker exec k3s-server kubectl get cronjobs -A` | 查看所有命名空间中的 CronJob |
| `sudo docker exec k3s-server kubectl describe cronjob <CronJob 名称>` | 查看 CronJob 的调度计划、状态和事件 |
| `sudo docker exec k3s-server kubectl get jobs --sort-by=.metadata.creationTimestamp` | 查看 CronJob 创建的 Job |
| `sudo docker exec k3s-server kubectl get pods --sort-by=.metadata.creationTimestamp` | 查看 CronJob 创建的 Pod |
| `sudo docker exec k3s-server kubectl patch cronjob <CronJob 名称> -p '{"spec":{"suspend":true}}'` | 暂停 CronJob 的后续调度 |
| `sudo docker exec k3s-server kubectl patch cronjob <CronJob 名称> -p '{"spec":{"suspend":false}}'` | 恢复 CronJob 的调度 |
| `sudo docker exec k3s-server kubectl delete cronjob <CronJob 名称>` | 删除 CronJob 及其后续调度 |
| `sudo docker exec k3s-server kubectl apply -f <CronJob YAML 文件>` | 根据 YAML 文件创建或更新 CronJob |

## 命名空间和配置

命名空间（Namespace）用于在同一个 K3s 集群中划分相互隔离的资源范围。可以按照项目、团队或业务环境创建不同的命名空间，例如 `dev`、`test` 和 `prod`，避免不同应用的资源名称发生冲突，也便于进行权限和资源管理。

ConfigMap 和 Secret 用于保存应用运行所需的配置：

- **ConfigMap**：保存普通配置，例如环境变量、配置文件和服务地址。
- **Secret**：保存敏感信息，例如密码、Token 和证书。Secret 不是完整的加密存储方案，使用时仍应限制访问权限，避免将敏感内容提交到代码仓库。

创建应用时，可以在资源清单中通过 `metadata.namespace` 指定命名空间；执行命令时，可以使用 `-n <命名空间>` 指定目标命名空间。如果未指定命名空间，命令默认作用于当前命名空间，通常为 `default`。

| 命令 | 用途 |
|---|---|
| `sudo docker exec k3s-server kubectl get ns` | 查看所有命名空间 |
| `sudo docker exec k3s-server kubectl create ns <命名空间名称>` | 创建命名空间 |
| `sudo docker exec k3s-server kubectl get configmap` | 查看当前命名空间中的 ConfigMap |
| `sudo docker exec k3s-server kubectl get secret` | 查看当前命名空间中的 Secret |
| `sudo docker exec k3s-server kubectl get events -A --sort-by=.lastTimestamp` | 按时间顺序查看所有命名空间的事件 |

## 资源和故障排查

| 命令 | 用途 |
|---|---|
| `sudo docker exec k3s-server kubectl top nodes` | 查看节点 CPU 和内存使用情况 |
| `sudo docker exec k3s-server kubectl top pods -A` | 查看所有 Pod 的 CPU 和内存使用情况 |
| `sudo docker exec k3s-server kubectl get pods -A -o wide` | 查看所有 Pod 的状态、IP 和所在节点 |
| `sudo docker exec k3s-server kubectl describe pod <Pod 名称> -n <命名空间>` | 查看 Pod 的详细信息和异常事件 |
| `sudo docker logs --tail=200 k3s-server` | 查看 K3s Server 容器日志 |
