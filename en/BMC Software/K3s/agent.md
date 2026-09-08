# Agent Deployment

> All commands in this document are executed on the `sub04` node. The K3s Agent runs as a Docker container on `sub04` and joins the K3s Server running on the `bmc` node.
>
> This example uses the following node configuration:
>
> - Server: `bmc`, with the API Server at `172.22.10.0:6443`
> - Agent: `sub04`, with the node address `172.22.14.0`

## Create the K3s Agent Data and Configuration Directories

Log in to the `sub04` node and create the K3s data directory and configuration directory:

```bash
sudo mkdir -p /userdata/k3s
sudo mkdir -p /etc/rancher/k3s
```

## Create the K3s Agent Configuration File

Write the following content to `/etc/rancher/k3s/config.yaml`:

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

Configuration notes:

- `server`: The API Server address of the K3s Server. Modify it according to the actual Server address.
- `token-file`: Specifies the path of the registration token file used by the Agent.
- `data-dir`: The data directory K3s uses inside the container. This directory is mapped to `/userdata/k3s` on the host.
- `snapshotter: native`: Uses the native snapshotter, which suits the current containerized deployment environment.
- `node-name`: Sets the Agent node name. Node names within the cluster must be unique.
- `node-ip`: Sets the internal IP address of the Agent node within the cluster. Modify it according to the actual network configuration.
- `flannel-iface`: Specifies the network interface used by the K3s cluster network. Make sure `eth1` is an interface that actually exists and is reachable.

## Obtain the K3s Registration Token

Run the following command on the K3s Server node to obtain the Token required for Agent registration:

```bash
sudo docker exec k3s-server cat /var/lib/rancher/k3s/server/node-token
```

Keep the Token safe and never commit the real Token to a code repository or public documentation.

## Configure the Agent Token

Copy the Token obtained from the `bmc` node to the `sub04` node and create the Token file. Replace `<K3S_NODE_TOKEN>` with the actual Token:

```bash
sudo tee /etc/rancher/k3s/token >/dev/null <<'EOF'
<K3S_NODE_TOKEN>
EOF

sudo chmod 600 /etc/rancher/k3s/token
```

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

## Start the K3s Agent

On the `sub04` node, start the K3s Agent as a privileged Docker container:

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

After startup, check the Agent container status and logs:

```bash
sudo docker ps --filter name=k3s-agent
sudo docker logs --tail=100 k3s-agent
```

## Verify the Agent Node

Go back to the K3s Server node and check the cluster node status:

```bash
sudo docker exec k3s-server kubectl get nodes -o wide
```

When the new node's status shows `Ready`, the Agent has successfully joined the cluster. Confirm that the `INTERNAL-IP`, node name, and version in the node information are as expected.

Also check the status of all system Pods:

```bash
sudo docker exec k3s-server kubectl get pods -A -o wide
```

If the Agent does not show `Ready` for a long time, or Pods stay in the `ContainerCreating`, `ImagePullBackOff`, or `CrashLoopBackOff` state, check the node information, events, and container logs respectively:

```bash
sudo docker exec k3s-server kubectl describe node sub04
sudo docker exec k3s-server kubectl get events -A --sort-by=.lastTimestamp
sudo docker logs --tail=200 k3s-agent
```
