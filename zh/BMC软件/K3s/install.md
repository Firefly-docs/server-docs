# K3s 部署

本章节介绍三种部署方式，部署版本统一为 K3s `v1.36.4+k3s1`：

- **在线部署**：在可联网的节点上安装 K3s，使用 Docker 作为容器运行时。
- **离线部署**：在内网节点上安装 K3s，同样使用 Docker 作为容器运行时，安装包与镜像需要提前准备。
- **Docker 部署**：K3s 本身以 Docker 容器方式启动，容器运行时使用 K3s 自带的 containerd。

## Server 节点部署

<CodeBlockTabs defaultValue="在线部署">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="在线部署">在线部署</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="离线部署">离线部署</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="Docker部署">Docker部署</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="在线部署">

    ### 创建数据和配置目录 [step]

    ```bash
    sudo mkdir -p /userdata/k3s
    sudo chmod 700 /userdata/k3s
    sudo mkdir -p /etc/rancher/k3s
    ```

    ### 配置 K3s [step]

    ```bash
    sudo tee /etc/rancher/k3s/config.yaml >/dev/null <<'EOF'
    data-dir: /userdata/k3s
    snapshotter: native
    node-ip: 172.16.100.176
    docker: true
    flannel-iface: MGMT
    tls-san:
      - 172.16.100.176
    EOF
    ```

    各配置项含义：

    - `data-dir`：K3s 数据目录，放在 `/userdata` 数据分区，避免写入 overlay 可写层。
    - `snapshotter: native`：使用 native snapshotter。
    - `node-ip`、`flannel-iface`：节点在集群中的地址和集群网络使用的网口，需按实际环境修改。
    - `docker: true`：使用 Docker 作为容器运行时。
    - `tls-san`：加入 API Server 证书的访问地址；通过其他地址访问时，需要一并添加。

    ### 配置 Docker Hub 镜像源 [step]

    ```bash
    sudo tee /etc/rancher/k3s/registries.yaml >/dev/null <<'EOF'
    mirrors:
      docker.io:
        endpoint:
          - https://docker.m.daocloud.io
    EOF

    sudo chmod 600 /etc/rancher/k3s/registries.yaml
    ```

    确认镜像站可以访问：

    ```bash
    curl -IL --connect-timeout 10 https://docker.m.daocloud.io/v2/
    ```

    ```shell
    bmc@bmc:~$ curl -IL --connect-timeout 10 https://docker.m.daocloud.io/v2/
    HTTP/2 401
    server: nginx
    date: Fri, 18 Sep 2026 07:17:34 GMT
    content-type: application/json; charset=utf-8
    content-length: 73
    www-authenticate: Bearer realm="https://m.daocloud.io/auth/token",service="docker.m.daocloud.io"
    docker-distribution-api-version: registry/2.0
    ```

    返回 `401` 表示镜像站可以正常访问；该接口需要认证，属于正常响应。

    使用 Docker 作为容器运行时（`docker: true`）时，Pod 镜像由 Docker 拉取，还需要在 `/etc/docker/daemon.json` 中配置 `registry-mirrors`，否则系统 Pod 会因拉取镜像超时而停留在 `ContainerCreating`：

    ```bash
    sudo tee /etc/docker/daemon.json >/dev/null <<'EOF'
    {
      "registry-mirrors": [
        "https://docker.m.daocloud.io",
        "https://docker.1ms.run",
        "https://docker-0.unsee.tech"
      ]
    }
    EOF

    sudo systemctl restart docker
    ```

    `registries.yaml` 只作用于 K3s 自带的 containerd，不影响 Docker 的镜像拉取。

    ### 安装 K3s Server [step]

    国内环境使用国区脚本，可以加快二进制下载：

    ```bash
    curl -sfL https://rancher-mirror.rancher.cn/k3s/k3s-install.sh | \
      sudo env INSTALL_K3S_MIRROR=cn INSTALL_K3S_EXEC=server INSTALL_K3S_VERSION=v1.36.4+k3s1 sh -
    ```

    其他环境：

    ```bash
    curl -sfL https://get.k3s.io | \
      sudo env INSTALL_K3S_EXEC=server INSTALL_K3S_VERSION=v1.36.4+k3s1 sh -
    ```

    ### 检查 K3s 服务 [step]

    ```bash
    sudo systemctl status k3s --no-pager -l
    ```

    服务未正常启动时，查看最近的日志：

    ```bash
    sudo journalctl -u k3s -n 200 --no-pager
    ```

    ### 等待节点和系统 Pod 就绪 [step]

    查看节点状态，`STATUS` 一列应为 `Ready`：

    ```bash
    sudo k3s kubectl get nodes -o wide
    ```

    ```shell
    bmc@bmc:~$ sudo k3s kubectl get nodes -o wide
    NAME   STATUS   ROLES           AGE     VERSION        INTERNAL-IP      EXTERNAL-IP   OS-IMAGE                           KERNEL-VERSION    CONTAINER-RUNTIME
    bmc    Ready    control-plane   6m11s   v1.36.4+k3s1   172.16.100.176   <none>        Debian GNU/Linux 12 (bookworm)     6.1.118 (arm64)   docker://20.10.24+dfsg1
    ```

    `CONTAINER-RUNTIME` 显示为 `docker://`，表示 Pod 使用 Docker 运行，与配置中的 `docker: true` 对应。

    查看系统 Pod：

    ```bash
    sudo k3s kubectl get pods -A -o wide
    ```

    首次启动需要拉取镜像，Pod 可能短暂处于 `ContainerCreating`；最终应看到以下组件进入 `Running` 状态：

    ```shell
    bmc@bmc:~$ sudo k3s kubectl get pods -A -o wide
    NAMESPACE     NAME                                      READY   STATUS      RESTARTS      AGE   IP           NODE   NOMINATED NODE   READINESS GATES
    kube-system   coredns-54996dc9b4-mdgm2                  1/1     Running     0             46m   10.42.0.74   bmc    <none>           <none>
    kube-system   helm-install-traefik-crd-ssc5b            0/1     Completed   0             46m   <none>       bmc    <none>           <none>
    kube-system   helm-install-traefik-rmwmk                0/1     Completed   2 (38m ago)   46m   <none>       bmc    <none>           <none>
    kube-system   local-path-provisioner-58d557dc48-ns4rp   1/1     Running     0             46m   10.42.0.72   bmc    <none>           <none>
    kube-system   metrics-server-6dc596dfb8-rvpbx           1/1     Running     0             46m   10.42.0.71   bmc    <none>           <none>
    kube-system   svclb-traefik-9ba443c2-qn7m4              2/2     Running     0             38m   10.42.0.75   bmc    <none>           <none>
    kube-system   traefik-59b7647586-kbgz2                  1/1     Running     0             38m   10.42.0.76   bmc    <none>           <none>
    ```
  </CodeBlockTab>
  <CodeBlockTab value="离线部署">
  
    ### 准备离线包（联网机器） [step]

    在一台可以联网的机器上创建离线包目录，下载安装脚本、arm64 二进制和系统组件镜像包：

    ```bash
    mkdir -p ~/k3s-offline && cd ~/k3s-offline

    curl -sfLO https://rancher-mirror.rancher.cn/k3s/k3s-install.sh
    curl -sfLO https://rancher-mirror.rancher.cn/k3s/v1.36.4-k3s1/k3s-arm64
    curl -sfLO https://rancher-mirror.rancher.cn/k3s/v1.36.4-k3s1/sha256sum-arm64.txt
    curl -sfLO https://github.com/k3s-io/k3s/releases/download/v1.36.4%2Bk3s1/k3s-airgap-images-arm64.tar
    ```

    其中 `k3s-airgap-images-arm64.tar` 约 169 MB，包含 K3s 系统组件所需的全部镜像；`sha256sum-arm64.txt` 仅用于手工校验二进制，离线安装时脚本不会读取它。

    与官方哈希比对，确认二进制完整：

    ```bash
    grep -E " k3s-arm64$" sha256sum-arm64.txt
    sha256sum k3s-arm64
    ```

    下载得到的脚本没有执行权限，需要手动添加：

    ```bash
    chmod +x k3s-install.sh
    ```

    ### 拷贝离线包到节点 [step]

    将整个 `k3s-offline` 目录拷贝到目标节点：

    ```bash
    scp -r ~/k3s-offline bmc@172.16.100.176:/home/bmc/
    ```

    ### 放置二进制和配置 [step]

    ```bash
    sudo mkdir -p /userdata/k3s /etc/rancher/k3s
    sudo chmod 700 /userdata/k3s
    sudo install -m 755 ~/k3s-offline/k3s-arm64 /usr/local/bin/k3s
    ```

    ```bash
    sudo tee /etc/rancher/k3s/config.yaml >/dev/null <<'EOF'
    data-dir: /userdata/k3s
    snapshotter: native
    node-ip: 172.16.100.176
    docker: true
    flannel-iface: MGMT
    tls-san:
      - 172.16.100.176
    EOF
    ```

    ### 导入镜像包 [step]

    ```bash
    sudo docker load -i ~/k3s-offline/k3s-airgap-images-arm64.tar
    ```

    ### 执行离线安装 [step]

    ```bash
    cd ~/k3s-offline
    sudo env INSTALL_K3S_SKIP_DOWNLOAD=true INSTALL_K3S_EXEC=server ./k3s-install.sh
    ```

    `INSTALL_K3S_SKIP_DOWNLOAD=true` 表示不下载二进制，直接使用已放置到 `/usr/local/bin/k3s` 的文件：

    ```shell
    bmc@bmc:~$ sudo env INSTALL_K3S_SKIP_DOWNLOAD=true INSTALL_K3S_EXEC=server ./k3s-install.sh
    [INFO]  Skipping k3s download and verify
    [INFO]  Creating /usr/local/bin/kubectl symlink to k3s
    [INFO]  systemd: Enabling k3s unit
    [INFO]  systemd: Starting k3s
    ```

    ### 检查服务与节点 [step]

    ```bash
    sudo systemctl status k3s --no-pager -l
    sudo k3s kubectl get nodes -o wide
    ```

    节点进入 `Ready` 即安装完成：

    ```shell
    NAME   STATUS   ROLES           AGE   VERSION        INTERNAL-IP      OS-IMAGE                         KERNEL-VERSION    CONTAINER-RUNTIME
    bmc    Ready    control-plane   72s   v1.36.4+k3s1   172.16.100.176   Debian GNU/Linux 12 (bookworm)   6.1.118 (arm64)   docker://29.7.2
    ```

    ### 等待系统 Pod 就绪 [step]

    ```bash
    sudo k3s kubectl get pods -A -o wide
    ```

    ```shell
    NAMESPACE     NAME                                      READY   STATUS      RESTARTS        AGE     IP          NODE   NOMINATED NODE   READINESS GATES
    kube-system   coredns-54996dc9b4-bkbl7                  1/1     Running     0               7m53s   10.42.0.5   bmc    <none>           <none>
    kube-system   helm-install-traefik-75cvd                0/1     Completed   2 (2m29s ago)   7m44s   10.42.0.4   bmc    <none>           <none>
    kube-system   helm-install-traefik-crd-xwhdg            0/1     Completed   0               7m44s   10.42.0.2   bmc    <none>           <none>
    kube-system   local-path-provisioner-77b9867795-fxbb5   1/1     Running     0               7m53s   10.42.0.6   bmc    <none>           <none>
    kube-system   metrics-server-6dc596dfb8-kxh25           1/1     Running     0               7m51s   10.42.0.3   bmc    <none>           <none>
    kube-system   svclb-traefik-6ba5904a-z9xtj              2/2     Running     0               2m14s   10.42.0.7   bmc    <none>           <none>
    kube-system   traefik-59b7647586-twzcb                  1/1     Running     0               2m14s   10.42.0.8   bmc    <none>           <none>
    ```

  </CodeBlockTab>
  <CodeBlockTab value="Docker部署">

    ### 创建数据和配置目录 [step]

    ```bash
    sudo mkdir -p /userdata/k3s
    sudo chmod 700 /userdata/k3s
    sudo mkdir -p /etc/rancher/k3s
    ```

    ### 配置 K3s [step]

    ```bash
    sudo tee /etc/rancher/k3s/config.yaml >/dev/null <<'EOF'
    data-dir: /var/lib/rancher/k3s
    snapshotter: native
    node-name: bmc
    node-ip: 172.16.100.176
    flannel-iface: MGMT
    tls-san:
      - 172.16.100.176
    EOF
    ```

    各配置项含义：

    - `data-dir`：容器内使用的数据目录，通过下方挂载映射到宿主机的 `/userdata/k3s`，因此这里填写容器内路径。
    - `snapshotter: native`：使用 native snapshotter。
    - `node-name`：容器内的主机名不是物理主机名，需要显式指定节点名称。
    - `node-ip`、`flannel-iface`：节点在集群中的地址和集群网络使用的网口，需按实际环境修改。
    - `tls-san`：加入 API Server 证书的访问地址；通过其他地址访问时，需要一并添加。

    ### 配置 Docker Hub 镜像源 [step]

    ```bash
    sudo tee /etc/rancher/k3s/registries.yaml >/dev/null <<'EOF'
    mirrors:
      docker.io:
        endpoint:
          - https://docker.m.daocloud.io
    EOF

    sudo chmod 600 /etc/rancher/k3s/registries.yaml
    ```

    确认镜像站可以访问，返回 `401` 属于正常响应：

    ```bash
    curl -IL --connect-timeout 10 https://docker.m.daocloud.io/v2/
    ```

    容器内的 K3s 使用自带的 containerd 拉取镜像，该配置文件会挂载进容器，因此这里的镜像源直接生效，不需要额外配置宿主机的 Docker。

    ### 启动 K3s Server [step]

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
      rancher/k3s:v1.36.4-k3s1 \
      server
    ```

    主要参数含义：

    - `--privileged`、`--network host`、`--cgroupns host`：K3s 容器需要使用宿主机网络和 cgroup，因此以特权方式运行。
    - `-v /etc/rancher/k3s/config.yaml:/etc/rancher/k3s/config.yaml:ro`：把宿主机上的配置文件挂载进容器；镜像源配置文件同理。
    - `-v /userdata/k3s:/var/lib/rancher/k3s`：容器内的数据目录映射到宿主机数据分区，与 `data-dir` 对应。
    - `-v /sys/fs/cgroup:/sys/fs/cgroup:rw`、`-v /lib/modules:/lib/modules:ro`：向容器提供 cgroup 和内核模块。

    ### 检查容器状态 [step]

    ```bash
    sudo docker ps --filter name=k3s-server
    ```

    容器未正常运行时，查看日志：

    ```bash
    sudo docker logs --tail=100 k3s-server
    ```

    ### 等待节点和系统 Pod 就绪 [step]

    查看节点状态，`STATUS` 一列应为 `Ready`：

    ```bash
    sudo docker exec k3s-server kubectl get nodes -o wide
    ```

    ```shell
    bmc@bmc:~$ sudo docker exec k3s-server kubectl get nodes -o wide
    NAME   STATUS   ROLES           AGE     VERSION        INTERNAL-IP      EXTERNAL-IP   OS-IMAGE           KERNEL-VERSION    CONTAINER-RUNTIME
    bmc    Ready    control-plane   6m11s   v1.36.4+k3s1   172.16.100.176   <none>        K3s v1.36.4+k3s1   6.1.118 (arm64)   containerd://2.3.4-k3s1.36
    ```

    容器内使用 K3s 自带的 containerd，因此 `CONTAINER-RUNTIME` 显示为 `containerd://`；`OS-IMAGE` 显示的是容器镜像内的系统信息。

    查看系统 Pod：

    ```bash
    sudo docker exec k3s-server kubectl get pods -A -o wide
    ```

    首次启动需要拉取镜像，Pod 会先处于 `ContainerCreating`：

    ```shell
    bmc@bmc:~$ sudo docker exec k3s-server kubectl get pods -A -o wide
    NAMESPACE     NAME                                      READY   STATUS              RESTARTS   AGE    IP       NODE   NOMINATED NODE   READINESS GATES
    kube-system   coredns-54996dc9b4-s96f7                  0/1     ContainerCreating   0          6m2s   <none>   bmc    <none>           <none>
    kube-system   helm-install-traefik-8xwk2                0/1     ContainerCreating   0          6m     <none>   bmc    <none>           <none>
    kube-system   helm-install-traefik-crd-zmggm            0/1     ContainerCreating   0          6m1s   <none>   bmc    <none>           <none>
    kube-system   local-path-provisioner-58d557dc48-7v6ft   0/1     ContainerCreating   0          6m2s   <none>   bmc    <none>           <none>
    kube-system   metrics-server-6dc596dfb8-wxrws           0/1     ContainerCreating   0          6m2s   <none>   bmc    <none>           <none>
    ```
    等待镜像拉取完成后，各组件会进入 `Running` 状态。

  </CodeBlockTab>
</CodeBlockTabs>

## Agent 节点部署

<CodeBlockTabs defaultValue="在线部署">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="在线部署">在线部署</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="离线部署">离线部署</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="Docker部署">Docker部署</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="在线部署">

    ### 检查与 Server 的连通性 [step]

    Agent 需要能够访问 Server 的 `6443` 端口：

    ```bash
    ping -c 3 172.16.100.176

    # 返回 200 说明 API Server 可以访问
    curl -ksS --connect-timeout 5 -o /dev/null -w '%{http_code}\n' https://172.16.100.176:6443/cacerts
    ```

    ### 获取 Node Token [step]

    在 Server 节点上执行，复制输出的 Token：

    ```bash
    sudo cat /userdata/k3s/server/node-token
    ```

    Server 的 `data-dir` 为 `/userdata/k3s`，因此 Token 不在默认的 `/var/lib/rancher/k3s/server/node-token`。

    ### 准备目录与 Token [step]

    回到 Agent 节点，创建数据目录和配置目录：

    ```bash
    sudo mkdir -p /userdata/k3s
    sudo chmod 700 /userdata/k3s
    sudo mkdir -p /etc/rancher/k3s
    ```

    把上一步复制的 Token 写入文件，将 `<K3S_TOKEN>` 替换为实际值：

    ```bash
    sudo tee /etc/rancher/k3s/token >/dev/null <<'EOF'
    <K3S_TOKEN>
    EOF
    ```

    ### 写入 Agent 配置 [step]

    ```bash
    sudo tee /etc/rancher/k3s/config.yaml >/dev/null <<'EOF'
    server: https://172.16.100.176:6443
    token-file: /etc/rancher/k3s/token
    data-dir: /userdata/k3s
    snapshotter: native
    node-name: sub11
    node-ip: 172.16.100.177
    flannel-iface: eth0
    docker: true
    EOF
    ```

    各配置项含义：

    - `server`：Server 的 API 地址，Agent 通过它加入集群。
    - `token-file`：Token 文件路径；也可以改用 `token`，直接写入 Token 值。
    - `data-dir`、`snapshotter`：与 Server 端保持一致，数据目录放在 `/userdata` 数据分区。
    - `node-name`：节点名称。本机主机名为 `firefly`，这里显式指定为 `sub11`。
    - `node-ip`、`flannel-iface`：节点在集群中的地址和集群网络使用的网口，需按实际环境修改。
    - `docker: true`：与 Server 一致，使用 Docker 作为容器运行时。

    ### 配置 Docker Hub 镜像源 [step]

    ```bash
    sudo tee /etc/rancher/k3s/registries.yaml >/dev/null <<'EOF'
    mirrors:
      docker.io:
        endpoint:
          - https://docker.m.daocloud.io
    EOF

    sudo chmod 600 /etc/rancher/k3s/registries.yaml
    ```

    与 Server 端相同，`docker: true` 时 Pod 镜像由 Docker 拉取，因此还需要在节点上配置 Docker 的镜像源：

    ```bash
    sudo tee /etc/docker/daemon.json >/dev/null <<'EOF'
    {
      "registry-mirrors": [
        "https://docker.1ms.run",
        "https://docker-0.unsee.tech"
      ]
    }
    EOF

    sudo systemctl restart docker
    ```

    ### 安装 K3s Agent [step]

    ```bash
    curl -sfL https://rancher-mirror.rancher.cn/k3s/k3s-install.sh | \
      sudo env \
        INSTALL_K3S_MIRROR=cn \
        INSTALL_K3S_VERSION=v1.36.4+k3s1 \
        K3S_URL=https://172.16.100.176:6443 \
        K3S_TOKEN_FILE=/etc/rancher/k3s/token \
        sh -
    ```

    设置 `K3S_URL` 后，安装脚本会创建并启用 `k3s-agent.service`；同时必须提供 `K3S_TOKEN` 或 `K3S_TOKEN_FILE`，否则脚本会以 `K3S_TOKEN or K3S_TOKEN_FILE is not defined` 报错退出。

    ### 检查 Agent 服务 [step]

    ```bash
    sudo systemctl status k3s-agent --no-pager -l
    ```

    服务状态应为 `active (running)`：

    ```text
    Active: active (running)
    ```

    服务未正常启动时，查看最近的日志：

    ```bash
    sudo journalctl -u k3s-agent -n 100 --no-pager
    ```

    ### 在 Server 上验证节点加入 [step]

    回到 Server 节点执行：

    ```bash
    sudo k3s kubectl get nodes -o wide
    ```

    Agent 节点应显示为 `Ready`：

    ```shell
    NAME    STATUS   ROLES           AGE    VERSION        INTERNAL-IP      KERNEL-VERSION     CONTAINER-RUNTIME
    bmc     Ready    control-plane   5d3h   v1.36.4+k3s1   172.16.100.176   6.1.118 (arm64)    docker://20.10.24+dfsg1
    sub11   Ready    <none>          5d     v1.36.4+k3s1   172.16.100.177   5.10.160 (arm64)   docker://26.1.3
    ```

    节点 `Ready` 只说明 kubelet 已注册，建议再确认该节点上确实有 Pod 可以正常运行：

    ```bash
    sudo k3s kubectl get pods -A -o wide --field-selector spec.nodeName=sub11
    ```
  </CodeBlockTab>
  <CodeBlockTab value="离线部署">
    Agent 节点离线部署使用与 Server 节点完全相同的离线包，区别是安装时以 `agent` 方式启动，并需要 Server 的 Node Token。离线包的准备方式见上方 **Server 节点部署 → 离线部署**，已经准备过可直接复用。

    Agent 节点同样需要提前安装并配置 Docker，请参考[系统环境](env.md)。离线部署不需要配置 `registries.yaml` 和 Docker 的 `registry-mirrors`；如果节点需要从内网镜像仓库拉取业务镜像，再按本页 **Agent 节点部署 → 在线部署** 中的说明配置。

    ### 拷贝离线包到 Agent 节点 [step]

    ```bash
    scp -r ~/k3s-offline root@172.16.100.177:/root/
    ```

    ### 获取 Node Token [step]

    在 Server 节点执行，复制输出的 Token：

    ```bash
    sudo cat /userdata/k3s/server/node-token
    ```

    Server 的 `data-dir` 为 `/userdata/k3s`，因此 Token 不在默认的 `/var/lib/rancher/k3s/server/node-token`。

    ### 放置二进制、配置与 Token [step]

    回到 Agent 节点，放置二进制并创建目录：

    ```bash
    sudo mkdir -p /userdata/k3s /etc/rancher/k3s
    sudo chmod 700 /userdata/k3s
    sudo install -m 755 ~/k3s-offline/k3s-arm64 /usr/local/bin/k3s
    ```

    将上一步复制的 Token 写入文件，把 `<K3S_TOKEN>` 替换为实际值：

    ```bash
    sudo tee /etc/rancher/k3s/token >/dev/null <<'EOF'
    <K3S_TOKEN>
    EOF
    ```

    写入 Agent 配置，各配置项含义与在线部署相同：

    ```bash
    sudo tee /etc/rancher/k3s/config.yaml >/dev/null <<'EOF'
    server: https://172.16.100.176:6443
    token-file: /etc/rancher/k3s/token
    data-dir: /userdata/k3s
    snapshotter: native
    node-name: sub11
    node-ip: 172.16.100.177
    flannel-iface: eth0
    docker: true
    EOF
    ```

    ### 导入镜像包 [step]

    ```bash
    sudo docker load -i ~/k3s-offline/k3s-airgap-images-arm64.tar
    ```

    ### 执行离线安装 [step]

    ```bash
    cd ~/k3s-offline
    sudo env INSTALL_K3S_SKIP_DOWNLOAD=true INSTALL_K3S_EXEC=agent \
      K3S_URL=https://172.16.100.176:6443 \
      K3S_TOKEN_FILE=/etc/rancher/k3s/token \
      ./k3s-install.sh
    ```

    与 Server 端离线安装相比，只需要两处调整：

    - `INSTALL_K3S_EXEC=agent`：以 Agent 方式安装，生成并启用 `k3s-agent.service`；
    - 增加 `K3S_URL` 与 `K3S_TOKEN_FILE`：分别指向 Server 的 API 地址和 Token 文件，用于加入集群。

    ```shell
    [INFO]  Skipping k3s download and verify
    [INFO]  Creating /usr/local/bin/kubectl symlink to k3s
    [INFO]  systemd: Enabling k3s-agent unit
    [INFO]  systemd: Starting k3s-agent
    ```

    ### 检查 Agent 服务 [step]

    ```bash
    sudo systemctl status k3s-agent --no-pager -l
    ```

    服务状态应为 `active (running)`：

    ```text
    Active: active (running)
    ```

    服务未正常启动时，查看最近的日志：

    ```bash
    sudo journalctl -u k3s-agent -n 100 --no-pager
    ```

    ### 在 Server 上验证节点加入 [step]

    回到 Server 节点执行：

    ```bash
    sudo k3s kubectl get nodes -o wide
    ```

    节点进入 `Ready` 即部署完成：

    ```shell
    NAME    STATUS   ROLES           AGE   VERSION        INTERNAL-IP      CONTAINER-RUNTIME
    bmc     Ready    control-plane   6d    v1.36.4+k3s1   172.16.100.176   docker://20.10.24+dfsg1
    sub11   Ready    <none>          5d    v1.36.4+k3s1   172.16.100.177   docker://26.1.3
    ```
  </CodeBlockTab>
  <CodeBlockTab value="Docker部署">
    Agent 同样以 Docker 容器方式启动，容器内使用 K3s 自带的 containerd，宿主机上的 Docker 只用于运行该容器。

    ### 创建数据和配置目录 [step]

    ```bash
    sudo mkdir -p /userdata/k3s
    sudo chmod 700 /userdata/k3s
    sudo mkdir -p /etc/rancher/k3s
    ```

    ### 获取 Node Token [step]

    在 Server 节点执行，复制输出的 Token：

    ```bash
    sudo docker exec k3s-server cat /var/lib/rancher/k3s/server/node-token
    ```

    ```shell
    bmc@bmc:~$ sudo docker exec k3s-server cat /var/lib/rancher/k3s/server/node-token
    K10ec8dcabbca254cbbdc6e9788d68f88f6140b95b3e2704ebf1481c684ac6f1e0c::server:bbae98578e05103a06f4ade57ceea6f5
    ```

    Server 容器的数据目录映射到宿主机的 `/userdata/k3s`，因此在宿主机上执行 `sudo cat /userdata/k3s/server/node-token` 结果相同。

    ### 准备 Token 与配置 [step]

    将上一步复制的 Token 写入文件，把 `<K3S_TOKEN>` 替换为实际值：

    ```bash
    sudo tee /etc/rancher/k3s/token >/dev/null <<'EOF'
    <K3S_TOKEN>
    EOF
    ```

    写入 Agent 配置：

    ```bash
    sudo tee /etc/rancher/k3s/config.yaml >/dev/null <<'EOF'
    server: https://172.16.100.176:6443
    token-file: /etc/rancher/k3s/token
    data-dir: /var/lib/rancher/k3s
    snapshotter: native
    node-name: sub11
    node-ip: 172.16.100.177
    flannel-iface: eth0
    EOF
    ```

    各配置项含义：

    - `server`：Server 的 API 地址，Agent 通过它加入集群。
    - `token-file`：容器内的 Token 文件路径，通过下方挂载映射到宿主机的 `/etc/rancher/k3s/token`。
    - `data-dir`：容器内使用的数据目录，通过下方挂载映射到宿主机的 `/userdata/k3s`，因此这里填写容器内路径。
    - `node-name`：容器内的主机名不是物理主机名，需要显式指定节点名称。
    - `node-ip`、`flannel-iface`：节点在集群中的地址和集群网络使用的网口，需按实际环境修改。

    ### 配置 Docker Hub 镜像源 [step]

    ```bash
    sudo tee /etc/rancher/k3s/registries.yaml >/dev/null <<'EOF'
    mirrors:
      docker.io:
        endpoint:
          - https://docker.m.daocloud.io
    EOF

    sudo chmod 600 /etc/rancher/k3s/registries.yaml
    ```

    容器内的 K3s 使用自带的 containerd 拉取镜像，该配置文件会挂载进容器，因此镜像源直接生效，不需要额外配置宿主机的 Docker。

    ### 启动 K3s Agent [step]

    ```bash
    sudo docker run -d \
      --name k3s-agent \
      --restart=unless-stopped \
      --privileged \
      --network host \
      --cgroupns host \
      -v /etc/rancher/k3s/config.yaml:/etc/rancher/k3s/config.yaml:ro \
      -v /etc/rancher/k3s/registries.yaml:/etc/rancher/k3s/registries.yaml:ro \
      -v /etc/rancher/k3s/token:/etc/rancher/k3s/token:ro \
      -v /etc/rancher/node:/etc/rancher/node \
      -v /userdata/k3s:/var/lib/rancher/k3s \
      -v /sys/fs/cgroup:/sys/fs/cgroup:rw \
      -v /lib/modules:/lib/modules:ro \
      rancher/k3s:v1.36.4-k3s1 \
      agent
    ```

    参数与 Server 容器基本相同，区别是启动命令为 `agent`，并额外挂载以下两项：

    - `-v /etc/rancher/k3s/token:/etc/rancher/k3s/token:ro`：把 Token 文件挂载进容器，与 `token-file` 对应。
    - `-v /etc/rancher/node:/etc/rancher/node`：节点密码保存在 `/etc/rancher/node/password`，它不在 `data-dir` 内，用于标识节点身份。容器重建时如果丢失，Server 会因节点密码不一致而拒绝该节点，因此需要持久化到宿主机。

    ### 检查容器状态 [step]

    ```bash
    sudo docker ps --filter name=k3s-agent
    ```

    容器未正常运行时，查看日志：

    ```bash
    sudo docker logs --tail=100 k3s-agent
    ```

    ### 在 Server 上验证节点加入 [step]

    回到 Server 节点执行：

    ```bash
    sudo docker exec k3s-server kubectl get nodes -o wide
    ```

    节点进入 `Ready` 即部署完成：

    ```shell
    NAME    STATUS   ROLES           AGE   VERSION        INTERNAL-IP      CONTAINER-RUNTIME
    bmc     Ready    control-plane   6d    v1.36.4+k3s1   172.16.100.176   containerd://2.3.4-k3s1.36
    sub11   Ready    <none>          5d    v1.36.4+k3s1   172.16.100.177   containerd://2.3.4-k3s1.36
    ```
  </CodeBlockTab>
</CodeBlockTabs>