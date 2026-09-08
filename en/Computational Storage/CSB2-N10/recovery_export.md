# Device rootfs Export and Firmware Repackaging

> Version: Official release
>
> This manual is used with:
> - Base firmware: the base image currently flashed on the customer device
> - `exportctl` built into recovery (located at `/usr/bin/exportctl`)
> - PC tool: `firmware-kits`
>
> This manual describes how to complete the following tasks:
> 1. Flash the provided firmware to the device
> 2. Deploy the required environment on the device
> 3. Enter recovery and export the current rootfs
> 4. Return to the PC and repackage the firmware with `firmware-kits`

---

## Contents

1. [Overall Workflow](#1-overall-workflow)
2. [Flash the Firmware](#2-flash-the-firmware)
3. [Deploy the Environment on the Device](#3-deploy-the-environment-on-the-device)
4. [Enter Recovery and Export the Rootfs](#4-enter-recovery-and-export-the-rootfs)
5. [PC: Repackage the Firmware with firmware-kits](#5-pc-repackage-the-firmware-with-firmware-kits)
6. [FAQ](#6-faq)
7. [Appendix: exportctl Quick Reference](#7-appendix-exportctl-quick-reference)

---

## 1. Overall Workflow

```text
1. Flash the base firmware (the recovery partition already contains exportctl)
2. Deploy software, files, and configuration in the normal system
3. Enter recovery and run exportctl to export the rootfs
4. Copy the exported rootfs.img to the PC
5. Use firmware-kits on the PC to unpack the base firmware
6. Replace the rootfs at the rootfs step / adjust partition sizes / add content
7. Package the new firmware, then flash and verify it
```

---

## 2. Flash the Firmware

Use the existing customer-side flashing tool to flash the base firmware.

```text
<base-firmware>.img
```

- The recovery partition already contains `exportctl`; no additional installation is required.
- Start the device normally and enter the system after flashing is complete.

---

## 3. Deploy the Environment on the Device

Complete the deployment in the device's normal system, for example:

```bash
sudo apt-get install -y <your-software>
sudo cp <your-file> /opt/
```

Note: Changes made during deployment are retained in the writable layer and are included in the subsequent export.

---

## 4. Enter Recovery and Export the Rootfs

### 4.1 Enter Recovery

Run the following command in the device's normal system:

```bash
sudo reboot recovery
```

After entering recovery, log in through the serial port or SSH and confirm that the device is in recovery:

```bash
uname -a
```

### 4.2 View the Exportable Platforms

```bash
exportctl list
```

The output is expected to look similar to the following:

```text
rk3588-firefly
bm1684
```

### 4.3 Export the Rootfs

Export the complete merged rootfs:

```bash
exportctl -o /dev/mmcblk0p7 -m merged
```

The `-o` option can also specify an ext4 partition directly, such as `/dev/sda1`. To export to a directory, use a mounted directory:

```bash
exportctl -o /dev/sda1 -m merged

mkdir -p /mnt/usb
mount /dev/sda1 /mnt/usb
exportctl -o /mnt/usb -m merged
umount /mnt/usb
```

Notes:

- `-o` can specify either an ext4 block device or a mounted directory.
- `-m merged` exports the complete rootfs and is the recommended default mode.
- The command automatically identifies the platform, mounts the source, imports the data, and generates `rootfs.img`.

> The export target must be an ext4 partition. Use ext4 for official deliveries.

### 4.4 Export Results

Output directory format:

```text
<output-directory>/<platform>-<system>-<version>-<timestamp>/
```

The default output is:

```text
rootfs.img
```

To export a directory tree, add `--no-img`.

### 4.5 Other Optional Parameters

| Parameter | Description |
| ---- | ---- |
| `-m ro` | Export the read-only base layer only |
| `-m rw` | Export the writable layer only |
| `-m userdata` | Export the data partition only |
| `--no-img` | Export the directory tree without packaging an img file |
| `--keep-identity` | Preserve the machine-id and SSH host keys |

### 4.6 Common Errors

| Error | Resolution |
| ---- | ---- |
| `Not an ext4 file system` | Use only an ext4 partition as the export target |
| `Output device is already mounted` | Unmount other mount points first, or use the directory method |
| `Insufficient output space` | Free space or use a larger storage device |

---

## 5. PC: Repackage the Firmware with firmware-kits

### 5.1 Preparation

- Base firmware package
- The `rootfs.img` exported in Section 4
- PC environment: Ubuntu 20.04/22.04 x86_64 with the required dependencies installed

### 5.2 Start the Workflow

Run the following commands in the `firmware-kits` directory:

```bash
cd firmware-kits
sudo ./firmware-kits run -l flow/rk3588.yaml -f ./<base-firmware>.img
```

The workflow pauses when manual processing is required. It usually first asks whether to use the `rootfs.img` exported from recovery to replace the unpacked rootfs:

1. Select `y` and provide the path to the exported `rootfs.img` as prompted.
2. Select `N` to continue using the rootfs unpacked from the base firmware.
3. Follow the prompts to adjust the partition sizes.
4. Continue to the rootfs step to add files or modify the configuration.

### 5.3 Adjust Partitions

If partition sizes do not need to be changed, continue directly when prompted:

```bash
sudo ./firmware-kits resume
```

If changes are required, complete the operation at the pause point and then continue according to the workflow prompts.

### 5.4 Replace Content

After adjusting the partitions, enter the rootfs step to add files or modify the configuration. When finished, continue with:

```bash
sudo ./firmware-kits resume
```

### 5.5 Package and Output

The workflow generates a new firmware package after it completes.

---

## 6. FAQ

**Q1: Is it necessary to enter recovery for the export?**
Yes. Recovery is better suited for exporting a consistent and clean rootfs.

**Q2: Why is the exported `rootfs.img` smaller than the system partition?**
The export process shrinks the image, which is expected.

**Q3: Will the default process make every device identical?**
No. By default, it resets the machine-id and SSH host keys.

**Q4: Can I store the exported file in `/tmp` in recovery?**
Yes, it can be used temporarily, but it is not recommended as the official export target. Prefer an ext4 block device or a mounted directory.

**Q5: What should I do if the USB drive is reported as not ext4?**
Format the USB drive partition as ext4 and try again.

**Q6: What should I do if the export is interrupted?**
Run `exportctl` again. A new output directory is generated with a new timestamp.

**Q7: Does the default export contain only `rootfs.img`?**
Yes. The default process directly generates `rootfs.img`.

**Q8: Does the export consume space on the device's userdata partition?**
Yes. Before exporting to a device partition, confirm that the target partition has sufficient free space.

**Q9: How do I confirm that the new firmware upgrade succeeded?**
After flashing, confirm that the system starts normally and that the deployed content is present.

**Q10: How do I confirm that the new firmware contains my deployed content?**
Check the corresponding paths after flashing, such as the files under `/opt/`.

---

## 7. Appendix: exportctl Quick Reference

```text
exportctl [-p <platform>] -o <target> [-m <mode>] [--no-img] [--keep-identity]
exportctl list
exportctl pack -i <tree-directory> -o <img-path> [-l <label>]

-o <target>   ext4 block device or mounted directory
-p <platform> Manually specify the platform (rk3588-firefly / bm1684)
-m <mode>     merged (default) | ro | rw | userdata
--no-img      Export the directory tree only; do not package an img file
--keep-identity  Do not reset the machine identity
```

---