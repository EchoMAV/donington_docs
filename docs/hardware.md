The Donington system uses the EchoMAV [EchoPilot AI](https://echomav.com/product/echopilot-ai/) combined with a customized carrier board.

To fill in gaps not covered in this documentation, please cross reference the [EchoPilot AI Documentation](https://echomav.github.io/docs/latest/echopilot_ai/). 

The flow chart below shows the overall hardware architecture. The custom carrier board described below mates to the EchoPilotAI's board to board connectors, and provided Donington-specific functionality. Specifically, the Donington carrier board provides two Septentrio GNSS units, a RS-422 level shifter for an external INS system, two FTDI serial converters, power subsystems and connectors not found on EchoMAV's standard commercial carrier board. The system also includes a ruggedize aluminum enclosure, passive heat dissipation, IP67-design, and industrial M12 connectors.

![Flow Chart](assets/flow_chart.png)

![Inside Box](assets/inside_box.png)

## Carrier Board Schematic

The Donington Carrier Board Schematic is [available to download.](assets/schematic.pdf) 

## FMU Ports

The Autopilot system (also referred to as Flight Management Unit (FMU)) is based on the open-hardware Pixhawk design running an STM32H743 microcontroller. The peripherals for this device are connected via I2C, SPI, CAN and UART ports. The table below identifies how each peripheral interfaces is used.

Port | Use | Connector Assignement
------------ | ------------- | ------------ 
USART1 | GPS1 | Mosaic X5 GPS (serial port 1, pins B1/D1)
USART2 | GPS2 | Mosaic H (serial port 1, pins B1/D1)
USART3 | Telemetry to Jetson (Telem2) | NA (internally routed)
UART4 | External INS1 (RS-422 shifted) | External INS connector 
USART5 | Not Used | NA 
USART6 | Remote ID | NA (internal)
UART7 | External/User (Debug) | EchoPilot J12
UART8 | IO MCU | NA (internal)
SPI1 | ICM42688P IMU #1 and #2 | NA (internal)
SPI2 | RM3100 Compass and FRAM | NA (internal)
SPI3 | Not Used | NA
SPI4 | ICM42688P IMU #3 and MS5611 Baro #1 NA (internal)
SPI5 | Not Used | NA
SPI6 | MS5611 Baro #2 | NA (internal)
I2C1 | External RGB LED | 
I2C2 | Internal (Spare) | Carrier Board J13
I2C3 | Not Used | NA
I2C4 | Not Used | NA

## FMU UART Order

The default UART order for use for autopilot firmware is provided below. The port name is important, as you will use this name within ArduPilot to set up parameters associated with each port. e.g. `SERIAL1_PROTOCOL`.

Port Name | Function | Port | Connector
------------ | ------------- | ------------ | ------------
SERIAL0 | Console | USB | EchoPilot J7
SERIAL1 | GPS1 | USART2 | None (to Mosaic H)
SERIAL2 | Telem2 | USART3 | None (internally routed to Jetson)
SERIAL3 | GPS2 | USART1 | None (to Mosaic X5)
SERIAL4 | External INS (RS-232 shifted) | UART4 | Carrier Board J32
SERIAL5 | Onboard Remote ID | USART6 | NA
SERIAL6 | Debug | UART7 | EchoPilot J12

Please reference the [EchoPilot AI's BSP](https://github.com/EchoMAV/echopilot_ai_bsp) firmware-specific board definition files for additional details related to board setup.

## CAN

2 CAN ports from the autopilot (STM32H743) are exposed, one on the CAN connector, another on an internal (spare) connector (J14) on the carrier board. Please refer to [ArduPilot CAN Bus Setup](https://ardupilot.org/rover/docs/common-canbus-setup-advanced.html) for information about DroneCAN setup.

1 CAN port from the Jetson is routed to the external NMEA2K connector. 

!!! note
    To enable CAN on the Jetson, modify `/etc/modprobe.d/denylist-mttcan.conf` and ensure the line `blacklist mttcan` is commented out. Reboot, then log in again and run `sudo modprobe mttcan`.

### Termination

The two (2) CAN connections from the FMU (FMU CAN1 and FUM CAN2) and the one (1) from the Jetson are driven by LTC2875 transceivers and contain termination resistors at the drivers on the EchoPilot AI board inside the Donington enclosure. Should you desire to remove these termination resistors (e.g., you want to place the system in the middle of a CAN chain rather than at the end), refer to the following resistor locations:  

CAN   | Resistor Label     | Notes      
------------ | ------------- | ------------ 
FMU CAN1       | R19         |  Near U4 and U45, size 0402
FMU CAN2        | R9         |  Near U3, size 0402
JETSON CAN1 | R95         |  Near U32, size 0402  

## Septentrio GNSS Units

The Donington system contains two Septentrio GNSS systems (X-5 and H). The table below summarizes the connections available to each.

GNSS   | Port    | Connection      
------------ | ------------- | ------------ 
Mosaic X5       | Serial 1 (B1/D1)         |  Autopilot SERIAL3 (115 kpbs)
Mosaic X5       | Serial 2 (F1/H1)        |  Jetson via USB FTDI (e.g. /dev/ttyUSBx)
Mosaic X5   |   USB | USB-C port inside enclosure
Mosaic H       | Serial 1 (B1/D1)         |  Autopilot SERIAL1 (115 kpbs)
Mosaic H       | Serial 2 (F1/H1)        |  Jetson via USB FTDI (e.g. /dev/ttyUSBx)
Mosaic H   |   USB | USB-C port inside enclosure 

Please refer to the [schematic for more information](assets/schematic.pdf).