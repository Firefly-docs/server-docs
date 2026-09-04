# aBMC

## Introduction

The Firefly Advanced Baseboard Management Controller (hereinafter referred to as aBMC) is an out-of-band centralized management software solution deployed on the BMC and designed specifically for high-density array servers. It provides full lifecycle management, from single-node hardware monitoring to resource pooling across the entire array, and supports remote O&M capabilities such as KVM over IP-KVM (network-based remote desktop) and virtual media mounting. aBMC offers standard Redfish interfaces and a comprehensive CLI, making it easy to integrate the system seamlessly into existing automated O&M platforms and enabling secondary development and the transition to intelligent operations.

![aBMC Backend Core Framework](../../../aBMC_img/common/aBMC-backend_core.png)

## Vision

1. Business: Help customers complete the entire deployment workflow—from rack mounting to official production launch of business systems—within 10 days, significantly shortening the service delivery cycle for array servers.
2. Operations: Overcome the long-standing pain points of traditional blade and array servers, such as scattered multi-node management, low efficiency in bulk maintenance, and insufficient hardware monitoring granularity, and achieve centralized, visualized, and efficient operations.
3. Development: Provide open, standardized Redfish and CLI interfaces that shield developers from underlying hardware differences, allowing them to operate the entire array of resources as easily as a single machine.
4. Ecosystem: All Firefly self-developed array server hardware natively adapts to the aBMC management and control framework. Once customer business software is integrated into the Firefly hardware ecosystem, subsequent compute expansion and device upgrades require no re-adaptation of management software, avoiding compatibility rework risks and continuously shortening iteration cycles.

![aBMC Backend Core Framework](../../../aBMC_img/common/aBMC-Framework.png)

## Key Terms

1. aBMC: The Firefly Advanced Baseboard Management Controller (hereinafter referred to as aBMC) is an out-of-band centralized management software solution deployed on the BMC and designed specifically for high-density array servers.
2. BMC: The Baseboard Management Controller is a dedicated controller on the server motherboard that runs independently of the main CPU and operating system. It is responsible for out-of-band hardware monitoring, power management, and remote O&M of the server.
3. Out-of-band management: Remote device management and control over an independent hardware channel, without relying on the server operating system.
4. Redfish: The "standardized REST API" for server management, allowing hardware from different vendors to be managed through a uniform set of interfaces. (Redfish is a modern server management standard based on RESTful APIs, developed by the DMTF organization to replace the legacy IPMI protocol. Through HTTP/HTTPS interfaces with JSON payloads, it enables remote discovery, monitoring, and management of server hardware—such as temperature, power, firmware versions, and network configuration—and is easier to integrate into development workflows while offering higher security.)
5. CLI: A command-line interactive tool for quickly performing bulk BMC O&M operations locally.
6. KVM: KVM is an abbreviation for Keyboard, Video, and Mouse. It is distinct from the hardware KVM switches found in server rooms that connect via VGA (rarely HDMI) and USB cables.
7. IP-KVM: A remote virtual console for remotely operating the server's display, keyboard, and mouse.
