# System Requirements

K3s is a lightweight Kubernetes distribution, but running K3s and its components still requires certain system resources.

Whether K3s runs in a container or is configured as a native Linux system service, each node should meet the following minimum requirements. The resources listed in the table are for K3s and its components only, and do not include the additional resources required by business workloads.

## Prerequisites

- Every node in the cluster must use a unique host name.
- If multiple nodes use the same host name, or host names may be reused by automated configuration systems, configure a unique name for each node. When adding a node, use its corresponding unique host name as well.

## CPU and Memory Requirements

The following are the minimum CPU and memory requirements for a highly available K3s Server node:

| Deployment Scale | Nodes | vCPUs | Memory |
|---|---:|---:|---:|
| Small | Up to 10 | 2 cores | 4 GB |
| Medium | Up to 100 | 4 cores | 8 GB |
| Large | Up to 250 | 8 cores | 16 GB |
| X-Large | Up to 500 | 16 cores | 32 GB |
| XX-Large | More than 500 | 32 cores | 64 GB |

> The configurations above are the minimum resource baseline for a K3s Server node. In actual deployments, resources should also be increased as appropriate based on the number of nodes, the number of Pods, business workloads, and monitoring components.

## Software Requirements

K3s can run in a container or as a native Linux system service. When deployed in a container, Docker must be pre-installed on every node, and kernel parameters and the container runtime environment must be checked.

### Docker

- Docker Engine is installed and the Docker service is running.
- The device should use a Docker version and container images that match the system architecture.
- The Docker storage directory should have sufficient free space for images, containers, and runtime data.
- Before installing or upgrading Docker, complete the kernel compatibility check according to the official documentation:
  [Docker kernel check and installation](https://community.t-firefly.com/docs/software/other/Docker/docker-kernel-check).

Use the following commands to confirm that Docker is running properly:

```bash
docker --version
sudo systemctl is-active docker
sudo docker info
```

If you run Docker commands as a non-root user, make sure the user has been added to the `docker` group, or prefix the commands with `sudo`.

### Pre-Deployment Checks

Before installing K3s, confirm that:

- All nodes have unique host names and can reach each other over the network. If nodes cannot communicate with each other, refer to [Network Settings](https://community.t-firefly.com/docs/server/bmc-software/aBMC/subNetwork) for configuration.
- Nodes have the container runtime that matches the deployment method.
- The Docker service is running and able to pull or load the images required by K3s.
- The firewall and other security policies do not block the ports required by the K3s API Server, inter-node communication, or business services.
- The system time of all nodes is synchronized. If the time is inconsistent, refer to [Time Management](https://community.t-firefly.com/docs/server/bmc-software/aBMC/timeManager) for configuration.

## Currently Supported Hardware

The currently supported server models and their internal compute units are as follows:

- **Server**: CSB1-N10R3576 v1.3 × 1
  - **BMC**: Rockchip RK3588, Debian 12, 8 GB memory, 64 GB storage × 1
  - **Sub-boards**: Rockchip RK3576, Debian 12, 8 GB memory, 64 GB storage × 10
