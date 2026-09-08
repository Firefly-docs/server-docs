# K3s 集群验证

本文档用于验证 K3s 集群是否能够正常运行工作负载，并检查 Job、Deployment、Service 和副本扩缩容等基本功能。

> 以下命令均在运行 K3s Server 的 `bmc` 节点上执行。

## 验证内容

| 验证项目 | 验证目标 |
|---|---|
| 集群状态 | 确认 Server 和 Agent 节点已加入集群并处于 `Ready` 状态 |
| Job | 验证集群能够拉取镜像并执行短期任务 |
| Deployment | 验证持续运行的应用能够正常创建和管理 Pod |
| NodePort Service | 验证集群外部可以访问应用服务 |
| 副本扩缩容 | 验证 Deployment 能够按配置调整 Pod 副本数量 |

开始验证前，请确认 `k3s-server` 容器正在运行，并且节点已完成初始化。

## 查看集群状态

### 查看节点状态

确认 `bmc` 和 Agent 节点均已加入集群，并且节点状态为 `Ready`：

```bash
sudo docker exec k3s-server  kubectl get nodes -o wide
```

```shell
root@bmc:/# sudo docker exec k3s-server  kubectl get nodes -o wide
NAME    STATUS   ROLES           AGE    VERSION        INTERNAL-IP   EXTERNAL-IP   OS-IMAGE           KERNEL-VERSION    CONTAINER-RUNTIME
bmc     Ready    control-plane   41h    v1.36.3+k3s1   172.22.10.0   <none>        K3s v1.36.3+k3s1   6.1.118 (arm64)   containerd://2.3.2-k3s2
sub04   Ready    <none>          176m   v1.36.3+k3s1   172.22.14.0   <none>        K3s v1.36.3+k3s1   6.1.84 (arm64)    containerd://2.3.2-k3s2
```

## 创建 Job 验证短期任务

Job 适合执行一次性或短时间运行的任务。以下示例创建一个 Job，验证集群是否可以拉取镜像并运行容器。

### 创建 Job

```bash
sudo docker exec k3s-server  kubectl create job hello  --image=hello-world:latest
```

```shell
root@bmc:/# sudo docker exec k3s-server  kubectl create job hello  --image=hello-world:latest
job.batch/hello created

```

### 等待 Job 完成

等待 Job 成功完成：

```bash
sudo docker exec k3s-server  kubectl wait --for=condition=complete job/hello --timeout=180s
```

```shell
root@bmc:/# sudo docker exec k3s-server  kubectl wait --for=condition=complete job/hello --timeout=180s
job.batch/hello condition met
```

### 查看 Pod 的调度节点

确认 Job 创建的 Pod 已被调度到某个节点，并且状态为 `Completed`：

```bash
sudo docker exec k3s-server  kubectl get pods  -l job-name=hello -o wide
```

```shell
root@bmc:/# sudo docker exec k3s-server  kubectl get pods  -l job-name=hello -o wide
NAME          READY   STATUS      RESTARTS   AGE   IP          NODE    NOMINATED NODE   READINESS GATES
hello-klrx8   0/1     Completed   0          67s   10.42.1.5   sub04   <none>           <none>
```
### 查看容器输出

查看 Job 的容器日志，确认任务执行结果：

```bash
 sudo docker exec k3s-server  kubectl logs job/hello
```

```shell
root@bmc:/# sudo docker exec k3s-server  kubectl logs job/hello

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (arm64v8)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/


```

### 删除 Job

验证完成后删除测试 Job 及其 Pod：

```bash
sudo docker exec k3s-server  kubectl delete job hello
```

```shell
root@bmc:/# sudo docker exec k3s-server  kubectl delete job hello
job.batch "hello" deleted from default namespace
```

## 创建 Deployment 验证长期任务

Deployment 适合管理持续运行的应用，并能够维持指定数量的 Pod 副本。

### 创建 Deployment

```bash
sudo docker exec k3s-server  kubectl create deployment nginx --image=nginx:alpine --replicas=1
```

```shell
root@bmc:/# sudo docker exec k3s-server  kubectl create deployment nginx --image=nginx:alpine --replicas=1
deployment.apps/nginx created
```

### 查看 Deployment 状态

确认 Deployment 已达到期望副本数：

```shell
sudo docker exec k3s-server kubectl get deployment nginx
```

```shell
root@bmc:/# sudo docker exec k3s-server kubectl get deployment nginx
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   1/1     1            1           66s
```

### 查看 Deployment 的 Pod 分布

查看 NGINX Pod 的运行状态及其所在节点：

```shell
sudo docker exec k3s-server kubectl get pods -l app=nginx -o wide
```

```shell
root@bmc:/# sudo docker exec k3s-server kubectl get pods -l app=nginx -o wide
NAME                     READY   STATUS    RESTARTS   AGE   IP          NODE    NOMINATED NODE   READINESS GATES
nginx-85b9b8c477-7tsdv   1/1     Running   0          97s   10.42.1.8   sub04   <none>           <none>
```

### 暴露网络服务端口

通过 NodePort 将 NGINX 服务暴露到节点端口，便于从集群外部访问。

#### 创建 NodePort Service

```shell
sudo docker exec k3s-server kubectl expose deployment nginx --name=nginx --type=NodePort --port=80 --target-port=80
```

```shell
root@bmc:/# sudo docker exec k3s-server kubectl expose deployment nginx --name=nginx --type=NodePort --port=80 --target-port=80
service/nginx exposed
```
#### 查看分配的端口

记录 `PORT(S)` 列中的 NodePort 端口，例如 `32363`，后续使用该端口访问服务。

```shell
sudo docker exec k3s-server kubectl get service nginx
```

```shell
root@bmc:/# sudo docker exec k3s-server kubectl get service nginx
NAME    TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
nginx   NodePort   10.43.214.154   <none>        80:32363/TCP   90s
```

#### 访问 NGINX 服务

将下面命令中的 `32363` 替换为上一步实际分配的 NodePort 端口：

```bash
curl http://172.16.100.170:32363
```

示例返回内容：

```html
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, nginx is successfully installed and working.
Further configuration is required for the web server, reverse proxy, 
API gateway, load balancer, content cache, or other features.</p>

<p>For online documentation and support please refer to
<a href="https://nginx.org/">nginx.org</a>.<br/>
To engage with the community please visit
<a href="https://community.nginx.org/">community.nginx.org</a>.<br/>
For enterprise grade support, professional services, additional 
security features and capabilities please refer to
<a href="https://f5.com/nginx">f5.com/nginx</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

## 扩容和缩容 Deployment

通过调整 Deployment 的副本数，验证 K3s 的工作负载调度和副本管理能力。

### 缩容到 1 个副本

```shell
sudo docker exec k3s-server kubectl scale deployment nginx --replicas=1
```

### 扩容到 2 个副本

```shell
sudo docker exec k3s-server kubectl scale deployment nginx --replicas=2
```

### 观察副本变化

使用 `-w` 持续观察 Pod 的创建、调度和就绪过程：

```shell
sudo docker exec k3s-server kubectl get pods -l app=nginx -o wide -w
```

```shell
bmc@bmc:~$   sudo k3s kubectl get pods -l app=nginx -o wide -w
NAME                     READY   STATUS    RESTARTS   AGE   IP          NODE    NOMINATED NODE   READINESS GATES
nginx-85b9b8c477-58lzj   1/1     Running   0          16m   10.42.2.5   sub02   <none>           <none>
nginx-85b9b8c477-ps62k   1/1     Running   0          9s    10.42.3.4   sub01   <none>           <none>
```

