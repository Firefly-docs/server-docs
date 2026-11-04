# Get the SDK and System Support

If you have secondary development needs, refer to the table below to obtain the SDK for each platform.

<table border="1" cellPadding="8" cellSpacing="0" width="100%">
  <thead>
    <tr>
      <th>Platform</th>
      <th>Chip Model</th>
      <th>SDK</th>
      <th>Description</th>
      <th>How to Obtain</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td rowSpan="9">Rockchip</td>
      <td rowSpan="5">RK3588 / RK3588S</td>
      <td>RK3588_Linux_5.10_SDK</td>
      <td>Kernel version 5.10; supports building buildroot, Ubuntu 18.04, Ubuntu 20.04, and Debian 11 firmware</td>
      <td rowSpan="7">Contact sales (<a href="mailto:sales@t-firefly.com">sales@t-firefly.com</a>) for the SDK download link</td>
    </tr>
    <tr>
      <td>RK3588_Linux_6.1_SDK</td>
      <td>Kernel version 6.1; supports building buildroot, Ubuntu 22.04, Ubuntu 24.04, and Debian 12 firmware</td>
    </tr>
    <tr>
      <td>RK3588_Android12_SDK</td>
      <td>Kernel version 5.10; supports building Android 12 firmware</td>
    </tr>
    <tr>
      <td>RK3588_Android12_AIC_SDK</td>
      <td>Kernel version 5.10; supports building Android 12 AIC images</td>
    </tr>
    <tr>
      <td>RK3588_Android14_SDK</td>
      <td>Kernel version 6.1; supports building Android 14 firmware</td>
    </tr>
    <tr>
      <td rowSpan="2">RK3576</td>
      <td>RK3576_Linux_6.1_SDK</td>
      <td>Kernel version 6.1; supports building buildroot, Ubuntu 22.04, Ubuntu 24.04, and Debian 12 firmware</td>
    </tr>
    <tr>
      <td>RK3576_Android14_SDK</td>
      <td>Kernel version 6.1; supports building Android 14 firmware</td>
    </tr>
    <tr>
      <td rowSpan="2">RK3399</td>
      <td>RK3399_Linux4.4_SDK</td>
      <td>Kernel version 4.4; supports building Ubuntu 18.04 firmware</td>
      <td><a href="https://pan.baidu.com/share/init?surl=C06Muo9HamcGEHnMO0B8Yg">Baidu Netdisk</a> (extraction code: 1234)</td>
    </tr>
    <tr>
      <td>RK3399_Android10.0_SDK</td>
      <td>Kernel version 4.19; supports building Android 10 firmware</td>
      <td><a href="https://pan.baidu.com/share/init?surl=XL6x7CGzG03zR238TcBSAA">Baidu Netdisk</a> (extraction code: 1234)</td>
    </tr>
    <tr>
      <td>NVIDIA</td>
      <td>Jetson Orin NX</td>
      <td>Jetson Linux</td>
      <td>Kernel version 5.15; supports building Ubuntu 22.04 firmware</td>
      <td><a href="https://docs.nvidia.com/jetson/archives/r36.3/DeveloperGuide/index.html">Visit the official website to obtain the SDK</a></td>
    </tr>
    <tr>
      <td>SpacemiT</td>
      <td>K3</td>
      <td>-</td>
      <td>Supports building buildroot, Bianbu, OpenHarmony, and other firmware</td>
      <td><a href="https://spacemit.com/community/document/info?lang=zh&amp;nodepath=hardware/key_stone/k3/k3_sw/k3_sdk_user_guide.md">Visit the official website to obtain the SDK</a></td>
    </tr>
  </tbody>
</table>

## System Support List

This chassis adopts a modular design and is compatible with multiple core boards, providing excellent flexibility and scalability. Users can select different core board models according to computing power requirements, interface configuration, and budget, and combine them into diverse complete-server solutions. The following sections describe the specifications and common combinations of each core board for reference during selection.

The BMC is equipped with Rockchip RK3588 or SpacemiT K3. The supported kernel and system versions for each core board are listed below.

<table border="1" cellPadding="8" cellSpacing="0" width="100%">
  <thead><tr><th style="width: 10%;">Position</th><th style="width: 28%;">Chip Platform</th><th style="width: 14%;">Kernel Version</th><th>System Version</th></tr></thead>
  <tbody>
    <tr><td rowSpan="3" style="text-align: center;">BMC</td><td rowSpan="2">Rockchip RK3588</td><td style="text-align: center;">kernel 5.10</td><td>Debian 11, Ubuntu 20.04, openEuler 24.03, Kylin V10</td></tr>
    <tr><td style="text-align: center;">kernel 6.1</td><td>Debian 12, Ubuntu 22.04, Ubuntu 24.04, openEuler 24.03, Kylin V10</td></tr>
    <tr><td>SpacemiT K3</td><td style="text-align: center;">kernel 6.18</td><td>Buildroot, Bianbu</td></tr>
    <tr><td rowSpan="9" style="text-align: center;">Sub-board</td><td rowSpan="2">Rockchip RK3588</td><td style="text-align: center;">kernel 5.10</td><td>Debian 11, Ubuntu 20.04, openEuler 24.03, Kylin V10</td></tr>
    <tr><td style="text-align: center;">kernel 6.1</td><td>Debian 12, Ubuntu 22.04, Ubuntu 24.04, openEuler 24.03, Kylin V10</td></tr>
    <tr><td rowSpan="2">Rockchip RK3588s</td><td style="text-align: center;">kernel 5.10</td><td>Debian 11, Ubuntu 20.04, openEuler 24.03, Kylin V10</td></tr>
    <tr><td style="text-align: center;">kernel 6.1</td><td>Debian 12, Ubuntu 22.04, Ubuntu 24.04, openEuler 24.03, Kylin V10</td></tr>
    <tr><td>Rockchip RK3576</td><td style="text-align: center;">kernel 6.1</td><td>Debian 12, Ubuntu 22.04, Ubuntu 24.04</td></tr>
    <tr><td>SpacemiT K3</td><td style="text-align: center;">kernel 6.18</td><td>Buildroot, Bianbu</td></tr>
    <tr><td>NVIDIA Jetson Orin Nano</td><td rowSpan="2" style="text-align: center;">kernel 5.15</td><td rowSpan="2">Ubuntu 22.04</td></tr>
    <tr><td>NVIDIA Jetson Orin NX</td></tr>
    <tr><td> Sophgo BM1684X </td><td style="text-align: center;">kernel 5.4</td><td>Ubuntu 20.04</td></tr>
  </tbody>
</table>