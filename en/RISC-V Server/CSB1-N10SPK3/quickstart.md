# Quick Start

New to CSB1-N10SPK3? Jump directly to a task, or switch to "First Time Setup" for the 9-step flow. This page is only an entry point; details are on the linked pages.

<Callout title="Read before power-on: safety" type="warn">
  Never plug or unplug power cables while the device is live; wait at least 30 seconds after power-off before powering on again. Power-on behavior varies by model; read the matching [Power Operations](start-server-power) page first.
</Callout>

<CodeBlockTabs defaultValue="tasks">
    <CodeBlockTabsList>
        <CodeBlockTabsTrigger value="tasks">Find by Task</CodeBlockTabsTrigger>
        <CodeBlockTabsTrigger value="first">First Time Setup</CodeBlockTabsTrigger>
    </CodeBlockTabsList>

    <CodeBlockTab value="tasks">
      <Cards>
        <Card title="Installation and Power-On" href="start-server-installation">
          Rack installation, power safety, and status checks.
        </Card>
        <Card title="Network and Login" href="start-server-network">
          BMC IP, sub-board network, and Web / SSH / serial login.
        </Card>
        <Card title="aBMC Features" href="aBMC">
          Users, alerts, storage, power policy, and other management features.
        </Card>
        <Card title="Application Cases" href="application-cases">
          Single NIC with multiple subnets, network isolation, and network sharing.
        </Card>
        <Card title="Firmware and Customization" href="bmc-firmware-upgrade">
          BMC upgrade, custom firmware, and recovery; see "Get the SDK and System Support" for the SDK and system support list.
        </Card>
        <Card title="Maintenance and Troubleshooting" href="op-issues-troubleshooting">
          Log collection, troubleshooting, and sub-board disconnection handling.
        </Card>
      </Cards>
    </CodeBlockTab>

    <CodeBlockTab value="first">
      | **Prepare** | **Connect** | **Operate** | **Deliver** |
      | :---: | :---: | :---: | :---: |
      | 1–4 | 5–6 | 7–8 | 9 |

      ### Prepare · Confirm Specifications and Support [step]

      Confirm the node count, interfaces, power consumption, and the operating systems and sub-board models supported by this model.

      - [Specifications and Components](product-specifications)
      - [System Support List](sdk-application#system-support-list)

      ### Unbox and Check Accessories [step]

      Check power cords, rails / brackets, SFP modules, and serial cables. The actual accessory list depends on the model.

      ### Power-On Safety [step]

      Confirm the power supply, grounding, and power-on sequence. Never plug or unplug power cables while the device is live; wait at least 30 seconds after power-off before powering on again.

      - [Power Operations](start-server-power)

      ### Power On, Boot, and Check Status [step]

      Connect power and check the node / system indicators and BMC indicators to confirm that the device is manageable.

      - [Status Identification](start-server-status)

      ### Connect · Network Wiring and BMC Access [step]

      Connect the management network according to the site plan, then access the BMC through its default IP or DHCP address and complete the first login.

      - [Network Wiring](start-server-network)
      - [Access the BMC](start-login-bmc)

      ### Configure aBMC Network and Sub-Boards [step]

      After logging in, change the BMC IP / VLAN, confirm that the management network matches the site plan, and then log in to the sub-boards.

      - [aBMC Network Configuration](/docs/server/bmc-software/aBMC/bmcNetwork)
      - [Log In to Sub-Boards](start-login-sub)
      - [Serial Console](start-server-serial)

      ### Operate · aBMC Features and Solutions [step]

      Configure users, alerts, storage, power policy, and other common features, and choose a network solution based on the site requirements.

      - [aBMC Web User Manual](/docs/server/bmc-software/aBMC/preface)
      - [Application Cases](application-cases)

      ### SDK, Firmware, and Customization [step]

      Check the system support list and get the SDK on the "Get the SDK and System Support" page. Upgrade the BMC or customize the firmware as needed.

      - [Get the SDK and System Support](sdk-application)
      - [Custom Firmware](dev-sub-firmware)
      - [BMC Upgrade](bmc-firmware-upgrade)

      ### Deliver · On-Site Deployment and Rack Installation [step]

      Complete rack installation, cabling, and power-on. Customers deploy their own business software and define their own acceptance criteria.

      - [Server Installation](start-server-installation)
      - [Power Operations](start-server-power)
    </CodeBlockTab>
</CodeBlockTabs>

## Downloads and Support

<Cards>
  <Card title="Downloads and Support" href="/docs/server/bmc-software/aBMC/install">
    Get aBMC packages, version directories, and installation commands.
  </Card>
  <Card title="Get the SDK and System Support" href="sdk-application#system-support-list">
    Check the system support list and get the SDK.
  </Card>
  <Card title="Release Notes" href="release-notes">
    See the latest aBMC and Wiki changes.
  </Card>
</Cards>