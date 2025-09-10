<p align="center"><img src="https://github.com/alexantao/aquaeye/blob/main/images/aquaeye-logo.jpeg?raw=true" width="350"></p>

This is a Aquarium monitoring system. It has two essential parts:
* **Hardware:** Circuit board, with some sensors (pH, Flux, Water Level and temperature)
* **Software:** HomeAssistant, that will show the values and control some equipment connected.

It has a lot of improvements to make.

# Description

It consists on two parts: hardware and software:

* **Hardware**

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

**CODE**
The circuit board, as it has a ESP32 core, it needs a firmware to run. I choose to run ESPHOME, because it is very easy to work woth and it is HomeAssistant compatible and easy to integrate.
All codes are included inside this GitHub.


**Schematic and PCB**
The electronic schematic and PCB board I designed on EasyEDA. This is my very first project, so it may not be optimized and also may contain some error. 

| Schematic | PCB (top layer) | 3D view |
|---|:---:|---:|
| <img src="https://github.com/alexantao/aquaeye/blob/main/images/esquematico.png?raw=true" width="150"> | <img src="https://github.com/alexantao/aquaeye/blob/main/images/2d-toplayer.png?raw=true" width="150"> | <img src="https://github.com/alexantao/aquaeye/blob/main/images/3D-PCB.png?raw=true" width="150"> |



* **Software**

  Software is based on Homeassistant. As explained earlier, the HW uses a ESP32 module wich contains Wi-Fi and connects itself to a HomeAssistant instance.
  All parameters are configured there. If you will not use a HASSIO server, you will have in hardcode the parameters on Esphome.
