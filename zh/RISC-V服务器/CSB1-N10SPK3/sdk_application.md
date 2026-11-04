# 获取 SDK 与系统支持

若您有二次开发需求，可通过下表获取各平台对应的 SDK。

<table border="1" cellPadding="8" cellSpacing="0" width="100%">
  <thead>
    <tr>
      <th>平台</th>
      <th>芯片型号</th>
      <th>SDK</th>
      <th>说明</th>
      <th>获取方式</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td rowSpan="9">Rockchip</td>
      <td rowSpan="5">RK3588 / RK3588S</td>
      <td>RK3588_Linux_5.10_SDK</td>
      <td>内核版本 5.10，可编译 buildroot、Ubuntu 18.04、Ubuntu 20.04、Debian 11 固件</td>
      <td rowSpan="7">联系商务（<a href="mailto:sales@t-firefly.com">sales@t-firefly.com</a>）获取 SDK 下载链接</td>
    </tr>
    <tr>
      <td>RK3588_Linux_6.1_SDK</td>
      <td>内核版本 6.1，可编译 buildroot、Ubuntu 22.04、Ubuntu 24.04、Debian 12 固件</td>
    </tr>
    <tr>
      <td>RK3588_Android12_SDK</td>
      <td>内核版本 5.10，可编译 Android 12 固件</td>
    </tr>
    <tr>
      <td>RK3588_Android12_AIC_SDK</td>
      <td>内核版本 5.10，可编译 Android 12 AIC 镜像</td>
    </tr>
    <tr>
      <td>RK3588_Android14_SDK</td>
      <td>内核版本 6.1，可编译 Android 14 固件</td>
    </tr>
    <tr>
      <td rowSpan="2">RK3576</td>
      <td>RK3576_Linux_6.1_SDK</td>
      <td>内核版本 6.1，可编译 buildroot、Ubuntu 22.04、Ubuntu 24.04、Debian 12 固件</td>
    </tr>
    <tr>
      <td>RK3576_Android14_SDK</td>
      <td>内核版本 6.1，可编译 Android 14 固件</td>
    </tr>
    <tr>
      <td rowSpan="2">RK3399</td>
      <td>RK3399_Linux4.4_SDK</td>
      <td>内核版本 4.4，可编译 Ubuntu 18.04 固件</td>
      <td><a href="https://pan.baidu.com/share/init?surl=C06Muo9HamcGEHnMO0B8Yg">百度网盘</a>（提取码：1234）</td>
    </tr>
    <tr>
      <td>RK3399_Android10.0_SDK</td>
      <td>内核版本 4.19，可编译 Android 10 固件</td>
      <td><a href="https://pan.baidu.com/share/init?surl=XL6x7CGzG03zR238TcBSAA">百度网盘</a>（提取码：1234）</td>
    </tr>
    <tr>
      <td>NVIDIA</td>
      <td>Jetson Orin NX</td>
      <td>Jetson Linux </td>
      <td>内核版本 5.15，可编译 Ubuntu 22.04 固件</td>
      <td><a href="https://docs.nvidia.com/jetson/archives/r36.3/DeveloperGuide/index.html">前往官方网站获取 SDK</a></td>
    </tr>
    <tr>
      <td>SpacemiT</td>
      <td>K3</td>
      <td>-</td>
      <td>可编译 buildroot、Bianbu、OpenHarmony 等固件</td>
      <td><a href="https://spacemit.com/community/document/info?lang=zh&amp;nodepath=hardware/key_stone/k3/k3_sw/k3_sdk_user_guide.md">前往官方网站获取 SDK</a></td>
    </tr>
  </tbody>
</table>

## 系统支持列表

本机箱采用模块化设计，可兼容搭载多种核心板，具备良好的灵活性与可扩展性。用户可根据算力需求、接口配置及成本预算，选配不同型号的核心板，组合出多样化的整机方案。下文将分别介绍各核心板的规格特点及其常见搭配方式，供选型时参考。

BMC 搭载瑞芯微 RK3588 或进迭时空 K3，各核心板支持的内核与系统版本如下。

<table border="1" cellPadding="8" cellSpacing="0" width="100%">
  <thead>
    <tr>
      <th style="width: 10%;">位置</th>
      <th style="width: 28%;">芯片平台</th>
      <th style="width: 14%;">内核版本</th>
      <th>系统版本</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td rowSpan="3" style="text-align: center;">BMC</td>
      <td rowSpan="2">Rockchip RK3588</td>
      <td style="text-align: center;">kernel 5.10</td>
      <td>Debian 11、Ubuntu 20.04、openEuler 24.03、麒麟 V10</td>
    </tr>
    <tr>
      <td style="text-align: center;">kernel 6.1</td>
      <td>Debian 12、Ubuntu 22.04、Ubuntu 24.04、openEuler 24.03、麒麟 V10</td>
    </tr>
    <tr>
      <td>SpacemiT K3</td>
      <td style="text-align: center;">kernel 6.18</td>
      <td>Buildroot、Bianbu</td>
    </tr>
    <tr>
      <td rowSpan="9" style="text-align: center;">子板</td>
      <td rowSpan="2">Rockchip RK3588</td>
      <td style="text-align: center;">kernel 5.10</td>
      <td>Debian 11、Ubuntu 20.04、openEuler 24.03、麒麟 V10</td>
    </tr>
    <tr>
      <td style="text-align: center;">kernel 6.1</td>
      <td>Debian 12、Ubuntu 22.04、Ubuntu 24.04、openEuler 24.03、麒麟 V10</td>
    </tr>
    <tr>
      <td rowSpan="2">Rockchip RK3588s</td>
      <td style="text-align: center;">kernel 5.10</td>
      <td>Debian 11、Ubuntu 20.04、openEuler 24.03、麒麟 V10</td>
    </tr>
    <tr>
      <td style="text-align: center;">kernel 6.1</td>
      <td>Debian 12、Ubuntu 22.04、Ubuntu 24.04、openEuler 24.03、麒麟 V10</td>
    </tr>
    <tr>
      <td>Rockchip RK3576</td>
      <td style="text-align: center;">kernel 6.1</td>
      <td>Debian 12、Ubuntu 22.04、Ubuntu 24.04</td>
    </tr>
    <tr>
      <td>SpacemiT K3</td>
      <td style="text-align: center;">kernel 6.18</td>
      <td>Buildroot、Bianbu</td>
    </tr>
    <tr>
      <td>NVIDIA Jetson Orin Nano</td>
      <td rowSpan="2" style="text-align: center;">kernel 5.15</td>
      <td rowSpan="2">Ubuntu 22.04</td>
    </tr>
    <tr>
      <td>NVIDIA Jetson Orin NX</td>
    </tr>
    <tr>
      <td> Sophgo BM1684X </td>
      <td style="text-align: center;">kernel 5.4</td>
      <td>Ubuntu 20.04</td>
    </tr>
  </tbody>
</table>