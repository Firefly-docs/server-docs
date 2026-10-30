# Wiki Release Notes

Records updates to the entire Wiki documentation site, summarized by month.

## Batch Overview

| Batch | Highlights |
| --- | --- |
| [2026.10](#202610) | aBMC release notes; network isolation/sharing cases; CSA1 power and troubleshooting |
| [2026.09](#202609) | K3s documentation; log collection; troubleshooting; product renaming |
| [2026.08](#202608) | Redfish API documentation; BMC upgrade and power operations; product docs |
| [2026.07](#202607) | Initial documentation site; aBMC manual and English version |

## 2026.10

**Added**

- aBMC release notes page, covering v1.0 – v3.2.3, with package download links (aBMC)
- New "Sub-board communication management" guide (aBMC)
- New "Sub-board serial collection" guide (aBMC)
- New network isolation and network sharing application cases, plus English versions (General)
- New "Single NIC, multiple subnets" application case, with Chinese/English topology diagrams (General)
- CSA1-N8 Quick Start: new power operations page (Product Manuals)
- CSA1 troubleshooting: new Q&As for TPU, pci_net, eMMC, and SATA (Product Manuals)

**Changed**

- Update records moved to a standalone section; aBMC release notes switched to a timeline layout (General)
- Network isolation/sharing cases: application scenarios rewritten; gateway priority and route metric notes added (General)
- Topology diagrams now use PNG references (General)

**Fixed**

- Fixed 404 links on the Redfish overview page (Redfish)
- Corrected CSA1 power safety wording (Product Manuals)

## 2026.09

**Added**

- K3s documentation expanded: installation, application deployment, cluster operations, Longhorn storage, AIC deployment, and uninstall (K3s)
- New log collection page op_log_collection, with fflog scenarios and a command reference (General)
- Troubleshooting: new "sub-board disconnected" section; Sophgo disk-full branch added (General)
- New "K3 Platform Software Development" shared page, shown under B1/C2 software development (General)
- aBMC access FAQ: added a "recover login password" Q&A (aBMC)

**Changed**

- start_login_sub reorganized by "channel × entry point" (Product Manuals)
- CSD2-Nx renamed to CSD2-N96-S3; new customization category CSD2-N96-M2 (Product Manuals)
- Product specification tables now distinguish node count and expansion disks by product (Product Manuals)
- K3s Chinese/English sync; architecture and other images switched to English versions (K3s)

**Fixed**

- Unified troubleshooting links to suffix-free relative paths (General)
- Corrected the sub-node serial login command (Product Manuals)
- Fixed misaligned labels in wiring and USB port diagrams (Product Manuals)

## 2026.08

**Added**

- Added the Redfish API documentation directory tree; Redfish documentation started (Redfish)
- Added the Common "BMC Upgrade" page (General)
- Added power operations and sub-board boot policy documentation (General)
- aBMC: added an architecture diagram and FAQ (aBMC)
- Added CSA1-N8, CSB1-N10, and CSD2 series product documentation (Product Manuals)

**Changed**

- Restructured the product introduction, sub-board firmware, and login documentation (General)
- Added L3 switch network specifications (Product Manuals)

**Fixed**

- Fixed multiple image paths, missing images, and syntax errors (General)

## 2026.07

**Added**

- Initial Wiki documentation site launched (General)
- aBMC manual established: time management, thermal management, port mapping, casting, firmware upgrade, disk management, file sharing, and sub-board network management (aBMC)
- aBMC English documentation launched (aBMC)

**Changed**

- aBMC documentation restructured; pages moved to a Tabs/Step layout with updated screenshots (aBMC)
- English UI labels and directory names adjusted (aBMC)

**Fixed**

- Redacted sub-board network and other screenshots (General)
- Fixed image paths and directory migration errors (General)