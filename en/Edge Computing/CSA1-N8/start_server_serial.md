# Server Serial Port

The serial port is the most direct and lowest-level way to access the server. When the network is unreachable, the display has no output, and all remote means fail, the serial port is your most reliable fallback: through it you can view boot logs and log in to the command line to troubleshoot problems that cannot be diagnosed remotely.

## Prepare the Serial Cable

The server debug serial port is an RJ45 connector. Use a USB-to-RJ45 serial cable (console cable) with a built-in **FT232 chip**. Recommended cable: [Taobao purchase link](https://detail.tmall.com/item.htm?id=704142723329&skuId=5200647198949).

<Callout type="info" title="Cable Length">
A 1 m cable is recommended. The longer the serial cable, the more the signal attenuates; an overly long cable may cause communication problems.
</Callout>

## Connect the Serial Terminal

1. Plug the USB end of the serial cable into the PC, and the RJ45 end into the server debug serial port.
2. Confirm that the PC has detected the serial device:
    - Windows: Open "Device Manager → Ports (COM & LPT)" and check the newly added COM port;
    - Linux: Run `ls /dev/ttyUSB*` to find the newly added serial device (usually `/dev/ttyUSB0`).
3. Open a serial terminal tool (such as PuTTY, MobaXterm, or minicom) and connect to the serial port with **115200-8-N-1** (baud rate 115200, 8 data bits, no parity bit, 1 stop bit).
4. Press **Enter** to wake up the terminal. When a command-line prompt appears, the connection is successful; you can then view boot logs or log in to the command-line system.

> If the terminal output is garbled, first check whether the baud rate is 115200. For problems that still cannot be located after logging in through the serial port, refer to [Troubleshooting](op_issues_troubleshooting.md).