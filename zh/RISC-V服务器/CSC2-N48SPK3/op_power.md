# 电源操作




<CodeBlockTabs defaultValue="SafeOpwerOn">
    <CodeBlockTabsList>
        <CodeBlockTabsTrigger value="SafeOpwerOn">安全上电</CodeBlockTabsTrigger>
        <CodeBlockTabsTrigger value="SafeOpwerOff">安全下电</CodeBlockTabsTrigger>
    </CodeBlockTabsList>

    <CodeBlockTab value="SafeOpwerOn">
      ## 服务器安全上电

      ### 上电操作步骤

      1. 开机前确认服务器整机完全下电。
      2. 将品字电源线一端插接至服务器电源接口。
      3. 电源线另一端接入 PDU 机柜配电单元或合格供电插排。
      4. 闭合漏电保护开关，给 PDU / 供电插排送电。
      5. 若服务器刚完成断电操作，需静置等待不少于 30 秒后方可重新上电。

      <Callout type="warn" title="安全警示">
        **严禁带电插拔电源线缆至服务器电源接口**；带电插接易产生电弧，极易击穿、损伤电源内部 MOS 功率管，缩短电源使用寿命；极端情况下会直接造成服务器电源失效、无法启动。

        1. 服务器CRPS冗余电源内部集成大容量输入滤波电解电容，同时搭载自恢复保险丝（PPTC）保护电路，用于防护前端功率器件。若在设备侧带电插接电源线，上电瞬间对电源内部空载大电容强制充电，产生瞬时浪涌电流，触发电源输入侧自恢复保险丝保护。
        2. 该保护机制可优先阻断异常电流，规避MOS功率管、整流桥、EMI滤波电路等核心精密器件损坏，属于电源自带容错防护设计。违规带电接入仅会触发保险丝断路保护，不会直接击穿、烧毁电源本体，仅会导致设备无法正常上电启动。
        3. 触发保护后无需更换器件，只需完全断开电源、关闭PDU合闸开关，静置冷却约**5分钟**，待自恢复保险丝自动复位、电路恢复正常后，即可按照标准流程重新安全上电。同时，设备断电后电容仍存有残余电压，短时间内重复带电插拔易反复触发保护，因此需静置30秒以上，确保电容充分放电，规避重复保护触发问题。
      </Callout>
      ### 上电方式

      服务器有以下几种上电方式：

      **方式一：通过 Power 按键开机（电源按钮/指示灯为绿色常亮）**

      1. 通过短按前面板的电源按钮，将服务器上电。电源按钮位置请参见前面板指示灯和按钮。

      **方式二：通过 aBMC WebUI 上电**

      1. 通过 aBMC WebUI 首页右上角的电源按键开机进入“服务器上下电”界面。
      2. 单击“上电”，出现上电提示时单击“确定”将服务器上电。

      > 系统默认“通电开机策略”为“保持上电”，即服务器的电源模块通电后系统自动开机。用户可通过 aBMC 修改“通电开机策略”。

      **方式三：通过远程虚拟控制台将服务器上电**

      1. 通过浏览器远程登录 aBMC。
      ![aBMC dashboard View](../../../servers_img/common/abmc_dashboard_view.png)
      2. 通过aBMC WebUI首页右上角的电源按钮设置为“ON”，服务器完成整机上电。
      ![aBMC Web Power Button View](../../../servers_img/common/abmc_power_button_view.png)
    </CodeBlockTab>

    <CodeBlockTab value="SafeOpwerOff">
      ## 服务器安全下电
      <div>
        <ul style="padding-left: 24px; margin: 10px 0;">
          <li>下电后，所有业务和程序将终止，因此下电前请务必确认服务器所有业务和程序已经停止或者转移到其他设备上。</li>
          <li>本章节的"下电"指将服务器下电至 Standby 状态（电源按钮指示灯为黄色常亮）。</li>
          <li>服务器强制下电后，需要等待 10 秒以上，以确保服务器完全下电，此时可进行再次上电操作。</li>
        </ul>
      </div>

      服务器有以下几种下电方式：
      - 服务器处于上电状态，通过短按前面板的电源按钮，可将服务器正常下电。
      - 通过远程虚拟控制台将服务器上电。
    </CodeBlockTab>
</CodeBlockTabs>


