<p align="center"><img src="https://github.com/alexantao/aquaeye/blob/main/images/aquaeye-logo.jpeg?raw=true" width="350"></p>

This is a Aquarium monitoring system. It has two essential parts:
* **Hardware:** Circuit board, with some sensors (pH, Flux, Water Level and temperature)
* **Software:** HomeAssistant, that will show the values and control some equipment connected.

It has a lot of improvements to make.

# Description

It consists on two parts: hardware and software:

## **Hardware**

  The hardware is ehe heart of the system, is based on a ESP32-C3 Supermini module, wich contains all tecnology necessary to work with the sensors and also integrate with Homeassistant (see below).
  I designed a PCB with some connectors to the sensors and protections for the ESP32 module aginst short circuit from the sensors.
  Althrough I could power the ESP32 module directly on USB, and the sensors getting their power from the ESP32 module pins, I decided to power all of them directly from the external power source, to elmininate the possibility the module cannot send enouth energy for all sensors at once.
  With this modification, the module is still powered by USB (some does not accepts powering directly from the pin) and the sensors gets a dedicated circuit controlled by a TPS2553 to avoid short circuits, that if happens, the module will still work, the energy for the sensors are cut and a signal is sent to ESP32 and consequantly, HomeAssistant.

  I use most cheap sensors that exists on the market, but must be careful, lots of those cheap harwdare are low quality. But the goal of this project is to make a affordable and reliable monitoring system.
  We also have a lot os options related to sensors tyes. and below are a list of those I tested:

  * Temperature Sensor: **DS18B20**
    This sensor is water proof and measures the water temperature. Got some cheap ones that stopped working, but the measurement is quite good.
     <p align="center"><img src="https://github.com/alexantao/aquaeye/blob/main/images/dallas.jpeg?raw=true" width="150"></p>
     <hr width=50%>
  * Water Level: **HC-SR04** , **RCWL-1670**, **JSN-SR04T/AJ-SR04M**
    
    These are sensors that uses sonic waves to measure the distance. I tested these three:

| HC-SR04 | RCWL-1670 | SN-SR04T/AJ-SR04M |
|:-------------:|:--------------:|:--------------:|
| <img src="https://github.com/alexantao/aquaeye/blob/main/images/sonic1.jpeg?raw=true" width="150"> | <img src="https://github.com/alexantao/aquaeye/blob/main/images/RCWL-1670.jpg?raw=true" width="150"> | <img src="https://github.com/alexantao/aquaeye/blob/main/images/sonic2.jpeg?raw=true" width="150"> |
| The most simple of then, the board is attached with the sensors and gives a good precision. Measures distances for at most 2 meters and minimal of about 2cm. The problem with it is that it is not waterproof, and on of then got rusty in little time. | Identical from the HR-SR04, but it claims to be water proof. Only the sensor itself, of course. The board is not.  | This one is quite different, the sensor is water proof and the board is split from it. It's the same as those used in cars bumpers as parking sensor. The advantage is clearly the water proof design, but the board will take some extra space on the box. |


 <hr width=50%>
  * Flux sensor: This is to monitor the water flux the canister is sending to the aquarium. For several times I noticed a low flow, due to obstruction of even a impeller fault. With this sensor I can even predict when I need to clean the canister, because the flow gets slower. There's a lot of flow sensors on the market, some with a open/close valve e others with temperature sensor included.
    
| YF-B05 | YF-201c |
|:---:|:---:|
| <img src="https://github.com/alexantao/aquaeye/blob/main/images/yf-b05.jpeg?raw=true" width="150"> |  <img src="https://github.com/alexantao/aquaeye/blob/main/images/yf-201c.jpeg?raw=true" width="150">  |
|  this sensor is made of metal and has a 3/4' (16mm) thread |  this sensor is made of plactis and has a 1/2' (13mm) thread |

  * PH: **PH-4502C**
    This sensor measures the PH of the water. The real measure it returns is a voltage acording the PH of the water. So, it must be calibrated properly so that the software interpret that Voltage and transform into a PH value. I still could not make it work properly, but it is included on the hardware for future utilization.
    <p align="center"><img src="https://github.com/alexantao/aquaeye/blob/main/images/ph.jpeg?raw=true" width="150"></p>

  * Light Sensor: **GY-302**
    I added this sensor because one of my aquariums get sunlight on winter. When it happens, I can delay the aquarium light to turn on some hours.
      <p align="center"><img src="https://github.com/alexantao/aquaeye/blob/main/images/gy-302.jpeg?raw=true" width="150"></p>

  * OLED display **SSD1315**
    I added this display so that we can see the sensors values even whenm the connection to HomeAssistant is not working
      <p align="center"><img src="https://github.com/alexantao/aquaeye/blob/main/images/oled.jpg?raw=true" width="150"></p>
      
  * I also had to print a support for the sonic sensor and temperature from <a href="https://www.thingiverse.com/thing:5439668">https://www.thingiverse.com/thing:5439668</A>:
    <p align="center"><img src="https://github.com/alexantao/aquaeye/blob/main/images/suporte-sensor.jpg?raw=true" width="150"></p>

### **Schematic and PCB**

The electronic schematic and PCB board I designed on EasyEDA. This is my very first project, so it may not be optimized and also may contain some error. 

<div align="center">

| Schematic | PCB (top layer) | 3D view |
|---|:---:|:---:|
| <img src="https://github.com/alexantao/aquaeye/blob/main/images/esquematico.png?raw=true" width="150"> | <img src="https://github.com/alexantao/aquaeye/blob/main/images/2d-toplayer.png?raw=true" width="150"> | <img src="https://github.com/alexantao/aquaeye/blob/main/images/3D-PCB.png?raw=true" width="150"> |

</div>

# **PROJECT FILES AND ESPHOME CODE**

  The circuit board, as it has a ESP32 core, it needs a firmware to run. I choose to run ESPHOME, because it is very easy to work woth and it is HomeAssistant compatible and easy to integrate.
The files included inside this GitHub are in <a href="https://github.com/alexantao/aquaeye/tree/main/code">code directory</a>:
* **aquaeye.yaml:** the ESPHome code I used to program the device
* **Gerber-.zip:** the gerber files to use
* **AquaEye-.epro:** Project file used in EaseEDA Pro

# Installation

With the hardware ready, get a 5V power source with a Jack 4p with at least 500mA. Aquaeye does not drain too much current.
You will need to flash Firmware first (refer to ESPHOME flashing procedures, its very simple, you will need only to plug a USB data cable to your computer to flash it) , and configure the WIFI.
Plug all sensors you will use **BEFORE** turning it on. Some sensors, like Dallas, requires to be connected on bootup sequence to be recognized.
After that, the system will automatically monitor your aquarium. 

The next step is HomeAssistant configuration.

<hr width=80%>

## **Software (HomeAssistant)**

  Software is based on Homeassistant.
  As explained earlier, the HW uses a ESP32 module wich contains Wi-Fi and connects itself to a HomeAssistant instance.

  ### **ADD-ON or INTEGRATION**
  For that, you will need to install ESPHOME Add-On (or integration, for the docker version). Click on below button to see and install:
   <p align="center"><A href="https://my.home-assistant.io/redirect/supervisor_addon/?addon=5c53de3b_esphome&repository_url=https%3A%2F%2Fgithub.com%2Fesphome%2Fhome-assistant-addon"><img src="https://github.com/alexantao/aquaeye/blob/main/images/supervisor_addon.svg?raw=true"></A> </p>

  After that, it should automatically find the Device on your network, if you configured it properly. If it does not apear, try to discover it's IP Address on your AccessPoint and try to mannually add it.

  ### Lovelace Card
  
  All parameters are configured there. If you will not use a HASSIO server, you will have in hardcode the parameters on Esphome.

 I mounted a simple and visual panel to see and control the aquarium. It has a representation for all sensors and also some commands implemented.

  <p align="center"><img src="https://github.com/alexantao/aquaeye/blob/main/images/panel.gif?raw=true"></p>

  These are the caracteristics:
  1. The water level is shown with fluid-level and reflects the sensor that’s installed on the aquarium. There’s the possibility to make the refil automatic (I don’t have it… yet).
  2. Canister flux is shown as a fan with the value below. I defined some severities, so that it will be red/green/orange depending on it’s level.
  3. Clicking on the circulation pump, it is activated/deactivated and the fan icon spins green if it's on.
  4. Clicking on the frog, activates/deactivates the air compressor connected to a frog that spits air in my aquarium. The water on the fluid changes, filled with bubbles, if it's on.
  5. The CO2 cylinder shows the estimated level of gas. For this I created a helper to store the last fill and another that has the estimated duration. Clicking on the valve opens/closes the solenoid to liberate the gas. Clicking on the canister, the date is set to today, indicating the REFILL was done and the level is updated. I still plan to make HA calculate automatically the duration time by the median time between refils, but I couldn’t figure it out yet how to do that.
  6. The light still has the basic on/of behavior, but I intend to reflect the intensity later making it opaque/transparent. My tries didn´t work.
  7. UV light on/off clicking on it.
  8. pH level will reflect the sensor reading. But I need a more representable gauge. I’m using the custom:pool-monitor-card because it was the closest I need, but I will look for another one better.
  9. Temperature bar: reflects the temperature sensor

All configuration for AquaEye project and anything related to the aquarium are configurated in this panel:
  <p align="center"><img src="https://github.com/alexantao/aquaeye/blob/main/images/painel-config.png?raw=true"></p>


Todo:

* Better light control, reflecting the brightness level, changing the opacity
* better pH gauge.
* Better positioning. Still learning how to do this correctly. I’m having issues when accessing in little screens, such as a phone.
* Controls to turn on devices not in the picture.
* Adjust temperature bar
* Show other parameters (amnonia, nitrite, nitrate, etc…)

  
# HOMEASSISTANT CONFIGURATION

Homeassistant configuration is complex (and not yet fully developped).
I'll try to make it simple by making YAML files to be included on _configuration.yaml_ file. 
See README file inside the code directory for a description of each.

AquaEye panel uses a series of integrations that must be installed before configuration:
* custom:pool-monitor-card (pH gauge)
* custom:fluid-level-background-card
* custom:button-card
* custom:slider-entity-row
* custom:hui-element
* custom:bar-card
* custom:card-templater

You will also need to make some adjustments in the panel YAML, for example, the Aquarium light entity.

The idea is to use HA packages feature: 
1. Create a _packages_ directory inside HomeAssistant config dir;
2. copy YAML files there (careful, there's YAML for ESPHOME and for HomeAssistant);
3. inside _configuration.yaml_, include the statements:
   ```
    packages: !include_dir_named packages
   ```
4. Check and restart HomeAssistant.
5. Add a lovelace Picture-Elements card on a new dashboard or on an existent one with the contants of the <a href="https://github.com/alexantao/aquaeye/blob/main/code/panel-card.yaml">panel-card.yaml</A> file.

Enjoy

Code files are located at: <A HREF="https://github.com/alexantao/aquaeye/tree/main/code">https://github.com/alexantao/aquaeye/tree/main/code</A>
   
