# K3s

## Overview

This document describes how to install and use K3s on Firefly series servers, and guides users through deploying their business applications to a K3s cluster.

The document covers the basic concepts of K3s, pre-deployment preparation, Server and Agent installation, cluster verification, as well as common operations required for application deployment and day-to-day maintenance. Readers can follow the relevant chapters according to their actual deployment environment.

## Intended Audience

This document is primarily intended for:

- Enterprise administrators
- Enterprise end users
- Technical personnel responsible for server deployment, application delivery, and cluster operations

Readers are expected to have basic experience with Linux operations and containers, as well as a working knowledge of networking, process management, and command-line tools.

## Why K3s

### Docker: A Unified Runtime Environment for Customer Applications

Customers need to run their applications on different types of platforms, such as embedded platforms from Rockchip and Allwinner. Different platforms usually differ in processor architecture, system components, and file systems. Adapting an application separately for each platform incurs high development and maintenance costs.

With Docker, customers can package their applications together with their runtime dependencies into images and use a unified delivery and startup method across platforms. Customers do not need to adapt their business applications to each vendor's file system, and DevOS can also leverage Docker to provide a consistent runtime environment for applications.

### Docker Compose and K3s: For Different Deployment Scales

The deployment method should be chosen according to the number of devices and the scale of the system:

- **Single-node devices or boxes**: Use Docker Compose to manage multiple related containers. It is simple to configure and well suited for deploying and maintaining applications on a single device.
- **Multi-node cluster systems**: Use Kubernetes to manage containerized applications. This is suitable for scenarios that require service orchestration, service discovery, load balancing, fault recovery, and rolling updates.
- **Embedded device clusters**: Use K3s. K3s retains the core capabilities of Kubernetes while reducing resource consumption and deployment complexity, making it better suited for embedded devices with limited CPU, memory, and storage resources.

### K3s: Kubernetes for Embedded Clusters

K3s helps users to:

- Deploy and manage applications in a unified container-based manner
- Schedule workloads across multiple nodes
- Provide stable service entry points through Service and Ingress
- Implement application updates and fault recovery with Deployment
- Persist application data using storage volumes
- Perform day-to-day operations using standard Kubernetes tools and resource models

### Multi-Cluster Management

For data center administrators, multiple Kubernetes or K3s clusters may coexist in a real-world environment. Each cluster can independently host a set of services, but logging in to and maintaining each cluster separately increases operational costs.

A multi-cluster management platform such as Rancher can be used to bring multiple Kubernetes and K3s clusters into a unified management interface. Administrators can view cluster status, nodes, and workloads from a single entry point; manage namespaces, applications, user permissions, and cluster configurations; and switch between clusters to perform operations as needed.

In summary, Docker is suitable for providing a unified application runtime environment, Docker Compose is suitable for managing containers on single-node devices, K3s is suitable for resource-constrained embedded clusters, and platforms such as Rancher are suitable for data center administrators to centrally manage multiple Kubernetes or K3s clusters.

For specific deployment scales and hardware resource requirements, refer to [System Requirements](systemneed.md).
