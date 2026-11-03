# Quick Start

New to CSB1-N10NOrinNX? Jump directly to a task, or switch to "First Time Setup" for the 9-step flow. This page is only an entry point; details are on the linked pages.

<Callout title="Read before power-on: safety" type="warn">
  Never plug or unplug power cables while the device is live; wait at least 30 seconds after power-off before powering on again. Power-on behavior varies by model; read the matching [Power Operations](start_server_power) page first.
</Callout>

<CodeBlockTabs defaultValue="tasks">
    <CodeBlockTabsList>
        <CodeBlockTabsTrigger value="tasks">Find by Task</CodeBlockTabsTrigger>
        <CodeBlockTabsTrigger value="first">First Time Setup</CodeBlockTabsTrigger>
    </CodeBlockTabsList>

    <CodeBlockTab value="tasks">
      <Cards>
        <Card title="Installation and Power-On" href="start_server_installation">
          Rack installation, power safety, and status checks.
        </Card>
        <Card title="Network and Login" href="start_server_network">
          BMC IP, sub-board network, and Web / SSH / serial login.
        </Card>
        <Card title="aBMC Features" href="aBMC">
          Users, alerts, storage, power policy, and other management features.
        </Card>
        <Card title="Application Cases" href="ap_vlan_Isolation">
          Single NIC with multiple subnets, network isolation, and network sharing.
        </Card>
        <Card title="Firmware and Customization" href="bmc_firmware_upgrade">
          BMC upgrade, support list, custom firmware, and recovery.
        </Card>
        <Card title="Maintenance and Troubleshooting" href="op_issues_troubleshooting">
          Log collection, troubleshooting, and sub-board disconnection handling.
        </Card>
      </Cards>
    </CodeBlockTab>

    <CodeBlockTab value="first">
      **1. Confirm Specifications and Support**

      - [Specifications and Components](product_specifications)
      - [System Support List](product_system_support_list)

      **2. Unboxing and Accessory Check**

      Check power cords, rails / brackets, SFP modules, and serial cables. The actual accessory list depends on the model.

      **3. Power-On Safety**

      - [Power Operations](start_server_power)
      - Never plug or unplug power cables while the device is live; wait at least 30 seconds after power-off before powering on again.

      **4. Power-On, Boot, and Status Check**

      - [Status Identification](start_server_status)
      - Check the node / system indicators and BMC indicators to confirm that the device is manageable.

      **5. Network Configuration**

      - [Network Wiring](start_server_network)
      - [aBMC Network Configuration](/docs/server/bmc-software/aBMC/bmcNetwork)

      **6. Log In to aBMC / Sub-Board**

      - [Access the BMC](start_login_bmc)
      - [Log In to Sub-Boards](start_login_sub)
      - [Serial Console](start_server_serial)

      **7. aBMC Features and Solutions**

      - [aBMC Web User Manual](/docs/server/bmc-software/aBMC/preface)
      - [Application Cases](ap_vlan_Isolation)

      **8. Firmware and Customization**

      - [Get the SDK](sdk_application)
      - [Custom Firmware](dev_sub_firmware)
      - [BMC Upgrade](bmc_firmware_upgrade)

      **9. On-Site Deployment and Rack Installation**

      - [Server Installation](start_server_installation)
      - [Power Operations](start_server_power)
      - [BMC Upgrade](bmc_firmware_upgrade)

      Customers deploy their own business software and define their own acceptance criteria.
    </CodeBlockTab>
</CodeBlockTabs>

## Downloads and Support

<Cards>
  <Card title="Downloads and Support" href="/docs/server/bmc-software/aBMC/install">
    Get aBMC packages, version directories, and installation commands.
  </Card>
  <Card title="Firmware Support List" href="product_system_support_list">
    Check the firmware versions supported by the current model.
  </Card>
  <Card title="Release Notes" href="wiki_release_notes">
    See the latest aBMC and Wiki changes.
  </Card>
</Cards>