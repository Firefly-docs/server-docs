# Power Operations




<CodeBlockTabs defaultValue="SafeOpwerOn">
    <CodeBlockTabsList>
        <CodeBlockTabsTrigger value="SafeOpwerOn">Safe Power-On</CodeBlockTabsTrigger>
        <CodeBlockTabsTrigger value="SafeOpwerOff">Safe Power-Off</CodeBlockTabsTrigger>
    </CodeBlockTabsList>

    <CodeBlockTab value="SafeOpwerOn">
      ## Safe Power-On of the Server

      ### Power-On Procedure

      1. Before powering on, confirm that the server is completely powered off.
      2. Plug one end of the IEC C13 power cord into the server power inlet.
      3. Connect the other end of the power cord to a PDU (rack power distribution unit) or a qualified power strip.
      4. Close the leakage protection switch to supply power to the PDU / power strip.
      5. If the server has just been powered off, wait at least 30 seconds before powering it on again.

      <Callout type="warn" title="Safety Warning">
        **It is strictly forbidden to plug or unplug the power cord into the server power inlet while it is live**; hot plugging can easily generate arcs, which can break down and damage the MOS power transistors inside the power supply and shorten its service life; in extreme cases, it can directly cause the server power supply to fail and the server to be unable to start.

        1. The server CRPS redundant power supply integrates large-capacity input filter electrolytic capacitors and is equipped with a self-recovering fuse (PPTC) protection circuit to protect the front-end power devices. If the power cord is plugged in while the device side is live, the large no-load capacitors inside the power supply are forcibly charged at the moment of power-on, generating an instantaneous inrush current that triggers the self-recovering fuse protection on the power input side.
        2. This protection mechanism can preferentially block abnormal current and prevent damage to core precision components such as MOS power transistors, rectifier bridges, and EMI filter circuits; it is a fault-tolerant protection design built into the power supply. Violating the rule by hot-plugging only triggers the fuse open-circuit protection and does not directly break down or burn the power supply itself; it only prevents the device from powering on normally.
        3. After the protection is triggered, no components need to be replaced. Simply disconnect the power completely, turn off the PDU breaker switch, and let it cool for about **5 minutes**. After the self-recovering fuse automatically resets and the circuit returns to normal, the server can be safely powered on again following the standard procedure. In addition, residual voltage remains in the capacitors after the device is powered off, and repeated hot plugging within a short time can easily trigger the protection repeatedly. Therefore, wait more than 30 seconds to ensure the capacitors are fully discharged and avoid repeated protection triggering.
      </Callout>
      ### Power-On Methods

      The server supports the following power-on methods:

      **Method 1: Power on via the Power button (the power button/indicator glows steady green)**

      1. Briefly press the power button on the front panel to power on the server. For the location of the power button, refer to the front panel indicators and buttons.

      **Method 2: Power on via the aBMC WebUI**

      1. Click the power button in the upper right corner of the aBMC WebUI home page to enter the "Server Power On/Off" page.
      2. Click "Power On", and when the power-on prompt appears, click "OK" to power on the server.

      > By default, the system's "power-on policy after power is applied" is "Keep Power-On", meaning the system automatically powers on after the server's power module is energized. Users can modify the "power-on policy" through aBMC.

      **Method 3: Power on the server via the remote virtual console**

      1. Remotely log in to aBMC through a browser.
      ![aBMC dashboard View](../../../servers_img/common/abmc_dashboard_view.png)
      2. Set the power button in the upper right corner of the aBMC WebUI home page to "ON"; the server completes full power-on.
      ![aBMC Web Power Button View](../../../servers_img/common/abmc_power_button_view.png)
    </CodeBlockTab>

    <CodeBlockTab value="SafeOpwerOff">
      ## Safe Power-Off of the Server
      <div>
        <ul style="padding-left: 24px; margin: 10px 0;">
          <li>After power-off, all services and programs will be terminated. Therefore, before powering off, be sure to confirm that all services and programs on the server have been stopped or migrated to other devices.</li>
          <li>In this chapter, "power-off" refers to powering the server down to the Standby state (the power button indicator glows steady yellow).</li>
          <li>After a forced power-off of the server, wait more than 10 seconds to ensure the server is completely powered off; only then can it be powered on again.</li>
        </ul>
      </div>

      The server supports the following power-off methods:
      - When the server is powered on, briefly press the power button on the front panel to power it off normally.
      - Power off the server via the remote virtual console.
    </CodeBlockTab>
</CodeBlockTabs>