# BMC Firmware Upgrade

This document describes the BMC firmware upgrade procedure for the CSB1-N10SPK3. There are two main upgrade methods:

- When the system is accessible normally, enter **Loader** mode and then upgrade.
- When the BMC fails to start normally, enter **MaskRom** mode to perform a recovery upgrade.

<Callout title="Confirm Before Upgrading" type="warn">
  A BMC firmware upgrade rewrites the underlying storage contents. Before upgrading, confirm that the firmware matches the device model and that the power supply is stable. During the upgrade, do not cut the power, unplug the USB-A data cable, or repeat the upgrade command.
</Callout>

## Prepare Tools [step]

- A good-quality USB-A data cable.
- The official Rockchip upgrade tool.
- A Windows or Linux host.
- Firmware that matches the CSB1-N10SPK3 BMC, for example `update.img`.

## Install the Upgrade Tool [step]

<CodeBlockTabs defaultValue="windows">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="windows">Windows</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="linux">Linux</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="windows">
    1. Download [RKDevTool](https://www.t-firefly.com/doc/download/172.html#other_572).
      ![Download upgrade tool package](../../../servers_img/common/bmc_upgrade/download_page.png)
    2. Extract the tool package.
    3. Enter the `DriverAssitant_v5.1.1` directory and run `DriverInstall.exe`.
    4. To ensure the correct driver version, it is recommended to select **Driver Uninstall** first, and then **Driver Install**.
      ![Install Rockusb driver](../../../servers_img/common/bmc_upgrade/rkdevtools_driver.png)
    5. After the driver installation is complete, enter `RKDevTool_Release_*` and run `RKDevTool.exe`.
      ![Open the RKDevTool upgrade tool](../../../servers_img/common/bmc_upgrade/rkdevtools_found_loader.png)
  </CodeBlockTab>

  <CodeBlockTab value="linux">
    No device driver installation is required on Linux; however, `upgrade_tool`, `adb`, and `fastboot` must be installed.

    Download [Linux UpgradeTool](https://www.t-firefly.com/doc/download/172.html#other_571), extract it, and install it to a system path:

    ```bash
    unzip Linux_Upgrade_Tool_xxxx.zip
    cd Linux_UpgradeTool_xxxx
    sudo mv upgrade_tool /usr/local/bin
    sudo chown root:root /usr/local/bin/upgrade_tool
    sudo chmod a+x /usr/local/bin/upgrade_tool
    ```

    Download [Linux adb fastboot](https://en.t-firefly.com/doc/download/149.html), extract it, and install it to a system path:

    ```bash
    sudo mv adb /usr/local/bin
    sudo chown root:root /usr/local/bin/adb
    sudo chmod a+x /usr/local/bin/adb

    sudo mv fastboot /usr/local/bin
    sudo chown root:root /usr/local/bin/fastboot
    sudo chmod a+x /usr/local/bin/fastboot
    ```
  </CodeBlockTab>
</CodeBlockTabs>

## Enter Upgrade Mode [step]

Connect the host to the server's OTG port using a USB-A data cable.

![Connect the OTG port](../../../servers_img/CSB1-N10SPK3/bmc_upgrade/bmc_upgrade_usb_port.png)

There are multiple ways to enter Loader mode. Choose any one of the following methods.


<CodeBlockTabs defaultValue="loader_by_cmd">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="loader_by_cmd">Command</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="loader_by_button">Button</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="loader_by_cmd">
  ### Enter Loader via System Command

  After the USB-A data cable is connected, you can enter the system through a serial debug terminal, `adb shell`, or an HDMI keyboard, and run:

  ```bash
    reboot loader
  ```
  </CodeBlockTab>

  <CodeBlockTab value="loader_by_button">
    ### Enter Loader via the Recovery Button

    Power off the device, hold down the **RECOVER** button on the BMC board, and then power on the server. After the host recognizes the Loader device, release the **RECOVER** button and flash the firmware following the normal procedure.

    ![Location of the RECOVER button](../../../servers_img/CSB1-N10SPK3/bmc_upgrade/loader_recovery_button.png)
  </CodeBlockTab>
</CodeBlockTabs>

## Confirm Loader Mode [step]
Depending on the operating system, the upgrade tool and operation method used to confirm Loader mode also differ.

<CodeBlockTabs defaultValue="windows">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="windows">Windows</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="linux">Linux</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="windows">
    Open `RKDevTool.exe`. If the BMC has entered Loader mode, the bottom of the tool will show **Found One LOADER Device**.

    ![RKDevTool recognizes a Loader device](../../../servers_img/common/bmc_upgrade/rkdevtools_found_loader.png)

    If `reboot loader` has already been executed but the tool still does not recognize the device, open Windows Device Manager and check whether a **Rockusb Device** is present.

    ![Rockusb Device in Device Manager](../../../servers_img/common/bmc_upgrade/win_found_rk_devices.png)

    If there is no **Rockusb Device**, reinstall the Rockusb driver, or try again after changing the USB port and the USB-A data cable.
  </CodeBlockTab>

  <CodeBlockTab value="linux">
    Run `upgrade_tool` to check the connection status. If the BMC has entered Loader mode, `Loader` will appear in the output.

    ```bash
    sudo upgrade_tool
    ```

    Example output:

    ```bash
    List of rockusb connected
    DevNo=1 Vid=0x2207,Pid=0x330c,LocationID=106    Loader
    Found 1 rockusb,Select input DevNo,Rescan press <R>,Quit press <Q>:q
    ```
  </CodeBlockTab>

</CodeBlockTabs>


## Flash the Firmware [step]
### Full Firmware Flashing
The complete firmware image is flashed to all storage partitions in one pass, overwriting the contents of all partitions, including the system, configuration, data, and all other information. It is suitable for fresh flashing, major firmware version upgrades, and system exception recovery scenarios. After execution, the original user configuration is lost, and the device is restored to factory state after flashing. It is intended for customers who need a full device firmware reset or complete upgrade.
<CodeBlockTabs defaultValue="windows">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="windows">Windows</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="Linux">Linux</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="windows">
    #### Windows Tool

    The unified firmware is usually `update.img`. The flashing steps are as follows:

    1. Open `RKDevTool.exe`.
    2. Switch to the **Upgrade Firmware** page.
    3. Click **Firmware** and select the `update.img` to be upgraded.
    4. Confirm that the tool displays the firmware information.
    5. Click **Upgrade** to start the upgrade.

    If the upgrade fails, try clicking **EraseFlash** first to erase the Flash, and then upgrade again.

    ![Flash update.img on Windows](../../../servers_img/common/bmc_upgrade/rkdevtools_found_loader.png)
  </CodeBlockTab>

  <CodeBlockTab value="Linux">
    #### Linux Tool

    ```bash
    sudo upgrade_tool uf update.img
    ```
  </CodeBlockTab>
</CodeBlockTabs>
### Single-Partition Flashing

Only the specified single partition is flashed and updated, without changing the data in other partitions. It supports updating individual components such as the kernel and U‑Boot while preserving the user configuration and business data in the remaining partitions. It is mostly used for minor version iteration debugging and localized fault repair, with higher upgrade efficiency and no need to reflash the entire firmware. It suits targeted customers who only need to update a specific partition without flashing the whole firmware.
<CodeBlockTabs defaultValue="windows">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="windows">Windows</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="Linux">Linux</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="windows">
    #### Windows Tool

    When only one or more partitions need to be updated, partition images can be flashed:

    1. Switch to the partition flashing page.
    2. Select the partitions to be flashed.
    3. Confirm that the image path for each partition is correct.
    4. Click **Run** to start the upgrade.
    5. The device will restart automatically after the upgrade is complete.

    ![Flash partition images on Windows](../../../servers_img/common/bmc_upgrade/rkdevtools_flash_partitions.png)
  </CodeBlockTab>

  <CodeBlockTab value="Linux">
    #### Linux Tool

      ```bash
      sudo upgrade_tool di -b /path/to/boot.img
      sudo upgrade_tool di -r /path/to/recovery.img
      sudo upgrade_tool di -m /path/to/misc.img
      sudo upgrade_tool di -u /path/to/uboot.img
      sudo upgrade_tool di -dtbo /path/to/dtbo.img
      sudo upgrade_tool di -p parameter
      sudo upgrade_tool ul bootloader.bin
      ```

      If the upgrade fails due to a Flash anomaly, try low-level formatting or erasing the EMMC:

      ```bash
      sudo upgrade_tool lf update.img
      sudo upgrade_tool ef update.img
      ```

      ### Flash Dynamic Partitions via fastboot [step]

      ```bash
      adb reboot fastboot
      sudo fastboot flash vendor vendor.img
      sudo fastboot flash system system.img
      sudo fastboot reboot
      ```
  </CodeBlockTab>
</CodeBlockTabs>

## FAQ [step]

### Flashing Failure [step]

If **Download Boot Fail** appears during the flashing process, or the tool reports an upgrade failure, it is usually related to the following causes:

- The USB-A data cable is of poor quality.
- The data cable or port has poor contact.
- The USB port of the host does not provide enough power.
- The driver is not installed correctly.
- The firmware does not match the device model.

Suggestions:

1. Replace the USB-A data cable.
2. Change the USB port on the host.
3. Reinstall the Rockusb driver on Windows.
4. Confirm that the firmware package matches the BMC.
5. If it still fails, erase the Flash first and then flash again.

![Example of a flashing failure](../../../servers_img/common/bmc_upgrade/rkdevtools_failed.png)