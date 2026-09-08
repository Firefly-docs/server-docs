# Server Deployment

> All commands in this document are executed on the `bmc` node. The K3s Server runs as a Docker container on `bmc`.

## Create the K3s Data and Configuration Directories

On the `bmc` node, create the persistent data directory and configuration directory for K3s:

```bash
sudo mkdir -p /userdata/k3s
sudo mkdir -p /etc/rancher/k3s
```

## Create the K3s Server Configuration File

Write the following content to `/etc/rancher/k3s/config.yaml`:

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

Configuration notes:

- `data-dir`: The data directory K3s uses inside the container. This directory is mapped to `/userdata/k3s` on the host.
- `snapshotter: native`: Uses the native snapshotter, which suits the current containerized deployment environment.
- `node-name`: Sets the node name. Every node in the cluster must use a unique name.
- `node-ip`: Sets the internal IP address of the node within the cluster. Modify it according to the actual network configuration.
- `flannel-iface`: Specifies the network interface used by the K3s cluster network. Make sure `nic0` is an interface that actually exists and is reachable.
- `tls-san`: Adds access addresses to the K3s API Server certificate. When accessing the API Server through other addresses, add the corresponding address to this list.

## Configure a Docker Hub Mirror

If the node has slow or restricted access to Docker Hub, you can configure a mirror:

```bash
sudo tee /etc/rancher/k3s/registries.yaml >/dev/null <<'EOF'
mirrors:
  docker.io:
    endpoint:
      - https://docker.m.daocloud.io
EOF

sudo chmod 600 /etc/rancher/k3s/registries.yaml
```

## Restrict LightDM Real-Time Scheduling Privileges

On some devices, the graphics service may use elevated real-time scheduling privileges, which prevents the K3s container from creating CPU cgroups properly. You can restrict LightDM's real-time scheduling privileges through a systemd override:

```bash
sudo systemctl edit lightdm.service
```

Write the following into the editor that opens:

```ini
[Service]
RestrictRealtime=yes
CPUSchedulingPolicy=other
CPUSchedulingPriority=0
```

After saving, reload the systemd configuration and restart LightDM:

```bash
sudo systemctl daemon-reload
sudo systemctl restart lightdm.service
```

If LightDM is not installed or not enabled on the device, skip this step.

## Start the K3s Server

Start the K3s Server as a privileged Docker container:

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

After starting the K3s Server on the `bmc` node, use the following commands to check the container status and startup logs:

```bash
sudo docker ps --filter name=k3s-server
sudo docker logs --tail=100 k3s-server
```

## Verify the Node Status

Wait for K3s to finish initialization, then check the Server node status:

```bash
sudo docker exec k3s-server kubectl get nodes -o wide
```

When the node status shows `Ready`, the K3s Server has started properly. Example:

```text
NAME   STATUS   ROLES           AGE     VERSION        INTERNAL-IP   EXTERNAL-IP   OS-IMAGE           KERNEL-VERSION   CONTAINER-RUNTIME
bmc    Ready    control-plane   6m11s   v1.36.3+k3s1   172.22.10.0   <none>        K3s v1.36.3+k3s1   6.1.118 (arm64)   containerd://2.3.2-k3s2
```

## Verify the System Pods

Check the Pods in the K3s system namespaces:

```bash
sudo docker exec k3s-server kubectl get pods -A -o wide
```

On first startup, components such as CoreDNS, Traefik, Local Path Provisioner, and Metrics Server may be in the `ContainerCreating` state. After the images are pulled and the containers start, the related Pods should show `Running`, and the `READY` column should show a fully ready state such as `1/1`.

If a Pod stays in the `ContainerCreating`, `ImagePullBackOff`, or `CrashLoopBackOff` state for a long time, check the Pod events and the K3s logs:

```bash
sudo docker exec k3s-server kubectl get events -A --sort-by=.lastTimestamp
sudo docker logs --tail=200 k3s-server
```
