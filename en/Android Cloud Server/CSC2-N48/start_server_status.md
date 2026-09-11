# Server Status Identification

After the server is connected to the power outlet, the BMC is powered on automatically.

Before using the server, check the power button/indicator on the server to determine its current operating status.

## Power Button/Indicator

![Power button/indicator](../../../servers_img/CSC2-N48/front_view_led_power.png)

The front panel has a power button used to power on and off all sub-boards. The button indicator shows the power status of the sub-boards through its color and blinking state:

- **Off**: The server is not powered on.
- **Blinking yellow**: The power-on stage after a cold start, when the power is not yet ready. After the power is ready, the indicator turns steady green, and all sub-boards are powered on by default.
- **Steady green**: The sub-boards are powered on. Press the button again, and the indicator turns to alternating yellow/green blinking while the sub-boards are shutting down; after the shutdown is completed, the indicator turns steady yellow.
- **Steady yellow**: All sub-boards are powered off. Press the button again, and the indicator turns steady green, and the sub-boards will be powered on one by one.