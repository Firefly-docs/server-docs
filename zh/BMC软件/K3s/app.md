# 部署一个简单应用

本章以 Nginx 为例，在已完成[在线部署](install.md)的 K3s 集群上部署一个简单应用，涉及宿主机目录挂载（`hostPath`）、Service 与 Ingress 等常用资源。

* **部署内容**：通过一个 YAML 文件创建 `Namespace`、`Deployment`、`Service` 和 `Ingress` 四类资源，并用 `hostPath` 把宿主机目录挂载给容器。
* **访问方式**：通过 NodePort 和 Ingress 两种方式访问 Nginx 应用。
* **前置条件**：集群中至少有一个 `Ready` 节点，且该节点能够拉取 Nginx 镜像（离线环境需要提前导入）。

## 确认节点状态 [step]

部署应用前，先确认集群中至少有一个节点处于 `Ready` 状态：

```bash
sudo k3s kubectl get nodes
```

```text
NAME    STATUS     ROLES           AGE     VERSION
bmc     Ready      control-plane   5d18h   v1.36.4+k3s1
sub11   Ready      <none>          5d15h   v1.36.4+k3s1
sub12   NotReady   <none>          47h     v1.36.4+k3s1
```

## 创建应用清单 [step]

将下面的内容保存为 `demo-app.yaml`：

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: demo                             # 独立命名空间，便于统一管理和清理
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: demo-app
  namespace: demo
spec:
  replicas: 1                            # 运行 1 个 Pod
  selector:
    matchLabels:
      app: demo-app
  template:
    metadata:
      labels:
        app: demo-app                    # Service 通过该标签找到 Pod
    spec:
      nodeSelector:
        kubernetes.io/hostname: sub11    # hostPath 是节点本地目录，固定 Pod 所在节点
      containers:
        - name: nginx
          image: nginx:1.27-alpine       # 需与节点 CPU 架构匹配，本环境为 arm64
          ports:
            - containerPort: 80          # Nginx 在容器内监听 80 端口
          volumeMounts:
            - name: data
              mountPath: /usr/share/nginx/html   # 网页目录
            - name: config
              mountPath: /etc/nginx/conf.d       # Nginx 配置目录
          resources:
            requests:
              cpu: 100m                  # 调度 Pod 时预留的 CPU
              memory: 64Mi               # 调度 Pod 时预留的内存
            limits:
              cpu: 500m                  # Pod 最多使用 500m CPU
              memory: 256Mi              # Pod 最多使用 256Mi 内存
      volumes:
        - name: data
          hostPath:
            path: /userdata/container/nginx_data/data_0     # 网页文件目录
            type: DirectoryOrCreate   # 目录不存在时由 kubelet 自动创建
        - name: config
          hostPath:
            path: /userdata/container/nginx_data/config_0   # Nginx 配置目录
            type: DirectoryOrCreate
---
apiVersion: v1
kind: Service
metadata:
  name: demo-svc
  namespace: demo
spec:
  type: NodePort                         # 集群外通过“节点 IP + 端口”访问应用
  selector:
    app: demo-app                        # 转发到带有 app=demo-app 标签的 Pod
  ports:
    - name: http
      port: 80                           # Service 在集群内提供的端口
      targetPort: 80                     # 转发到 Pod 的 80 端口
      nodePort: 30080                    # 集群外通过 <节点 IP>:30080 访问
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: demo-ingress
  namespace: demo
spec:
  ingressClassName: traefik              # 使用 K3s 内置的 Traefik 处理 Ingress 请求
  rules:
    - host: demo.local                   # 请求 Host 为 demo.local 时匹配此规则
      http:
        paths:
          - path: /
            pathType: Prefix             # 匹配 / 下的所有请求
            backend:
              service:
                name: demo-svc           # 将请求转发到 demo-svc
                port:
                  number: 80             # 使用 Service 的 80 端口
```

## 部署应用 [step]

```bash
sudo k3s kubectl apply -f demo-app.yaml
```

```shell
sudo k3s kubectl apply -f demo-app.yaml
namespace/demo created
deployment.apps/demo-app created
service/demo-svc created
ingress.networking.k8s.io/demo-ingress created
```

查看创建的资源：

```bash
sudo k3s kubectl -n demo get all,ingress -o wide
```

```shell
sudo k3s kubectl -n demo get all,ingress -o wide
NAME                           READY   STATUS    RESTARTS   AGE   IP           NODE    NOMINATED NODE   READINESS GATES
pod/demo-app-8f47c9767-2jq88   1/1     Running   0          3s    10.42.1.58   sub11   <none>           <none>

NAME               TYPE       CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE   SELECTOR
service/demo-svc   NodePort   10.43.99.4   <none>        80:30080/TCP   4s    app=demo-app

NAME                       READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES              SELECTOR
deployment.apps/demo-app   1/1     1            1           4s    nginx        nginx:1.27-alpine   app=demo-app

NAME                                     CLASS     HOSTS        ADDRESS                         PORTS   AGE
ingress.networking.k8s.io/demo-ingress   traefik   demo.local   172.16.100.176,172.16.100.177   80      5s
```

确认 Pod 的 `STATUS` 为 `Running`、`NODE` 为 `sub11`。

## 访问应用 [step]

`hostPath` 只在 Pod 所在节点上有效：写入文件需要在 `sub11` 上执行，重新加载配置可以在 `bmc` 上执行。

**在 sub11 节点写入配置与网页文件**

```bash
sudo tee /userdata/container/nginx_data/config_0/default.conf >/dev/null <<'EOF'
server {
    listen 80;
    root /usr/share/nginx/html;
    index index.html;
}
EOF

echo hello-from-hostpath | sudo tee /userdata/container/nginx_data/data_0/index.html
```

**在 bmc 节点重新加载 Nginx 配置**

```bash
sudo k3s kubectl -n demo exec deploy/demo-app -- nginx -s reload
```

### 通过 NodePort 访问 [step]

Service 使用 `30080` 作为 NodePort，因此可以通过节点 IP 和端口访问 Nginx：

```bash
curl http://172.16.100.177:30080/
```

```bash
curl http://172.16.100.177:30080/
hello-from-hostpath
```

NodePort 会在每台节点上监听，因此用 `bmc` 的地址访问也能到达 `sub11` 上的这个 Pod：

```bash
curl http://172.16.100.176:30080/
```

```bash
curl http://172.16.100.176:30080/
hello-from-hostpath
```

### 通过 Ingress 访问 [step]

```bash
curl -H 'Host: demo.local' http://172.16.100.177/
```

```shell
curl -H 'Host: demo.local' http://172.16.100.177/
hello-from-hostpath
```

### 从集群内部访问 [step]

在集群内部，可以通过 Service 的 DNS 名称访问 Nginx：

```bash
sudo k3s kubectl -n demo exec deploy/demo-app -- \
  sh -c 'wget -qO- http://demo-svc.demo.svc.cluster.local/'
```

```text
hello-from-hostpath
```

## 清单说明 [step]

<div style={{ border: '1px solid var(--color-fd-border,#d1d5db)', borderRadius: '8px', padding: '16px', fontSize: '14px', lineHeight: 1.8 }}>
<div style={{ textAlign: 'center', fontWeight: 600 }}>客户端（集群外）</div>
<div style={{ display: 'grid', gridTemplateColumns: 'repeat(auto-fit,minmax(220px,1fr))', gap: '12px', marginTop: '12px' }}>
<div style={{ border: '1px solid var(--color-fd-border,#d1d5db)', borderRadius: '6px', padding: '10px' }}>
  <div style={{ fontWeight: 600 }}>节点端口 30080</div>
  <div style={{ color: 'var(--color-fd-muted-foreground,#6b7280)' }}>每台节点都监听，由 kube-proxy 转发</div>
  <div style={{ marginTop: '6px', fontFamily: 'ui-monospace,SFMono-Regular,Menlo,monospace', fontSize: '12px' }}>
    curl http://172.16.100.176:30080/<br/>
    curl http://172.16.100.177:30080/
  </div>
</div>
<div style={{ border: '1px solid var(--color-fd-border,#d1d5db)', borderRadius: '6px', padding: '10px' }}>
  <div style={{ fontWeight: 600 }}>Ingress 规则</div>
  <div style={{ color: 'var(--color-fd-muted-foreground,#6b7280)' }}>Host: demo.local → demo-svc:80，由 Traefik 实现，每台节点 80 端口</div>
  <div style={{ marginTop: '6px', fontFamily: 'ui-monospace,SFMono-Regular,Menlo,monospace', fontSize: '12px' }}>
    curl -H 'Host: demo.local' http://172.16.100.176/<br/>
    curl -H 'Host: demo.local' http://172.16.100.177/
  </div>
</div>
<div style={{ border: '1px solid var(--color-fd-border,#d1d5db)', borderRadius: '6px', padding: '10px' }}>
  <div style={{ fontWeight: 600 }}>集群内其它 Pod</div>
  <div style={{ color: 'var(--color-fd-muted-foreground,#6b7280)' }}>通过 Service DNS 访问，不经过节点端口与 Ingress</div>
  <div style={{ marginTop: '6px', fontFamily: 'ui-monospace,SFMono-Regular,Menlo,monospace', fontSize: '12px' }}>
    wget -qO- http://demo-svc.demo.svc.cluster.local/
  </div>
</div>
</div>
<div style={{ textAlign: 'center', color: 'var(--color-fd-muted-foreground,#6b7280)', margin: '12px 0' }}>↓ 三种入口最终都转发到 Service 的后端 Pod</div>
<div style={{ border: '1px solid var(--color-fd-border,#d1d5db)', borderRadius: '6px', padding: '10px', textAlign: 'center' }}>
  <span style={{ fontWeight: 600 }}>Service demo-svc</span>
  <span style={{ color: 'var(--color-fd-muted-foreground,#6b7280)' }}>　ClusterIP:80，Endpoints → Pod IP</span>
</div>
<div style={{ textAlign: 'center', color: 'var(--color-fd-muted-foreground,#6b7280)', margin: '8px 0' }}>↓ 转发到容器 80 端口</div>
<div style={{ border: '1px solid var(--color-fd-border,#d1d5db)', borderRadius: '6px', padding: '10px', textAlign: 'center' }}>
  <span style={{ fontWeight: 600 }}>Pod（调度到 sub11）</span>
  <span style={{ color: 'var(--color-fd-muted-foreground,#6b7280)' }}>　nginx 容器，监听 80 端口</span>
</div>
<div style={{ textAlign: 'center', color: 'var(--color-fd-muted-foreground,#6b7280)', margin: '8px 0' }}>↓ hostPath 挂载（sub11 宿主机目录）</div>
<div style={{ display: 'grid', gridTemplateColumns: 'repeat(auto-fit,minmax(240px,1fr))', gap: '12px' }}>
<div style={{ border: '1px dashed var(--color-fd-border,#d1d5db)', borderRadius: '6px', padding: '10px' }}>
  <div style={{ fontFamily: 'ui-monospace,SFMono-Regular,Menlo,monospace', fontSize: '12px' }}>/userdata/container/nginx_data/data_0</div>
  <div style={{ color: 'var(--color-fd-muted-foreground,#6b7280)' }}>网页文件 → /usr/share/nginx/html</div>
</div>
<div style={{ border: '1px dashed var(--color-fd-border,#d1d5db)', borderRadius: '6px', padding: '10px' }}>
  <div style={{ fontFamily: 'ui-monospace,SFMono-Regular,Menlo,monospace', fontSize: '12px' }}>/userdata/container/nginx_data/config_0</div>
  <div style={{ color: 'var(--color-fd-muted-foreground,#6b7280)' }}>Nginx 配置文件 → /etc/nginx/conf.d</div>
</div>
</div>
</div>

| 资源 | 关键配置 | 作用 |
|---|---|---|
| `Namespace` | `name: demo` | 隔离本次应用的全部资源，删除命名空间即可清理 |
| `Deployment` | `image`、`replicas`、`nodeSelector`、`resources` | 运行 Nginx Pod，并用 `nodeSelector` 固定到 `sub11` |
| `Deployment.volumes` | `hostPath`、`type: DirectoryOrCreate` | 把宿主机的 `data_0`、`config_0` 挂进容器，目录不存在时自动创建 |
| `Service` | `type: NodePort`、`nodePort: 30080` | 为 Pod 提供固定入口，并在每台节点上监听 `30080` |
| `Ingress` | `host: demo.local`、`ingressClassName: traefik` | 按域名把请求转发到 `demo-svc`，由 Traefik 处理 |

## 清理应用 [step]

删除命名空间会一并删除 `Deployment`、`Service`、`Ingress` 等集群资源，但 **`hostPath` 目录属于宿主机，不会被删除**，需要单独清理。

**在 bmc 节点删除集群资源**

```bash
sudo k3s kubectl delete namespace demo
```

```text
namespace "demo" deleted
```

**在 sub11 节点删除宿主机数据**

数据在删除命名空间后仍然保留，确认确实不需要后再删除；需要保留时先备份：

```bash
sudo ls -l /userdata/container/nginx_data/data_0/

# 备份
sudo tar czf /userdata/nginx_data-backup.tar.gz -C /userdata/container nginx_data

# 删除
sudo rm -rf /userdata/container/nginx_data
```
