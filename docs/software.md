# Software Configuration

## Default Installed Jetson Software

From the factory, the autopilot system of each Donington system is provisioned with ArduRover(Boat) (latest stable release), and the Jetson is flashed with Jetson L4T 35.4.1 (or later). Minimal open-source [software](https://github.com/EchoMAV/echopilot_deploy) is installed on the Jetson module as described below.

- Cockpit

    [Cockpit](https://cockpit-project.org/) provides a web-based user interface which allows the user remote access into the system using any web browser. The access the system, from a host computer on the same network, browse to `https://IP_ADDRESS` where IP_ADDRESS is the IP Address of the Donington system (see system label). Once logged into the web UI, there are many features including service monitoring, system settings and a web-based terminal.

- Mavlink-router

    [Mavlink-router](https://github.com/EchoMAV/mavlink-router) is an open source tool used to receive telemetry from the autopilot via a serial port and stream it to an IP endpoint (10.223.1.10:14550 over UDP by default). This software is open source and you are free to review the [installation scripts](https://github.com/EchoMAV/echopilot_deploy) or reinstall/remove the software. 

    Default telemetry will stream to `10.223.1.10:14550` using UDP (client mode). This will allow automatic connection to common Ground Control Stations including QGroundControl and Mission Planner. For this to work, your host computer must be set to `10.223.1.10` and the EchoPilot AI must have a [network connection](userguide.md#ip-configuration) between one of the Ethernet ports and the host computer. Once you get this basic telemetry set up working, then we suggest moving to your final desired telemetry configuration.

    The Cockpit web application has been set up to allow basic configuration changes to Mavlink-router including connection mode, endpoint IP and Port and the input serial port connected to the autopilot system. While we believe this will be sufficient for the majority of applications, Mavlink-router can be configured with much more complicated scenarios, in which case we recommend NOT using the webUI and rather editing `etc\mavlink-router\main.conf` directly. 



## Default Autopilot Software and Configuration

By default, the Donington system will come from the factory with __ArduRover(Boat)__ installed and bench tested. The following important ArduPilot parameters are set to ensure connectivity to the Jetson and proper use of the Septentrio GNSS systems:

```
SERIAL2_PROTOCOL: MAVLink2
SERIAL2_BAUD: 500   ## 500,000 kbps

GPS_TYPE: 10  # SPF for GPS1
GPS_TYPE2: 10  # SPF for GPS2

SERIAL1_PROTOCOL: GPS   # Mosaic H is on SERIAL1
SERIAL1_BAUD: 115

SERIAL3_PROTOCOL: GPS  # Mosaic X5 is on SERIAL3
SERIAL3_BAUD: 115

```


The hardware is compatible with other variants of ArduPilot (e.g. Plane, Sub, etc.) as well as the PX4 autopilot project. Instructions for how to flash other versions of firmware can be found at the links below:

- [Flashing ArduPilot](https://echomav.github.io/docs/latest/build_ardupilot/)
- [Flashing PX4](https://echomav.github.io/docs/latest/build_px4/)

If you flash PX4, the following parameters would need to be set to ensure connectivity to the Jetson:

```
MAV_1_CONFIG 102: Telem 2  ## Reboot after this change to expose additional parameters
MAV_1_RATE: 0
MAV_1_MODE 2: Onboard
SER_TEL2_BAUD: 500000   ## Reboot after this change

```

## Telemetry data between the Autopilot and the Jetson

The autopilot has a high-speed serial interface between the FMU/STM32H7 and the Jetson SOM. The Jetson UART1 (pins 203, 205) is connected to the autopilot's USART3 (SERIAL2).

On the Jetson side, UART1 is typically enumerated as ```/dev/ttyTHS0```, although it could vary with different Jetson modules including ```/dev/ttyTHS1``` and ```/dev/ttyTHS2```.

For using the EchoPilot AI to route MAVLink data over a network, we pre-install and recommend [MAVLink Router](https://github.com/mavlink-router/mavlink-router). By default, mavlink-router is configured to push telemetry via UDP to `10.223.1.10:14550`

Should you need or want to install this independently, EchoMAV has an open-source installer which makes it easy to install MAVLink Router and configure it as a service which starts at boot. Please refer to the our installer repo [https://github.com/EchoMAV/mavlink-router](https://github.com/EchoMAV/mavlink-router) for instructions. 

If you use the install repo above, please refer to the instructions there for configuration of the UART and destination IP address. Specifically edit `etc\mavlink-router\main.conf` with the appropriate settings.

!!! WARNING

    If you manually configure mavlink-router by modifying `etc\mavlink-router\main.conf`, the default web user interface may show incorrect data related to telemetry settings, or may overwrite your desired settings if saved from the web-UI. If you are working with this file, you may choose to edit the cockpit files which control telemetry settings to match your needs, or remove this feature from the web UI completely. Please see associated files [https://github.com/EchoMAV/echopilot_deploy/tree/master/ui/general](https://github.com/EchoMAV/echopilot_deploy/tree/master/ui/general).

If you have permission issues accessing `/dev/ttyTHSX` on the Jetson, please disable `nvgetty` and ensure you are a member of the `dialout` group as shown below.
```
sudo systemctl stop nvgetty
sudo systemctl disable nvgetty
sudo usermod -aG dialout $USER
```
!!! note
    Reboot to apply changes.

### Access the telemetry stream with your own application

If you wish to develop your own application to parse the vehicle's telemetry, you can disable mavlink-router using:
```
sudo systemctl stop mavlink-router
sudo systemctl disable mavlink-router
```
At this point, you can install your own application, which opens ```/dev/ttyTHS0``` (on most Jetson systems) at 500,000 kbps and uses a [mavlink parser library](https://mavlink.io/en/getting_started/use_libraries.html) to parse the byte stream.


## Aditional information related to Septentrio GNSS

The Septentrio X5 and H units must be configured to output an SBF stream on COM1 before they will work with ArduPilot. The instructions to do so are below:

1. Apply power to the Donington system, open the lid, and connect to the USB configuration port for the GNSS port you wish to configure.

![Inside Box](assets/inside_box.png)

2. Many USB devices will enumerate, including one which should be a RNDIS network interface.

!!! note
    On Windows, you will need to install the driver before the RNDIS network device will function. The installer is located in the `driver` will be available in the mass storage device which will enumerate in the step above.

3. Open a web browser at 192.168.3.1 to access the Septentrio configuration UI.
4. Go to the NMEA/SBF Out tab
5. Select +New SBF Stream > Serial Port > COM1
6. Select Detailed Selection and ensure the following messages are selected: PVTGeodetic, VelCovGeodetic, DOP and ReceiverStatus
7. Finish > OK
8. A popup menu should appear at the bottom right, click Save to ensure these settings are applied for future boots.

To ensure the settings were applied, we recommend power cycling, then reconnecting to the GNSS unit, navigate to NMEA/SBF Out tab and ensure the output you set up in the previous step has persisted across a power cycle.

Repeat the settings for COM2, configuring the stream type desired for software which may be running on the Jetson (e.g. NMEA GLL and GSA messages are the most common).

Please find information [here](https://customersupport.septentrio.com/s/article/How-to-integrate-latest-Septentrio-GNSS-receivers-with-Ardupilot-using-Pixhawk-standard-boards) for additional info about configuring Septentrio devices with ArduPilot.

## Unique Board Identifier

Each EchoPilot AI includes an AT24CS01-STUM unique ID EEPROM attached to the Jetson I2C 1 port at address 0x58. This can be used to obtain a unique 128-bit identifier (serial number) for your board. 

Below is an example python script you can use to read this serial number.

First install python3 wih smbus
```
sudo apt-get install python3-smbus
```
Create a new file ```serial.py``` with these contents:
```
import smbus
import sys

# usage, pass the i2c bus as the first argument, e.g. python3 serial_number 0

i2c_ch = int(sys.argv[1]) 

# address on the I2C bus
i2c_address = 0x58

# Register address
serial_num = 0x80

# Read serial number register
def read_serial():

    # Read the serial register, a 16 byte block
    val = bus.read_i2c_block_data(i2c_address, serial_num, 16)    
    return val

# Initialize I2C (SMBus)
bus = smbus.SMBus(i2c_ch)

try:
    # Print out the serial number
    print(bytes(read_serial()).hex())

except:
    pass
```
You can then run the script using below, where the argument is the system's i2c bus. This may vary from different Jetson modules, but will most often by 0 or 1.
```
sudo python3 serial.py 0
```






