# User Guide

## Accessing the Jetson console

!!! WARNING

    Do not run the Jetson SOM without a heat sink. The lid of the Donington system is the primary heat sink, and running the Jetson with the lid off may result in shut down or throttling. If you need to run the system for a continued period of time with the lid removed, position a fan to move air over the heatspreader installed on the Jetson module.

1. Open the lid of the Donington system, to expose the inner electronics. 

!!! note

    A thermal pad is positioned bewtween a heat spreader on the Jetson and the Donington system lid. Please ensure this is in position and undamaged after removing and replacing the lid.

2. Attached a USB cable between your host computer and J15 (Console) on the EchoPilot AI Board
![Console USB Connection](assets/usb-to-echopilot-carrier.png)
3. In step 2, your host computer should have enumerated a virtual comm port. You will now need to find the name of the port.
!!! info
    **On Windows:** Open Device Manager (Start → Control Panel → Hardware and Sound → Device Manager) Look in the Device Manager list, open the category "Ports", and note the COM port added **USB Serial Port (COM?)** (e.g., COM10).  
    **On Linux:** Run ```dmesg -w``` and then plug in unplug and replug in the USB cable. You should see the name of the device added, typically ```FTDI USB Serial device converter now attached to ttyUSB?``` (e.g., ttyUSB0). 
Use a terminal program to connect to the Jetson's console at 115200 baud, 8N1. 
!!! info
    **On Windows:** We recommend [Putty](https://www.putty.org/) or [TeraTerm](https://osdn.net/projects/ttssh2/releases/).  
    **On Linux:** We recommend Picocom. Install with ```sudo apt-get install picocom```. Use with ```picocom /dev/ttyUSB? -b 115200```. To exit picocom, use ```Ctrl-a Ctrl-x```.
Power the Donington System 13-36VDC source capable of supplying up to **4A**.
!!! warning
    If using a bench supply with over-current protection, we recommend turning it **OFF**. The boot process requires short bursts of high current and over-current protection on some supplies will result in a failed boot. In most cases, if the Jetson fails to boot it is due to a poor power supply.
You should now see the boot messages in your console, and once boot is complete, you will see a login prompt.
!!! note
    The default username is **echopilot** and the default password is **echopilot**
!!! success
    At this point you are logged into the Jetson.

## Accessing the Jetson via network

The Donington system has two 100Mbps Ethernet ports (ETH1 and ETH2). Upstream, these go to a network switch, so either one can be used to access the Jetson SOM. An M12 to RJ45 cable is required (e.g. [ASI-M12-RJ45-11101](https://www.digikey.com/en/products/detail/asi-ez/ASI-M12-RJ45-11101/14008395?s=N4IgTCBcDaIIIGUCSBaAsgRjCgSgKQBYBWFDMgBgwB0AXEAXQF8g))

EchoMAV's standard provisioning sets the Jetson module to a static IP address provided on the label with the device. There is also an alias ip of 192.168.253.0 which can be used if you do not know the static IP. 

To gain console access to the Jetson, use `ssh` from a terminal session on the host computer:

```
ssh echopilot@IP_ADDRESS    #IP_ADDRESS obtained from the label on the device
```
!!! note
    The default password is **echopilot**


!!! note
    If the label is damanged, or the static IP has been inadvertendly changes, you can use the configuration IP "backdoor" alias of __192.168.154.0/24__ to access the system. Ensure your host system is in the 192.168.0.0/24 subnet (any valid IP address __not equal__ to 192.168.154.0 will work). Please refer to the instructions above for how to change your host IP address.

## Configuring for DHCP

If you wish to use [DHCP](https://en.wikipedia.org/wiki/Dynamic_Host_Configuration_Protocol), follow the instructions below:

First [gain console access](#accessing-the-jetson-console) via the USB connector. Once logged in via the console, modify the existing static connection (e.g. "static-eth0") to be DHCP:
```
sudo nmcli con mod static-eth0 ipv4.method auto
sudo nmcli con mod static-eth0 ipv4.gateway ""
sudo nmcli con mod static-eth0 ipv4.address ""
sudo nmcli con down static-eth0
sudo nmcli con up static-eth0
```
If the network connection is plugged into a DHCP server, the system will now get an IP address. You can confirm with: 
```
ip addr
```

## Connecting to the FMU via the USB connector

1. Attach a USB cable between the host computer and the **FMU USB** connector (J7).
2. Start a Ground Control application on the host computer such as [QGroundControl](https://docs.qgroundcontrol.com/master/en/getting_started/download_and_install.html) or [Mission Planner](https://ardupilot.org/planner/docs/mission-planner-installation.html).
!!! info
    **QGroundControl:** Will automatically connect.  
    **Mission Planner:** Select the appropriate COM port at the top right, 115200, then click CONNECT.




## IP Configuration
## Telemetry Routing
## Obtaining Autopilot Data
## Cellular Modem Installation