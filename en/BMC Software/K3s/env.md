# System Environment

Before installing K3s, prepare the base environment of every node.

The checks and configurations in this chapter must be performed on **all K3s nodes**, including the Server and the Agents.

If you have many nodes, complete the configuration on one node and confirm that it works before applying the same steps to the others.

## Cluster Planning

### Server Hardware Requirements [step]

The K3s Server runs the Kubernetes API, the scheduler, and other cluster management components. As the number of nodes and Pods grows, the CPU and memory usage of the Server grows as well.

Use the following configuration as a reference according to the cluster scale:

| Deployment Scale | Nodes | vCPU | Memory |
| -------- | -------: | ---: | ----: |
| Small    | Up to 10 | 2 cores | 4 GB |
| Medium   | Up to 100 | 4 cores | 8 GB |
| Large    | Up to 250 | 8 cores | 16 GB |
| X-Large  | Up to 500 | 16 cores | 32 GB |
| XX-Large | More than 500 | 32 cores | 64 GB |

<Callout>These values mainly define the baseline resources of the Server. In practice, also take the resource usage of Pods, workloads, monitoring, and storage components into account.</Callout>

### Node Configuration [step]

The current environment uses one K3s Server and two K3s Agents:

| Hostname | Role | Management IP | Architecture | Operating System | Kernel | Docker Engine | K3s Version |
| ----- | ------------------------- | -------------- | ----- | ------------------------------ | -------- | -------------- | ------------ |
| bmc   | K3s Server (control-plane) | 172.16.100.176 | arm64 | Debian GNU/Linux 12 (bookworm) | 6.1.118  | 20.10.24+dfsg1 | v1.36.4+k3s1 |
| sub11 | K3s Agent                 | 172.16.100.177 | arm64 | Ubuntu 20.04.6 LTS             | 5.10.160 | 26.1.3         | v1.36.4+k3s1 |
| sub12 | K3s Agent                 | 172.16.100.178 | arm64 | Debian GNU/Linux 12 (bookworm) | 6.1.141  | 20.10.24+dfsg1 | v1.36.4+k3s1 |

Keep the following in mind when configuring the nodes:

* Docker versions may differ between nodes; for example, `bmc` and `sub12` use `20.10.24+dfsg1` while `sub11` uses `26.1.3`.
* Use the same K3s version for the Server and the Agents; this environment uses `v1.36.4+k3s1` on all nodes.
* Adjust the node IPs for your own network, and make sure the nodes can communicate with each other.

## Pre-Deployment Checks

Check each node before installing K3s.

### Check Node Networking [step]

All nodes must use static IP addresses, and the nodes must be able to reach each other. K3s uses the node IP as the internal cluster address. If the address changes, the node may fail or may be unable to join the cluster.

**Confirm that the address is statically configured**

```bash
# Show the node IPv4 address
ip -4 addr

# Show the default route
ip route show default
```

- `valid_lft forever` after the address means the address is static. A countdown such as `valid_lft 3512sec` means the address comes from DHCP and must be changed to a static address first.
- A default route showing `proto static` means the gateway is also statically configured.

Sample output from a real run:

```text
inet 172.16.100.176/16 brd 172.16.255.255 scope global MGMT
   valid_lft forever preferred_lft forever

default via 172.16.0.1 dev MGMT proto static metric 100
```

Once the address is confirmed to be static, test connectivity from the `bmc` node to the other nodes:

```bash
ping -c 4 172.16.100.177
ping -c 4 172.16.100.178
```

If the nodes cannot communicate, or the address is not static, configure it first as described in [Network Settings](https://community.t-firefly.com/docs/server/bmc-software/aBMC/subNetwork).

### Check Firewall Ports [step]

Make sure the firewall and other security policies do not block the traffic K3s requires, and confirm that these ports are not used by other programs and have no conflicting port mapping rules.

K3s uses the following ports by default:

| Port | Protocol | Purpose |
| ----------- | --- | --------------------------------------- |
| 6443        | TCP | K3s API Server; communication between the Server and Agent nodes |
| 8472        | UDP | Flannel VXLAN; communication between nodes |
| 10250       | TCP | Kubelet; the Server accesses Agent nodes |
| 80 / 443    | TCP | Traefik Ingress (when Traefik/ServiceLB is enabled) |
| 30000-32767 | TCP | NodePort service ports |

### Check the System Time [step]

The system time of all nodes should stay in sync.

A large time offset may cause TLS certificate validation failures and prevent nodes from joining the cluster. It also makes log timestamps from different nodes inconsistent, which complicates troubleshooting.

Check the current time:

```bash
date
```

If the time is not synchronized, configure it first as described in [Time Manager](https://community.t-firefly.com/docs/server/bmc-software/aBMC/timeManager).

### Check the Kernel Configuration [step]

After configuring networking, ports, and time, confirm that the Linux kernel meets the requirements of K3s. K3s relies on kernel capabilities such as namespaces, cgroups, networking, and file systems. These capabilities are determined by the kernel configuration and cannot be added by installing packages, so check them before installing K3s and rebuild the kernel if necessary.

K3s ships with a `check-config` subcommand that checks the kernel configuration, cgroups, iptables, and other requirements automatically, so you do not have to compare the items one by one. Download the k3s binary for your architecture (this environment uses `arm64`) and run the check:

```bash
curl -sfLO https://github.com/k3s-io/k3s/releases/download/v1.36.4%2Bk3s1/k3s-arm64
chmod +x k3s-arm64
sudo ./k3s-arm64 check-config
```

If K3s is already installed on the node, use the installed version instead:

```bash
sudo k3s check-config
```

The output lists the result of each check, with an overall conclusion on the last line:

```text
System:
- /usr/sbin iptables v1.8.9 (legacy): ok
- swap: disabled
- routes: default CIDRs 10.42.0.0/16 or 10.43.0.0/16 already routed

info: reading kernel config from /proc/config.gz ...

Generally Necessary:
- cgroup hierarchy: cgroups V2 mounted, cpu|cpuset|memory controllers status: good
- CONFIG_NAMESPACES: enabled
- CONFIG_NET_NS: enabled
...

Storage Drivers:
- "overlay":
  - CONFIG_OVERLAY_FS: enabled

STATUS: pass
```

`STATUS: pass` means the kernel meets the requirements of K3s. If it reports `fail`, adjust the kernel configuration according to the items marked `missing`, rebuild the kernel, and flash it again. Missing items under `Optional Features` are optional capabilities (for example, encrypted networking).

## Container Configuration

### Docker [step]

K3s uses its bundled containerd as the container runtime by default. This environment uses Docker instead, enabled with `docker: true`, so **every K3s node must have Docker installed**.

**Installation**

For the installation steps, see [Docker Installation](https://community.t-firefly.com/docs/software/other/Docker/docker-install).

After the installation, run the following commands to confirm that Docker is installed correctly and that the service is running:

```bash
docker --version
sudo systemctl is-active docker
```

Expected output:

```text
Docker version 20.10.24+dfsg1, build 297e128
active
```

**Migrating the data directory**

Firefly devices enable overlayroot by default. In that case, `/` consists of a read-only root file system (`/root-ro`) and a writable layer located in `/userdata/rootfs_overlay`, and only `/userdata` is a separate ext4 partition on the device.

Run the following command to confirm the current file system layout:

```bash
mount | grep overlayroot
```

Docker stores its data in `/var/lib/docker` by default, which is located in the writable layer of overlayfs. The Docker `overlay2` storage driver cannot be created directly on top of overlayfs: startup may fail with `failed to mount overlay: invalid argument`, fall back to the deprecated devicemapper driver, or even fail to start.

Therefore, move the Docker data directory to the separate `/userdata` partition, for example `/userdata/docker`.

Create the data directory:

```bash
sudo mkdir -p /userdata/docker
```

Edit `/etc/docker/daemon.json`:

```bash
sudo tee /etc/docker/daemon.json >/dev/null <<'EOF'
{
  "data-root": "/userdata/docker",
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "50m",
    "max-file": "3"
  },
  "registry-mirrors": [
    "https://docker.m.daocloud.io",
    "https://docker.1ms.run",
    "https://docker-0.unsee.tech"
  ],
  "insecure-registries": [
    "172.16.55.55:5000"
  ],
  "experimental": true
}
EOF
```

Key configuration items:

| Item | Description |
| --------------------- | -------------------------------------------------------- |
| `data-root`           | Store Docker data in `/userdata/docker` instead of the overlay writable layer of the root file system |
| `log-driver`          | Save container logs with `json-file` |
| `max-size`            | Maximum size of a single container log file: 50 MB |
| `max-file`            | Keep at most 3 log files |
| `registry-mirrors`    | Docker Hub mirrors; configure them according to your network |
| `insecure-registries` | Allow HTTP access to private registries, for example `172.16.55.55:5000` |
| `experimental`        | Enable Docker experimental features |

If you do not use a private registry, remove the `insecure-registries` entry.

If your network does not need Docker Hub mirrors, remove the `registry-mirrors` entry.

**Restart and verify**

Restart Docker after the configuration:

```bash
sudo systemctl restart docker
sudo docker info
```

Confirm the following in the output:

```text
Docker Root Dir: /userdata/docker
```

If `Docker Root Dir` shows `/userdata/docker`, the Docker data directory configuration has taken effect.

### Containerd [step]

The system containerd stores its data in `/var/lib/containerd` by default, which is also on overlayfs. The containerd overlay snapshotter has the same limitation as the Docker overlay2 driver and cannot be created on top of overlayfs, so its data directory must also be placed on the ext4 partition `/userdata`.

K3s ships with its own containerd (embedded in the k3s process, independent of the system containerd). The system containerd only supports Docker, so its CRI plugin must be disabled to avoid two CRI implementations conflicting with each other.

**Modify the configuration**

Create the directories:

```bash
sudo mkdir -p /etc/containerd
sudo mkdir -p /userdata/containerd
```

Edit `/etc/containerd/config.toml`:

```bash
sudo tee /etc/containerd/config.toml >/dev/null <<'EOF'
disabled_plugins = ["cri"]

root = "/userdata/containerd"
EOF
```

Key configuration items:

| Item | Description |
| ------------------ | --------------------------------------------------------- |
| `disabled_plugins` | Disable the CRI plugin of the system containerd so that it does not conflict with the containerd bundled with K3s |
| `root`             | Point the containerd data directory to the ext4 partition `/userdata/containerd` instead of overlayfs |

**Restart and verify**

Restart containerd after the configuration:

```bash
sudo systemctl restart containerd
sudo systemctl is-active containerd
```

Confirm the following in the output:

```text
active
```

## Special Cases

### Restrict the LightDM Realtime Scheduling Permission [step]

If the device also runs a graphical desktop environment, LightDM may use a high realtime scheduling priority.

In some environments this prevents K3s from setting up CPU cgroups when creating containers, and Pods may stay in the `CreateContainerError` state.

**Only apply the configuration below if you actually run into this problem after installing K3s.**

Edit the LightDM service:

```bash
sudo systemctl edit lightdm.service
```

Add:

```ini
[Service]
RestrictRealtime=yes
CPUSchedulingPolicy=other
CPUSchedulingPriority=0
```

Save the file, then reload the configuration and restart LightDM:

```bash
sudo systemctl daemon-reload
sudo systemctl restart lightdm.service
```

Check the K3s Pod status again after the restart.

If LightDM is not installed, or if K3s runs normally, this configuration is not required.
