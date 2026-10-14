# Software Installation Guide

This guide describes how to download aBMC packages, the version directory structure, and the installation and upgrade steps for the BMC and sub-boards.

## Download

The aBMC packages are shared through Baidu Netdisk. The shared directory is **aBMC**:

[Download link](https://pan.baidu.com/s/1uPwUVfheglaChoFf7bUY-A?pwd=xfiy) (access code: `xfiy`)

### Directory Structure

The aBMC directory is divided into subdirectories by version. Versions are ordered from newest to oldest, and the first one is the latest version.

```text
aBMC
├── v3.2.0
├── v3.1.3
└── v2.9.4
```

Taking v3.2.0 as an example, the version directory contains the following files:

| File name | Description |
| :--- | :--- |
| `firefly-abmc-server_3.2.0-1791956975_arm64.deb` | Server program |
| `firefly-abmc-tools_1.1.0_arm64.deb` | Toolset |
| `firefly-abmc_3.2.0-1791956975_all.deb` | Meta package that installs the server and toolset in one pass |
| `firefly-abmc-client_1.0.1_all.deb` | Sub-board dependency package |
| `readme_zh.txt` / `readme_en.txt` | Installation commands and update log |

## Installation and Upgrade

When installing or upgrading aBMC, both the BMC and the sub-boards must be connected to the network (with access to the software repositories), so that `apt` can resolve and install the dependencies.

### BMC [step]

Upload the following deb packages to the BMC:

- `firefly-abmc-server*.deb`
- `firefly-abmc-tool*.deb`
- `firefly-abmc_*all.deb`

Log in to the BMC, change to the directory containing the deb packages, and run:

```bash
sudo apt install -y ./firefly-abmc-server*.deb ./firefly-abmc-tool*.deb ./firefly-abmc_*all.deb
```

### Sub-Board [step]

Upload the following deb package to the sub-board:

- `firefly-abmc-client*.deb`

Log in to the sub-board, change to the directory containing the deb package, and run:

```bash
sudo apt install -y ./firefly-abmc-client*.deb
```

### Verify [step]

Log in to the BMC and run the following command to check the aBMC service status:

```bash
systemctl status aBMC
```

A service status of `active (running)` indicates that the installation or upgrade succeeded. You can then log in to the aBMC web management page; see the [Login Guide](guide.md) for details.
