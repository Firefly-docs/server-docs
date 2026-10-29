# Sub-Board Serial Collection

## Introduction

Sub-Board Serial Collection (Sub Serial Collection) is a serial port output collection feature that aBMC provides for array servers. With this feature, the serial port output of sub-boards can be continuously collected and recorded while aBMC is running. During collection, the serial port output of sub-boards is aggregated in real time into human-readable log text, written uniformly to the system log service (journald), and attributed by sub-board identifier fields. O&M personnel can retrieve and trace back the serial port history by sub-board, providing a trusted timeline basis for fault locating and downtime review.

<Callout title="Version Requirement" type="info">
    aBMC version >= v3.1.0
</Callout>

## Development Vision

1. Enable users to locate faults through the historical serial port output.
2. Provide convenient, fine-grained operation logic and API interfaces, allowing users to freely choose whether to enable the serial collection feature for a specific sub-board.

# Feature Usage

## Viewing and Configuring Serial Collection

<CodeBlockTabs defaultValue="WEB">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="WEB">WEB</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="API">API</CodeBlockTabsTrigger>
  </CodeBlockTabsList>

  <CodeBlockTab value="WEB">
    ### View the serial collection configuration [step]

    1. Select **Devices** in the left main navigation bar.
    2. Select **General** in the secondary navigation bar to enter the **Device Overview** page.
    3. The **Serial** column shows the serial collection configuration of the sub-board.

    The first value is the auto-start status of serial collection: `enabled` indicates that aBMC attempts to enable the serial collection feature when the aBMC service starts, while `disabled` indicates that it does not. The second value is the running status of serial collection: `running` indicates that the serial collection service is running, while `exited` indicates that the serial collection service has stopped.

    ![Show Serial Collection configuration](../../../aBMC_img/v3.0/subSerialCollection_img/01-show-serial-collection-en-v1.png)

    ### Configure serial collection [step]

    On the page of the previous step:

    1. Locate the target sub-board in the device list and click the **⚙️** icon.
    2. Select **Serial Collection Setting**.

    ![Open Serial Collection configuration](../../../aBMC_img/v3.0/subSerialCollection_img/02-open-serial-collection-en-v1.png)

    | Field | Description |
    | --- | --- |
    | Auto Start | Controls the auto-start status of serial collection |
    | Running Status | Controls the running status of serial collection |

    ![Set Serial Collection configuration](../../../aBMC_img/v3.0/subSerialCollection_img/03-set-serial-collection-en-v1.png)

    <Callout title="Note" type="info">
        Auto Start controls whether aBMC attempts to enable the serial collection feature on the next aBMC startup, while Running Status controls enabling and disabling of serial collection during the current aBMC runtime.
    </Callout>

    ### View the historical serial output [step]

    After serial collection is enabled, you can view the historical serial output. On the page of the first step:

    1. Locate the target sub-board in the device list and click the **Terminal** icon.
    2. Select **SerialHistoryLog** in **Debug Mode**.
    3. Click **Confirm** to enter the console page.

    ![Show Serial Collection](../../../aBMC_img/v3.0/subSerialCollection_img/04-show-serial-log-en-v1.png)
    ![Show Serial Collection](../../../aBMC_img/v3.0/subSerialCollection_img/05-show-serial-log-en-v1.png)

    <Callout title="Note" type="info">
        The historical serial output page supports the following common keys:

        | Key | Function |
        | --- | --- |
        | ↑ / ↓ | Scroll up / down line by line |
        | PgUp / PgDn | Page up / down |
        | g / G | Jump to the beginning / end of the log (G jumps directly to the latest record) |
        | `/keyword` + Enter | Search the log; press n / N to jump to the next / previous match |
    </Callout>
  </CodeBlockTab>

  <CodeBlockTab value="API">
    | Operation | Method | URI |
    | --- | --- | --- |
    | Get the serial collection status | GET | `/redfish/v1/Managers/{ManagerId}/Oem/Firefly/SerialCollect` |
    | Configure serial collection | POST | `/redfish/v1/Managers/{ManagerId}/Oem/Firefly/SerialCollect/Actions/Configure` |
    | View the historical serial output | GET (Websocket) | `/redfish/v1/Managers/{ManagerId}/Oem/Firefly/Terminal/Webtty` |

    <Callout title="Note" type="info">
        For detailed information about interface authentication, request parameters, response fields, and error codes, refer to the Redfish API documentation.
    </Callout>
  </CodeBlockTab>
</CodeBlockTabs>
