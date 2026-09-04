# Secondary Customization Firmware


## 1. Introduction
`firmware-kits` is a secondary customization firmware assistant tool developed by our company. The tool provides the following features:
1. Re-extract firmware upgrade packages
2. Modify or replace the file system
3. Replace partitions
4. Assistant tool for repacking upgrade packages

## 2. Development Vision
1. In factory mass-production delivery scenarios, users often want to embed development and production tools into the firmware upgrade package to ensure consistency of production versions. This tool aims to provide a convenient implementation solution and lower the cost of standardizing versions.
2. Build a firmware customization and pre-debugging environment that is independent of real hardware. Based on X86‑ARM64 QEMU virtualization technology, the target file system can be run directly; developers can complete application pre-installation, custom configuration, and debugging in the virtual environment without relying on real hardware, reducing hardware resource usage and effectively lowering development and hardware investment costs.



## 3. Firmware Preprocessing

`firmware-kits` is used to preprocess BM1684 and RK3588 firmware packages before the official firmware upgrade. It can unpack the original firmware following a specified flow, adjust the sizes of partitions such as ROOTFS/DATA/CUSTOM, mount the rootfs for manual modification of system contents, and repack the firmware into a package that can be delivered or used for upgrading after the modifications are complete.

The tool is suitable for scenarios where firmware contents need to be customized before upgrading, such as expanding partitions, preloading files, adjusting rootfs configuration, and generating TFTP or SD card upgrade packages. For ordinary firmware upgrades, the upgrade management features described earlier on this page should still be preferred; use `firmware-kits` only when the firmware needs to be modified or repacked first.

If your workflow includes exporting the rootfs on the device and repackaging the firmware on a PC, also refer to [Device rootfs Export and Firmware Repackaging](recovery_export.mdpp).

The following describes how to use `firmware-kits` to unpack, modify, and repack BM1684 and RK3588 firmware. Follow the steps strictly.

All commands are executed in the `firmware-kits` directory.

## 4. Environment Preparation

### 4.1 Operating System Requirements

An Ubuntu 20.04/22.04 x86_64 host is recommended.

### 4.2 Install Dependencies
<CodeBlockTabs defaultValue="debian_ubuntu">
    <CodeBlockTabsList>
        <CodeBlockTabsTrigger value="debian_ubuntu">Debian / Ubuntu</CodeBlockTabsTrigger>
        <CodeBlockTabsTrigger value="opensuse">openSUSE</CodeBlockTabsTrigger>
        <CodeBlockTabsTrigger value="fedora_rhel">Fedora / RHEL</CodeBlockTabsTrigger>
    </CodeBlockTabsList>

    <CodeBlockTab value="debian_ubuntu">
    ```bash
    sudo apt update
    sudo apt install -y u-boot-tools qemu-user-static p7zip-full unzip e2fsprogs
    ```
    </CodeBlockTab>
    <CodeBlockTab value="opensuse">
    ```bash
    sudo zypper refresh
    sudo zypper install -y u-boot-tools qemu-user-static p7zip unzip e2fsprogs
    ```
    </CodeBlockTab>

    <CodeBlockTab value="fedora_rhel">
    ```bash
    sudo dnf refresh
    sudo dnf install -y u-boot-tools qemu-user-static p7zip unzip e2fsprogs
    ```
    </CodeBlockTab>
</CodeBlockTabs>
### 4.3 Download the Tool Package
### 4.4 Check the Tool Package Directory

Enter the tool package directory:

```bash
cd firmware-kits
```

Confirm that the directory contains at least the following contents:

```text
firmware-kits
flow/
scripts/
tools/
bin/
```

Confirm that the main program can be executed:

```bash
./firmware-kits --help
```

Firmware processing involves mounting, chroot, file system adjustment, and repacking, so `sudo` is required for official operations.

## 5. Basic Commands

Process the firmware from scratch:

```bash
sudo ./firmware-kits run -l <flow-file> -f <firmware-file>
```

Resume after the flow is paused:

```bash
sudo ./firmware-kits resume
```

Parameter description:

| Parameter | Description |
| ---- | ---- |
| `run` | Execute a firmware processing flow from scratch |
| `resume` | Continue execution from the position where it was last paused |
| `-l` | Specify the flow file, for example `flow/bm1684_tftp_data.yaml` |
| `-f` | Specify the original firmware file |
| `-o` | Advanced parameter, passed to the processing script; do not use it unless specifically instructed |

When the tool reaches a position that requires manual handling, it pauses automatically and prompts which command to execute next. After the manual operation is complete, run `sudo ./firmware-kits resume` to continue.

<Callout type="info" title="Important Notes">
  1. Do not delete `out/`, `.firmware-kits_state.json`, or `tools/.env` while the flow is paused.
  2. Do not shut down the host after the rootfs has been mounted unless you have confirmed that the unmount is complete.
  3. Process only one firmware flow at a time; do not run multiple `firmware-kits run/resume` commands in multiple terminals simultaneously.
  4. The output directory is `out/`. If the artifacts are no longer needed, run `sudo rm -rf out/` to free up space.
  5. If the flow reports that an interrupted record exists, run `sudo ./firmware-kits resume` first; do not directly re-run `run`.
</Callout>





## 6. Firmware Creation Operations

### 6.1 Obtain the Upgrade Firmware

### 6.2 Unpack the Upgrade Package
<CodeBlockTabs defaultValue="Rockchip">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="Rockchip">Rockchip</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="Sophgo">Sophgo</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="Rockchip">
  </CodeBlockTab>
  <CodeBlockTab value="Sophgo">
  </CodeBlockTab>
</CodeBlockTabs>


### 6.3 Expand the ROOTFS

<Callout type="info" title="Tip">
  Before modifying the ROOTFS contents and deploying new files, expand the ROOTFS partition and file system size in advance to reserve sufficient storage space, avoiding insufficient space when adding programs, configuration files, and resource data later, which could cause packaging failures or abnormal firmware operation.
</Callout>

<CodeBlockTabs defaultValue="Rockchip">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="Rockchip">Rockchip</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="Sophgo">Sophgo</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="Rockchip">
  </CodeBlockTab>
  <CodeBlockTab value="Sophgo">
  </CodeBlockTab>
</CodeBlockTabs>

### 6.4 Replace the File System


### 6.5 Regenerate the Upgrade Firmware Package
<CodeBlockTabs defaultValue="Rockchip">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="Rockchip">Rockchip</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="Sophgo">Sophgo</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="Rockchip">
  </CodeBlockTab>
  <CodeBlockTab value="Sophgo">
  </CodeBlockTab>
</CodeBlockTabs>






<CodeBlockTabs defaultValue="Rockchip">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="Rockchip">Rockchip</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="Sophgo">Sophgo</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="Rockchip">
  </CodeBlockTab>
  <CodeBlockTab value="Sophgo">
  </CodeBlockTab>
</CodeBlockTabs>






## FAQ

### 1. Insufficient Permissions Reported

Firmware processing involves mounting, chroot, and root-owned files. Confirm that the command was run with `sudo`.

If deleting the output directory fails, run:

```bash
sudo rm -rf out/
```

### 2. Unsure What to Execute Next

If the flow is paused, the terminal usually prompts the next command. Generally, you only need to:

1. View or modify the partition size as prompted, or modify the rootfs contents.
2. Run `sudo ./firmware-kits resume` when done.

### 3. What to Do If the Partition Size Does Not Need to Be Modified

At the partition size pause point, if no modification is needed, run directly:

```bash
sudo ./firmware-kits resume
```

### 4. What to Do If the rootfs Contents Do Not Need to Be Modified

At the rootfs content modification pause point, if no modification is needed, run directly:

```bash
sudo ./firmware-kits resume
```

### 5. apt Commands Show locale or /dev/pts Warnings

Some rootfs environments may display warnings related to locale or `/dev/pts`. As long as the command eventually succeeds, it generally does not affect firmware creation.

### 6. How to Confirm There Are No Leftover Mounts

After a normal flow completes, there are usually no leftover mounts. To check, run:

```bash
mount | grep firmware-kits/out
```

No output means there are no leftover mounts.

If there is output, contact the delivery personnel for handling; do not arbitrarily delete directories that are currently mounted.


### 7. Execution Records

Two actual execution records are provided with the package and can be downloaded to review the complete operation process:

- [Download BM1684 actual execution record](/docs-assets/server-docs/aBMC_img/v3.0/firmware-kits/bm1684-run-2026-07-28.zip)
- [Download RK3588 actual execution record](/docs-assets/server-docs/aBMC_img/v3.0/firmware-kits/rk3588-run-2026-07-28.zip)

The logs keep only the firmware processing commands that customers care about, terminal output, pause point operations, demonstrations of commands executed inside the rootfs, demonstrations of file copying, the final artifact paths, and the mount check results.


### 8. Recovery from an Abnormal Interruption

If you see a message similar to the following when running `run`:

```text
Interrupted record found, please use `sudo ./firmware-kits resume` to continue
```

It means the previous flow did not complete. Run:

```bash
sudo ./firmware-kits resume
```

If you confirm that the previous flow is no longer needed, clean up the state and output:

```bash
sudo rm -f .firmware-kits_state.json tools/.env
sudo rm -rf out/
```

After the cleanup, `run` can be executed again.