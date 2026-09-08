# K3s

## Overview

This document describes how to install and use K3s on Firefly series servers, and guides users through deploying business applications to a K3s cluster.

The document covers the basic concepts of K3s, pre-deployment preparation, Server and Agent installation, cluster verification, as well as common operations required for application deployment and daily operations and maintenance. Readers can choose the relevant chapters according to their actual deployment environment.

## Intended Audience

This document is intended for:

- Enterprise administrators
- Enterprise end users
- Technical personnel responsible for server deployment, application delivery, and cluster operations and maintenance

Readers should have basic experience with Linux operations and container usage, and understand the basic operations of networking, process management, and command-line tools.

## Why Use K3s

### Docker: A Unified Runtime Environment for Customer Applications

Customers need to run their own applications on different types of platforms, such as embedded platforms from Rockchip, Allwinner, and others. Different platforms usually have differences in processor architecture, system components, and file systems. Adapting applications for each platform directly incurs high development and maintenance costs.

With Docker, customers can package their applications and runtime dependencies into images and use a unified delivery and startup approach across different platforms. Customers do not need to adapt their business applications to each vendor's file system separately, and DevOS can also provide a consistent runtime environment for applications through Docker.

### Docker Compose and K3s: For Deployments of Different Scales

The deployment method should be selected based on the number of devices and system scale:

- **Single-node devices or boxes**: Use Docker Compose to manage multiple related containers. It is simple to configure and suitable for application deployment and maintenance on a single device.
- **Multi-node cluster systems**: Use Kubernetes to manage containerized applications. It is suitable for scenarios that require service orchestration, service discovery, load balancing, fault recovery, and rolling updates.
- **Embedded device clusters**: Use K3s. K3s retains the core capabilities of Kubernetes while reducing resource consumption and deployment complexity, making it more suitable for embedded devices with limited CPU, memory, and storage resources.

### K3s: Kubernetes for Embedded Clusters

K3s helps users to:

- Deploy and manage applications in a unified containerized way
- Schedule workloads across multiple nodes
- Provide stable service access endpoints through Service and Ingress
- Implement application updates and fault recovery with Deployment
- Persist application data with storage volumes
- Perform daily operations and maintenance using standard Kubernetes tools and resource models

### Multi-Cluster Management

For data center administrators, multiple Kubernetes or K3s clusters may exist simultaneously in a real environment. Each cluster can independently host a set of business workloads, but logging into and maintaining multiple clusters separately increases O&M costs.

Multi-cluster management platforms such as Rancher can be used to bring multiple Kubernetes and K3s clusters into a unified management interface. Administrators can view cluster status, nodes, and workloads from a single entry point, manage namespaces, applications, user permissions, and cluster configurations, and switch to different clusters to perform operations and maintenance as needed.

Therefore, Docker is suitable for providing a unified application runtime environment, Docker Compose is suitable for managing containers on single-node devices, K3s is suitable for resource-constrained embedded clusters, and platforms such as Rancher are suitable for data center administrators to manage multiple Kubernetes or K3s clusters in a unified manner.

For specific deployment scale and hardware resource requirements, refer to [System Requirements](systemneed.md).


## Detailed Documentation

For the complete deployment, configuration, application release, and daily operations and maintenance procedures of K3s, please refer to the [K3s User Manual](/docs/server/bmc-software/K3s/preface).