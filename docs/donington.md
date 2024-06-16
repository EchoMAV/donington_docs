# Donington Documentation

## Overview

The Donington product offers andvanced capabilities for unmanned systems (PX4 and Ardupilot compatiable autopilot) combined with embedded computer power (Jetson Orin) and dual/redundant Septentrio H and X5 GNSS systems. The system is packaged in a robust aluminum case which has been designed to IP67 standards. The Donington product is based on the EchoMAV [EchoPilot AI](https://echomav.com/product/echopilot-ai/), please refer to the [EchoPilot AI Documentation](https://echomav.github.io/docs/latest/echopilot_ai/) for information specific to that device.

## Mechanical Drawings and STEP Files

![Assembly](assets/assembly.png)


## System Diagram/Flowchart

![Flow Chart](assets/flow_chart.png)

## Carrier Board Schematic

The Donigton Carrier Board Schematic is [available to download.](assets/schematic.pdf) 

## Pinouts and Connectors

The PWR LED on the left side indicates that the system is powered and running. The STATUS LED is an RGB output indicating the status of the autopilot. Please refer to the autopilot firmware documentation for the meaning of the STATUS LED, e.g. [ArduRover RGB LED documenation](https://ardupilot.org/rover/docs/common-leds-pixhawk.html#rgb-leds). Both LEDs can be deactivated by changing the position of a DIP switch located inside the case. 

![Long Side Connectors](assets/pinout_long.png)
<center>

Connector   | Pins | Type | Part Number | Mating Cable Assembly | Notes
------------ | ------------- | ------------- | ------------- | ------------- | ------------- 
PWR       | 4 | M12, A-Code Male  | T4140012041-000  | Tensility 10-03805 |  13-36VDC, 4A + supply        
PWM       | 12 | M12, A-Code Female | T4141012121-000  | Custom |          
INS       | 8 | M12, A-Code Female | T4141012081-000  | Custom |  
IRIDIUM   | 12 | M12, A-Code Female    | T4141012121-000 | Custom | 
FMU CAN   | 4 | M12, A-Code Female  | T4141012041-000 | Custom | 
NMEA2K    | 5 | M12, A-Code Female   | T4141012051-000  | Custom |   
ANALOG IN | 5 | M12, D-Code Female   | T4141512051-000     | Custom |  

</center>

![Short Side Comms](assets/pinout_short.png)

<center>

Connector   | Pins | Type | Part Number | Mating Cable Assembly | Notes
------------ | ------------- | ------------- | ------------- | ------------- | ------------- 
ETH       | 4 | M12, D-Code Male  |  T4141512041-000  | ASI-M12-RJ45-11101 |  100 Mbps Ethernet to Jetson      
ETH       | 4 | M12, D-Code Female | T4141512041-000 | ASI-M12-RJ45-11101 | 100 Mbps Ethernet to Jetson             
USB       | 12 | M12, A-Code Female | T4141012121-000  | Custom | USB 3 SS to Jetson    
USB       | 12 | M12, A-Code Female    | T4141012121-000 | Custom | USB 3 SS to Jetson

</center>


![Short Side RF](assets/pinout_short.png)

<center>

Connector   | Type | Part Number  | Notes
------------ | ------------- | ------------- | ------------- 
H-1       | SMA  | CABLE 385 RF-150-A-2   |  Mosaic H, Channel 1      
H-2    | SMA | CABLE 385 RF-150-A-2  |  Mosaic H, Channel 2 
X5       | SMA | CABLE 385 RF-150-A-2  |  Mosaic X5   
CELL1   | SMA    | CABLE 385 RF-150-A-2 | Cellular 1          
CELL2   | SMA | CABLE 385 RF-150-A-2 |  Cellular 2

</center>

## Cellular Modem Installation




