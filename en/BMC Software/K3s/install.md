# K3s Deployment

This chapter describes three deployment methods. All of them use K3s `v1.36.4+k3s1`:

- **Online deployment**: install K3s on a node with internet access, using Docker as the container runtime.
- **Offline deployment**: install K3s on an intranet node, also using Docker as the container runtime; the installation package and images must be prepared in advance.
- **Docker deployment**: K3s itself runs as a Docker container, and the container runtime is the containerd bundled with K3s.

## Server Node Deployment

<CodeBlockTabs defaultValue="Online Deployment">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="Online Deployment">Online</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="Offline Deployment">Offline</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="Docker Deployment">Docker</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="Online Deployment">

    ### Create the Data and Configuration Directories [step]

    ```bash
    sudo mkdir -p /userdata/k3s
    sudo chmod 700 /userdata/k3s
    sudo mkdir -p /etc/rancher/k3s
    ```

    ### Configure K3s [step]

    ```bash
    sudo tee /etc/rancher/k3s/config.yaml >/dev/null <<'EOF'
    data-dir: /userdata/k3s
    snapshotter: native
    node-name: bmc
    node-ip: 172.16.100.176
    docker: true
    flannel-iface: MGMT
    tls-san:
      - 172.16.100.176
    EOF
    ```

    Meaning of each configuration item:

    - `data-dir`: the K3s data directory, placed on the `/userdata` data partition to avoid writing to the overlay writable layer.
    - `snapshotter: native`: use the native snapshotter.
    - `node-ip`, `flannel-iface`: the address of the node inside the cluster and the network interface used by the cluster network; adjust them for your environment.
    - `docker: true`: use Docker as the container runtime.
    - `tls-san`: additional addresses added to the API Server certificate; add any other address you use to access the API.

    ### Configure the Docker Hub Mirror [step]

    ```bash
    sudo tee /etc/rancher/k3s/registries.yaml >/dev/null <<'EOF'
    mirrors:
      docker.io:
        endpoint:
          - https://docker.m.daocloud.io
    EOF

    sudo chmod 600 /etc/rancher/k3s/registries.yaml
    ```

    Confirm that the mirror is reachable:

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

    ### Install the K3s Server [step]

    **Networks in China**

    ```bash
    curl -sfL https://rancher-mirror.rancher.cn/k3s/k3s-install.sh | \
    sudo env INSTALL_K3S_MIRROR=cn \
    INSTALL_K3S_EXEC=server \
    INSTALL_K3S_VERSION=v1.36.4+k3s1 sh -
    ```

    **Other networks**

    ```bash
    curl -sfL https://get.k3s.io | \
    sudo env INSTALL_K3S_EXEC=server \
    INSTALL_K3S_VERSION=v1.36.4+k3s1 sh -
    ```

    ### Check the K3s Service [step]

    ```bash
    sudo systemctl status k3s --no-pager -l
    ```

    If the service does not start properly, check the recent logs:

    ```bash
    sudo journalctl -u k3s -n 200 --no-pager
    ```

    ### Wait for the Node and the System Pods to Become Ready [step]

    Check the node status; the `STATUS` column should show `Ready`:

    ```bash
    sudo k3s kubectl get nodes -o wide
    ```

    ```shell
    bmc@bmc:~$ sudo k3s kubectl get nodes -o wide
    NAME   STATUS   ROLES           AGE     VERSION        INTERNAL-IP      EXTERNAL-IP   OS-IMAGE                           KERNEL-VERSION    CONTAINER-RUNTIME
    bmc    Ready    control-plane   6m11s   v1.36.4+k3s1   172.16.100.176   <none>        Debian GNU/Linux 12 (bookworm)     6.1.118 (arm64)   docker://20.10.24+dfsg1
    ```

    Check the system Pods:

    ```bash
    sudo k3s kubectl get pods -A -o wide
    ```

    The images are pulled on the first start, so the Pods may briefly stay in `ContainerCreating`. In the end the following components should be `Running` or `Completed`:

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
  <CodeBlockTab value="Offline Deployment">
  
    ### Prepare the Offline Package [step]

    On a machine with internet access, create a directory for the offline package and download the installation script, the arm64 binary, and the system component image package:

    ```bash
    mkdir -p ~/k3s-offline && cd ~/k3s-offline

    # Installation script
    curl -sfLO https://rancher-mirror.rancher.cn/k3s/k3s-install.sh
    # k3s binary
    curl -sfLO https://rancher-mirror.rancher.cn/k3s/v1.36.4-k3s1/k3s-arm64
    # k3s checksum
    curl -sfLO https://rancher-mirror.rancher.cn/k3s/v1.36.4-k3s1/sha256sum-arm64.txt
    # Offline images
    curl -sfLO https://github.com/k3s-io/k3s/releases/download/v1.36.4%2Bk3s1/k3s-airgap-images-arm64.tar
    ```

    Compare against the official hash to confirm that the binary is intact:

    ```bash
    grep -E " k3s-arm64$" sha256sum-arm64.txt
    sha256sum k3s-arm64
    ```

    The downloaded script has no execute permission, so add it manually:

    ```bash
    chmod +x k3s-install.sh
    ```

    ### Copy the Offline Package to the Node [step]

    Copy the whole `k3s-offline` directory to the target node:

    ```bash
    scp -r ~/k3s-offline bmc@172.16.100.176:/home/bmc/
    ```

    ### Create the Data and Configuration Directories [step]

    ```bash
    sudo mkdir -p /userdata/k3s
    sudo chmod 700 /userdata/k3s
    sudo mkdir -p /etc/rancher/k3s
    ```

    ### Install the Binary [step]

    ```bash
    sudo install -m 755 ~/k3s-offline/k3s-arm64 /usr/local/bin/k3s
    ```

    ### Configure K3s [step]

    ```bash
    sudo tee /etc/rancher/k3s/config.yaml >/dev/null <<'EOF'
    data-dir: /var/lib/rancher/k3s
    snapshotter: native
    node-name: bmc
    node-ip: 172.16.100.176
    docker: true
    flannel-iface: MGMT
    tls-san:
      - 172.16.100.176
    EOF
    ```

    Meaning of each configuration item:

    - `data-dir`: the data directory used inside the container, mapped to `/userdata/k3s` on the host by the mount below, so the container path is used here.
    - `snapshotter: native`: use the native snapshotter.
    - `node-name`: the hostname inside the container is not the physical hostname, so the node name has to be set explicitly.
    - `docker: true`: use Docker as the container runtime.
    - `node-ip`, `flannel-iface`: the address of the node inside the cluster and the network interface used by the cluster network; adjust them for your environment.
    - `tls-san`: additional addresses added to the API Server certificate; add any other address you use to access the API.

    ### Import the Image Package [step]

    ```bash
    sudo docker load -i ~/k3s-offline/k3s-airgap-images-arm64.tar
    ```

    ### Configure the Docker Hub Mirror [step]

    ```bash
    sudo tee /etc/rancher/k3s/registries.yaml >/dev/null <<'EOF'
    mirrors:
      docker.io:
        endpoint:
          - https://docker.m.daocloud.io
    EOF

    sudo chmod 600 /etc/rancher/k3s/registries.yaml
    ```

    ### Run the Offline Installation [step]

    ```bash
    cd ~/k3s-offline
    sudo env INSTALL_K3S_SKIP_DOWNLOAD=true INSTALL_K3S_EXEC=server ./k3s-install.sh
    ```

    ```shell
    bmc@bmc:~$ sudo env INSTALL_K3S_SKIP_DOWNLOAD=true INSTALL_K3S_EXEC=server ./k3s-install.sh
    [INFO]  Skipping k3s download and verify
    [INFO]  Creating /usr/local/bin/kubectl symlink to k3s
    [INFO]  systemd: Enabling k3s unit
    [INFO]  systemd: Starting k3s
    ```

    ### Check the Service and the Node [step]

    ```bash
    sudo systemctl status k3s --no-pager -l
    sudo k3s kubectl get nodes -o wide
    ```

    The installation is complete once the node is `Ready`:

    ```shell
    NAME   STATUS   ROLES           AGE   VERSION        INTERNAL-IP      OS-IMAGE                         KERNEL-VERSION    CONTAINER-RUNTIME
    bmc    Ready    control-plane   72s   v1.36.4+k3s1   172.16.100.176   Debian GNU/Linux 12 (bookworm)   6.1.118 (arm64)   docker://29.7.2
    ```

    ### Wait for the System Pods to Become Ready [step]

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
  <CodeBlockTab value="Docker Deployment">

    ### Create the Data and Configuration Directories [step]

    ```bash
    sudo mkdir -p /userdata/k3s
    sudo chmod 700 /userdata/k3s
    sudo mkdir -p /etc/rancher/k3s
    ```

    ### Configure K3s [step]

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

    Meaning of each configuration item:

    - `data-dir`: the data directory used inside the container, mapped to `/userdata/k3s` on the host by the mount below, so the container path is used here.
    - `snapshotter: native`: use the native snapshotter.
    - `node-name`: the hostname inside the container is not the physical hostname, so the node name has to be set explicitly.
    - `node-ip`, `flannel-iface`: the address of the node inside the cluster and the network interface used by the cluster network; adjust them for your environment.
    - `tls-san`: additional addresses added to the API Server certificate; add any other address you use to access the API.

    ### Configure the Docker Hub Mirror [step]

    ```bash
    sudo tee /etc/rancher/k3s/registries.yaml >/dev/null <<'EOF'
    mirrors:
      docker.io:
        endpoint:
          - https://docker.m.daocloud.io
    EOF

    sudo chmod 600 /etc/rancher/k3s/registries.yaml
    ```

    Confirm that the mirror is reachable:

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

    ### Start the K3s Server [step]

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

    Meaning of the main parameters:

    - `--privileged`, `--network host`, `--cgroupns host`: the K3s container needs the host network and cgroups, so it runs in privileged mode.
    - `-v /etc/rancher/k3s/config.yaml:/etc/rancher/k3s/config.yaml:ro`: mounts the host configuration file into the container; the same applies to the registry mirror configuration file.
    - `-v /userdata/k3s:/var/lib/rancher/k3s`: maps the container data directory to the host data partition, matching `data-dir`.
    - `-v /sys/fs/cgroup:/sys/fs/cgroup:rw`, `-v /lib/modules:/lib/modules:ro`: provides cgroups and kernel modules to the container.

    ### Check the Container Status [step]

    ```bash
    sudo docker ps --filter name=k3s-server
    ```

    If the container does not run properly, check the logs:

    ```bash
    sudo docker logs --tail=100 k3s-server
    ```

    ### Wait for the Node and the System Pods to Become Ready [step]

    Check the node status; the `STATUS` column should show `Ready`:

    ```bash
    sudo docker exec k3s-server kubectl get nodes -o wide
    ```

    ```shell
    bmc@bmc:~$ sudo docker exec k3s-server kubectl get nodes -o wide
    NAME   STATUS   ROLES           AGE     VERSION        INTERNAL-IP      EXTERNAL-IP   OS-IMAGE           KERNEL-VERSION    CONTAINER-RUNTIME
    bmc    Ready    control-plane   6m11s   v1.36.4+k3s1   172.16.100.176   <none>        K3s v1.36.4+k3s1   6.1.118 (arm64)   containerd://2.3.4-k3s1.36
    ```

    Check the system Pods:

    ```bash
    sudo docker exec k3s-server kubectl get pods -A -o wide
    ```

    The images are pulled on the first start, so the Pods stay in `ContainerCreating` at first:

    ```shell
    bmc@bmc:~$ sudo docker exec k3s-server kubectl get pods -A -o wide
    NAMESPACE     NAME                                      READY   STATUS              RESTARTS   AGE    IP       NODE   NOMINATED NODE   READINESS GATES
    kube-system   coredns-54996dc9b4-s96f7                  0/1     ContainerCreating   0          6m2s   <none>   bmc    <none>           <none>
    kube-system   helm-install-traefik-8xwk2                0/1     ContainerCreating   0          6m     <none>   bmc    <none>           <none>
    kube-system   helm-install-traefik-crd-zmggm            0/1     ContainerCreating   0          6m1s   <none>   bmc    <none>           <none>
    kube-system   local-path-provisioner-58d557dc48-7v6ft   0/1     ContainerCreating   0          6m2s   <none>   bmc    <none>           <none>
    kube-system   metrics-server-6dc596dfb8-wxrws           0/1     ContainerCreating   0          6m2s   <none>   bmc    <none>           <none>
    ```
    Once the images have been pulled, the components move to the `Running` state.

  </CodeBlockTab>
</CodeBlockTabs>

## Agent Node Deployment

<CodeBlockTabs defaultValue="Online Deployment">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="Online Deployment">Online</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="Offline Deployment">Offline</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="Docker Deployment">Docker</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="Online Deployment">

    ### Check Connectivity to the Server [step]

    The Agent must be able to reach port `6443` of the Server:

    ```bash
    ping -c 3 172.16.100.176

    # An HTTP 200 means the API Server is reachable
    curl -ksS --connect-timeout 5 -o /dev/null -w '%{http_code}\n' https://172.16.100.176:6443/cacerts
    ```

    ### Get the Node Token [step]

    Run this on the Server node and copy the printed token:

    ```bash
    sudo cat /userdata/k3s/server/node-token
    ```

    The `data-dir` of the Server is `/userdata/k3s`, so the token is not in the default `/var/lib/rancher/k3s/server/node-token`.

    ### Prepare the Directories and the Token [step]

    Back on the Agent node, create the data and configuration directories:

    ```bash
    sudo mkdir -p /userdata/k3s
    sudo chmod 700 /userdata/k3s
    sudo mkdir -p /etc/rancher/k3s
    ```

    Write the token you copied into a file, replacing `<K3S_TOKEN>` with the actual value:

    ```bash
    sudo tee /etc/rancher/k3s/token >/dev/null <<'EOF'
    <K3S_TOKEN>
    EOF
    ```

    ### Write the Agent Configuration [step]

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

    Meaning of each configuration item:

    - `server`: the API address of the Server, through which the Agent joins the cluster.
    - `token-file`: the path of the token file; `token` can be used instead to write the token value directly.
    - `data-dir`, `snapshotter`: the same as on the Server, with the data directory on the `/userdata` data partition.
    - `node-name`: the node name. The hostname of this machine is `firefly`, so `sub11` is set explicitly here.
    - `node-ip`, `flannel-iface`: the address of the node inside the cluster and the network interface used by the cluster network; adjust them for your environment.
    - `docker: true`: the same as on the Server, using Docker as the container runtime.

    ### Configure the Docker Hub Mirror [step]

    ```bash
    sudo tee /etc/rancher/k3s/registries.yaml >/dev/null <<'EOF'
    mirrors:
      docker.io:
        endpoint:
          - https://docker.m.daocloud.io
    EOF

    sudo chmod 600 /etc/rancher/k3s/registries.yaml
    ```
  
    Confirm that the mirror is reachable:

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

    ### Install the K3s Agent [step]

    ```bash
    curl -sfL https://rancher-mirror.rancher.cn/k3s/k3s-install.sh | \
      sudo env \
        INSTALL_K3S_MIRROR=cn \
        INSTALL_K3S_VERSION=v1.36.4+k3s1 \
        INSTALL_K3S_EXEC=agent \
        sh -
    ```

    ### Check the Agent Service [step]

    ```bash
    sudo systemctl status k3s-agent --no-pager -l
    ```

    The service should be `active (running)`:

    ```text
    Active: active (running)
    ```

    If the service does not start properly, check the recent logs:

    ```bash
    sudo journalctl -u k3s-agent -n 100 --no-pager
    ```

    ### Verify the Node Join on the Server [step]

    Go back to the Server node and run:

    ```bash
    sudo k3s kubectl get nodes -o wide
    ```

    The Agent node should show up as `Ready`:

    ```shell
    NAME    STATUS   ROLES           AGE    VERSION        INTERNAL-IP      KERNEL-VERSION     CONTAINER-RUNTIME
    bmc     Ready    control-plane   5d3h   v1.36.4+k3s1   172.16.100.176   6.1.118 (arm64)    docker://20.10.24+dfsg1
    sub11   Ready    <none>          5d     v1.36.4+k3s1   172.16.100.177   5.10.160 (arm64)   docker://26.1.3
    ```

    A node in `Ready` state only means that the kubelet has registered. It is worth confirming that Pods actually run on that node:

    ```bash
    sudo k3s kubectl get pods -A -o wide --field-selector spec.nodeName=sub11
    ```
  </CodeBlockTab>
  <CodeBlockTab value="Offline Deployment">

    ### Copy the Offline Package to the Agent Node [step]

    ```bash
    scp -r ~/k3s-offline root@172.16.100.177:/root/
    ```

    ### Get the Node Token [step]

    Run this on the Server node and copy the printed token:

    ```bash
    sudo cat /userdata/k3s/server/node-token
    ```

    The `data-dir` of the Server is `/userdata/k3s`, so the token is not in the default `/var/lib/rancher/k3s/server/node-token`.

    ### Install the Binary, the Configuration, and the Token [step]

    Back on the Agent node, install the binary and create the directories:

    ```bash
    sudo mkdir -p /userdata/k3s /etc/rancher/k3s
    sudo chmod 700 /userdata/k3s
    sudo install -m 755 ~/k3s-offline/k3s-arm64 /usr/local/bin/k3s
    ```

    Write the token you copied into a file, replacing `<K3S_TOKEN>` with the actual value:

    ```bash
    sudo tee /etc/rancher/k3s/token >/dev/null <<'EOF'
    <K3S_TOKEN>
    EOF
    ```

    Write the Agent configuration; the configuration items mean the same as in the online deployment:

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

    ### Import the Image Package [step]

    ```bash
    sudo docker load -i ~/k3s-offline/k3s-airgap-images-arm64.tar
    ```

    ### Run the Offline Installation [step]

    ```bash
    cd ~/k3s-offline
    sudo env INSTALL_K3S_SKIP_DOWNLOAD=true \ 
    INSTALL_K3S_EXEC=agent \
    ./k3s-install.sh
    ```

    ```shell
    [INFO]  Skipping k3s download and verify
    [INFO]  Creating /usr/local/bin/kubectl symlink to k3s
    [INFO]  systemd: Enabling k3s-agent unit
    [INFO]  systemd: Starting k3s-agent
    ```

    ### Check the Agent Service [step]

    ```bash
    sudo systemctl status k3s-agent --no-pager -l
    ```

    The service should be `active (running)`:

    ```text
    Active: active (running)
    ```

    If the service does not start properly, check the recent logs:

    ```bash
    sudo journalctl -u k3s-agent -n 100 --no-pager
    ```

    ### Verify the Node Join on the Server [step]

    Go back to the Server node and run:

    ```bash
    sudo k3s kubectl get nodes -o wide
    ```

    The deployment is complete once the node is `Ready`:

    ```shell
    NAME    STATUS   ROLES           AGE   VERSION        INTERNAL-IP      CONTAINER-RUNTIME
    bmc     Ready    control-plane   6d    v1.36.4+k3s1   172.16.100.176   docker://20.10.24+dfsg1
    sub11   Ready    <none>          5d    v1.36.4+k3s1   172.16.100.177   docker://26.1.3
    ```
  </CodeBlockTab>
  <CodeBlockTab value="Docker Deployment">

    ### Create the Data and Configuration Directories [step]

    ```bash
    sudo mkdir -p /userdata/k3s
    sudo chmod 700 /userdata/k3s
    sudo mkdir -p /etc/rancher/k3s
    ```

    ### Get the Node Token [step]

    Run this on the Server node and copy the printed token:

    ```bash
    sudo docker exec k3s-server cat /var/lib/rancher/k3s/server/node-token
    ```

    ```shell
    bmc@bmc:~$ sudo docker exec k3s-server cat /var/lib/rancher/k3s/server/node-token
    K10ec8dcabbca254cbbdc6e9788d68f88f6140b95b3e2704ebf1481c684ac6f1e0c::server:bbae98578e05103a06f4ade57ceea6f5
    ```

    The data directory of the Server container is mapped to `/userdata/k3s` on the host, so `sudo cat /userdata/k3s/server/node-token` on the host prints the same value.

    ### Prepare the Token and the Configuration [step]

    Write the token you copied into a file, replacing `<K3S_TOKEN>` with the actual value:

    ```bash
    sudo tee /etc/rancher/k3s/token >/dev/null <<'EOF'
    <K3S_TOKEN>
    EOF
    ```

    Write the Agent configuration:

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

    Meaning of each configuration item:

    - `server`: the API address of the Server, through which the Agent joins the cluster.
    - `token-file`: the token file path inside the container, mapped to `/etc/rancher/k3s/token` on the host by the mount below.
    - `data-dir`: the data directory used inside the container, mapped to `/userdata/k3s` on the host by the mount below, so the container path is used here.
    - `node-name`: the hostname inside the container is not the physical hostname, so the node name has to be set explicitly.
    - `node-ip`, `flannel-iface`: the address of the node inside the cluster and the network interface used by the cluster network; adjust them for your environment.

    ### Configure the Docker Hub Mirror [step]

    ```bash
    sudo tee /etc/rancher/k3s/registries.yaml >/dev/null <<'EOF'
    mirrors:
      docker.io:
        endpoint:
          - https://docker.m.daocloud.io
    EOF

    sudo chmod 600 /etc/rancher/k3s/registries.yaml
    ```

    Confirm that the mirror is reachable:

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
    
    ### Start the K3s Agent [step]

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

    The parameters are almost the same as for the Server container. The differences are the `agent` command and two extra mounts:

    - `-v /etc/rancher/k3s/token:/etc/rancher/k3s/token:ro`: mounts the token file into the container, matching `token-file`.
    - `-v /etc/rancher/node:/etc/rancher/node`: the node password is stored in `/etc/rancher/node/password`, which is outside `data-dir` and identifies the node. If it is lost when the container is recreated, the Server rejects the node because the node password does not match, so it must be persisted on the host.

    ### Check the Container Status [step]

    ```bash
    sudo docker ps --filter name=k3s-agent
    ```

    If the container does not run properly, check the logs:

    ```bash
    sudo docker logs --tail=100 k3s-agent
    ```

    ### Verify the Node Join on the Server [step]

    Go back to the Server node and run:

    ```bash
    sudo docker exec k3s-server kubectl get nodes -o wide
    ```

    The deployment is complete once the node is `Ready`:

    ```shell
    NAME    STATUS   ROLES           AGE   VERSION        INTERNAL-IP      CONTAINER-RUNTIME
    bmc     Ready    control-plane   6d    v1.36.4+k3s1   172.16.100.176   containerd://2.3.4-k3s1.36
    sub11   Ready    <none>          5d    v1.36.4+k3s1   172.16.100.177   containerd://2.3.4-k3s1.36
    ```
  </CodeBlockTab>
</CodeBlockTabs>
