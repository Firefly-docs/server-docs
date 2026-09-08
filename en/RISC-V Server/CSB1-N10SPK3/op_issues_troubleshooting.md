# Troubleshooting


# Access

## Q: Can aBMC be accessed remotely?

A: Yes. aBMC is an independent out-of-band management unit. When the device is powered on and the management network port is connected, it supports remote access to the Web management interface and the Redfish/IPMI interfaces, enabling remote power on/off, KVM console, hardware monitoring, virtual media, and other operations and maintenance tasks.
Local LAN access: In the same intranet environment, the computer and the aBMC management network are interconnected; enter the aBMC intranet IP to access it directly;
* Cross-region access over the Internet: Ports cannot be exposed directly to the public network. There are two compliant solutions:
 1. The terminal connects to the device's local LAN through a VPN, and then accesses aBMC;
 2. Deploy a cloud proxy server; the intranet aBMC proactively establishes a long connection outward, and remote operations and maintenance over the Internet are achieved through the proxy.
 <Callout title="Security Reminder" type="warn">
      It is forbidden to open aBMC service ports directly to the public network, as this poses a high-privilege security risk; for remote operations and maintenance across external networks, prefer VPN or cloud proxy solutions.
</Callout>

## Q: aBMC becomes inaccessible while the server is running?

A: First distinguish between the two fault scenarios and troubleshoot:

* Scenario 1: The aBMC management IP cannot be pinged
    First troubleshoot the physical link and Layer 2/Layer 3 network: network cable, switch port status, IP subnet, VLAN division, and access control policies.

* Scenario 2: The aBMC IP can be pinged, but the Web management interface cannot be accessed
    1. If an external monitor can be connected on site for troubleshooting
        If the monitor normally outputs the terminal/system screen, the underlying operating system is running normally. You can run an access test on the local machine: `https://127.0.0.1`
            1.1 If the local machine can open the aBMC page normally: the aBMC business service is running normally, and the fault lies in the network between the O&M terminal and the device. Troubleshooting directions: subnet connectivity, switch ACL policies, VPN, proxy, etc.;
            1.2 If the local machine also cannot access the aBMC page: determine that the aBMC service is abnormal, and troubleshoot in the following order
                1.2.1 Check the service running status
                    `systemctl status aBMC`
                1.2.2 If the service status is active (running): check the business configuration
                    `cat /etc/aBMC/config.ini`
                    1.2.2.1 Confirm that the access protocol is enabled (HTTPS by default)
                    1.2.2.2 Confirm whether the Web listening port has been modified
                    1.2.2.3 Confirm the Redis communication port configuration
                1.2.3 If the service is abnormal or not started: troubleshoot the cause of the service startup failure based on the logs.

    2. If an external monitor cannot be connected on site, or the monitor is black or distorted with no output
        ```bash
        2.1 Use a Console debug cable (USB to RJ45 serial, baud rate 115200) to connect to the serial terminal and log in to the command-line system;
        2.2 After successful login, reuse the 1.2 procedure above to troubleshoot the aBMC service issue.
        ```
    3. If neither the monitor terminal login nor the Console login responds, it is most likely that the BMC program is stuck or the system has crashed. Common causes:
        3.1 A user business process running with root privileges causes a kernel exception
            3.1.1 First check the system logs and kernel logs to locate the application BUG;
            3.1.2 If a kernel crash Oops/panic occurs, the logs often cannot be persisted to disk. It is recommended to continuously capture serial logs to reproduce the issue, so that development can locate the root cause.

        3.2 The BMC system partition disk is full (a frequent customer issue)
            This is mostly caused by users deploying their own business applications that generate a large amount of debug logs and temporary files without periodic cleanup, exhausting the disk and causing system function anomalies.

        3.3 Improper mount configuration in `/etc/fstab` modified by the user blocks system startup
            If the `nofail` parameter is not added when configuring automatic disk mounting at boot in `/etc/fstab`, the system will fall into a startup blocking state waiting for a timeout once an anomaly occurs during mounting. Typical scenarios that commonly cause this fault include:
            3.3.1 Mismatched resource identifiers: The disk UUID fixed during development and debugging does not match the actual production environment, and was not corrected before deployment.
            3.3.2 Underlying hardware failure: The disk has a physical fault, causing the kernel to fail to get a device response and the mount request to be blocked permanently.
            3.3.3 Device naming drift: Mounting that depends on device paths (such as `/dev/sda1`) becomes invalid because the kernel enumeration order changes after a system restart.
            3.3.4 Unprepared storage media: The newly added disk was not formatted or partitioned, so the system cannot recognize a valid file system structure when mounting.

## Q: How to use Redis on aBMC?

A: Redis 7.2 is built into the aBMC system by default. If a business service requires Redis, it can directly use the instance shipped with the system; no reinstallation or deployment is needed.
To customize the Redis port, database number, or access password:
    1. Modify the Redis-related parameters in the configuration file `/etc/aBMC/config.ini`;
    2. Restart the aBMC service for the new configuration to take effect.

<Callout title="Tip" type="info">
    Tip: Do not manually install and start additional Redis processes separately, to prevent system anomalies such as port conflicts and resource contention.
</Callout>