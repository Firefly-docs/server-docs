# Power Operations

<CodeBlockTabs defaultValue="SafeOpwerOn">
    <CodeBlockTabsList>
        <CodeBlockTabsTrigger value="SafeOpwerOn">Safe Power-On</CodeBlockTabsTrigger>
        <CodeBlockTabsTrigger value="SafeOpwerOff">Safe Power-Off</CodeBlockTabsTrigger>
    </CodeBlockTabsList>

    <CodeBlockTab value="SafeOpwerOn">
      ## Safe Power-On of the Server [step]

      ### Power-On Procedure

      1. Before powering on, confirm that the server is completely powered off.
      2. Plug one end of the IEC C13 power cord into the server power inlet.
      3. Connect the other end of the power cord to a PDU (rack power distribution unit) or a qualified power strip.
      4. Close the leakage protection switch to supply power to the PDU / power strip.
      5. The server powers on automatically once power is supplied; no power button is required.
      6. If the server has just been powered off, wait at least 30 seconds before powering it on again.

      <Callout type="warn" title="Safety Warning">
        **It is strictly forbidden to plug or unplug the power cord into the server power inlet while it is live**; hot plugging can easily generate arcs, which can break down and damage the MOS power transistors inside the power supply and shorten its service life; in extreme cases, it can directly cause the server power supply to fail and the server to be unable to start.

        1. The server power supply integrates large-capacity input filter electrolytic capacitors and is equipped with a self-recovering fuse (PPTC) protection circuit to protect the front-end power devices. If the power cord is plugged in while the device side is live, the large no-load capacitors inside the power supply are forcibly charged at the moment of power-on, generating an instantaneous inrush current that triggers the self-recovering fuse protection on the power input side.
        2. This protection mechanism can preferentially block abnormal current and prevent damage to core precision components such as MOS power transistors, rectifier bridges, and EMI filter circuits; it is a fault-tolerant protection design built into the power supply. Violating the rule by hot-plugging only triggers the fuse open-circuit protection and does not directly break down or burn the power supply itself; it only prevents the device from powering on normally.
        3. **If protection is triggered,** no components need to be replaced. Simply disconnect the power completely, turn off the PDU breaker switch, and let it cool for about **5 minutes**. After the self-recovering fuse automatically resets and the circuit returns to normal, the server can be safely powered on again following the standard procedure. In addition, residual voltage remains in the capacitors after the device is powered off, and repeated hot plugging within a short time can easily trigger the protection repeatedly. Therefore, wait more than 30 seconds to ensure the capacitors are fully discharged and avoid repeated protection triggering.

        The power module does not support hot swapping. Disconnect external power before replacing a power module.
      </Callout>
      ### Power-On Method

      This model uses a "power-on when powered" design: once external power is connected, the whole server powers on automatically, and all sub-nodes power on together with the chassis. No power button or per-board operation is required.
    </CodeBlockTab>

    <CodeBlockTab value="SafeOpwerOff">
      ## Safe Power-Off of the Server [step]
      <div>
        <ul style="padding-left: 24px; margin: 10px 0;">
          <li>After power-off, all services and programs will be terminated. Therefore, before powering off, be sure to confirm that all services and programs on the server have been stopped or migrated to other devices.</li>
          <li>This model does not provide a front-panel power button or a whole-chassis software power-off operation. To power off, disconnect external power through the PDU / circuit breaker.</li>
          <li>After the server is powered off, wait at least 30 seconds to ensure it is completely powered off before powering it on again.</li>
        </ul>
      </div>

      Power-off procedure:
      - Stop all services and programs on the server first.
      - Turn off the PDU breaker switch or disconnect external power; the whole server is then powered off.
    </CodeBlockTab>
</CodeBlockTabs>