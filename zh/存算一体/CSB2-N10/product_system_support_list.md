# 服务器系统支持列表

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