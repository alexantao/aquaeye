<p align="center"><img src="https://github.com/alexantao/aquaeye/blob/main/images/logo com borda - en.png?raw=true" width="350"></p>

This is a Aquarium monitoring system. It has two essential parts:
* **Hardware:** Circuit board, with some sensors (pH, Flux, Water Level and temperature)
* **Software:** HomeAssistant, that will show the values and control some equipment connected.

It has a lot of improvements to make.

# Description

It consists of two parts: hardware and software:

## **Hardware**

  The hardware is ehe heart of the system, is based on a ESP32-C3 Supermini module, wich contains all tecnology necessary to work with the sensors and also integrate with Homeassistant (see below).
  I designed a PCB with some connectors to the sensors and protections for the ESP32 module aginst short circuit from the sensors.
  Althrough I could power the ESP32 module directly on USB, and the sensors getting their power from the ESP32 module pins, I decided to power all of them directly from the external power source, to elmininate the possibility the module cannot send enouth energy for all sensors at once.
  With this modification, the module is still powered by USB (some does not accepts powering directly from the pin) and the sensors gets a dedicated circuit controlled by a TPS2553 to avoid short circuits, that if happens, the module will still work, the energy for the sensors are cut and a signal is sent to ESP32 and consequantly, HomeAssistant.

  I use most cheap sensors that exists on the market, but must be careful, lots of those cheap harwdare are low quality. But the goal of this project is to make a affordable and reliable monitoring system.
  We also have a lot os options related to sensors tyes. and below are a list of those I tested:

  
| Temperature Sensor: **DS18B20** | Light Sensor: **GY-302** |
|:---:|:---:|
|<div align=center>
<img src="https://github.com/alexantao/aquaeye/blob/main/images/dallas.jpeg?raw=true" width="150">|<img src="https://github.com/alexantao/aquaeye/blob/main/images/gy-302.jpeg?raw=true" width="150">|
|This sensor is water proof and measures the water temperature. Got some cheap ones that stopped working, but the measurement is quite good.|I added this sensor because one of my aquariums get sunlight on winter. When it happens, I can delay the aquarium light to turn on some hours.|

</div>

  * Water Level: **HC-SR04** , **RCWL-1670**, **JSN-SR04T/AJ-SR04M**
    
    These are sensors that uses sonic waves to measure the distance. I tested these three:

| HC-SR04 | RCWL-1670 |
|:-------------:|:--------------:|
| <img src="https://github.com/alexantao/aquaeye/blob/main/images/sonic1.jpeg?raw=true" width="150"> | <img src="https://github.com/alexantao/aquaeye/blob/main/images/RCWL-1670.jpg?raw=true" width="150"> |
| The most simple of then, the board is attached with the sensors and gives a good precision. Measures distances for at most 2 meters and minimal of about 2cm. The problem with it is that it is not waterproof, and on of then got rusty in little time. | Identical from the HR-SR04, but it claims to be water proof. Only the sensor itself, of course. The board is not.  |

| **Sonic sensor SN-SR04T/AJ-SR04M** |
|:-------------:|
| <img src="https://github.com/alexantao/aquaeye/blob/main/images/sonic2.jpeg?raw=true" width="150"> |
  **DON'T USE !** This one is quite different, the sensor is water proof and the board is split from it. It's the same as those used in cars bumpers as parking sensor. The advantage is clearly the water proof design, but the board will take some extra space on the box.
  $${\color{red}THE\space PROBLEM\space  WITH\space  THIS\space  SENSOR\space  IS\space  THAT\space  THE\space  MINIMUM\space  ACURATE\space  DISTANCE\space  IS\space  20CM!}$$ |
| The reason of this is that, since this sensor has only one component that serves both for TRIGGER and ECHO, it needs to change from one to another, and this transaction takes some _ms_ to make. So, if it's too close, the waves will return before it can hear the ECHO properly. |

**Atention:**: Verify the sensor return unit you have. HC-SR04 return distance in centimeters, JSN-SR04T returns in meters. You must adjust the code to reflect yours

  
  * Flux sensor: This is to monitor the water flux the canister is sending to the aquarium. For several times I noticed a low flow, due to obstruction of even a impeller fault. With this sensor I can even predict when I need to clean the canister, because the flow gets slower. There's a lot of flow sensors on the market, some with a open/close valve e others with temperature sensor included. Each sensor has a _Factor number_ that indicates the number of pulses by liter. This factor can be configured by software (see below).
    
| YF-B05 | YF-201c |
|:---:|:---:|
| <img src="https://github.com/alexantao/aquaeye/blob/main/images/yf-b05.jpeg?raw=true" width="150"> |  <img src="https://github.com/alexantao/aquaeye/blob/main/images/yf-201c.jpeg?raw=true" width="150">  |
|  this sensor is made of metal and has a 3/4' (16mm) thread |  this sensor is made of plastics and has a 1/2' (13mm) thread |


| PH: **PH-4502C** | OLED display **SSD1315** |
| :---: | :---: |
| <img src="https://github.com/alexantao/aquaeye/blob/main/images/ph.jpeg?raw=true" width="150"> | <img src="https://github.com/alexantao/aquaeye/blob/main/images/oled.jpg?raw=true" width="150"> |
| This sensor measures the PH of the water. The real measure it returns is a voltage acording the PH of the water. So, it must be calibrated properly so that the software interpret that Voltage and transform into a PH value. I still could not make it work properly, but it is included on the hardware for future utilization. | I added this display so that we can see the sensors values even whenm the connection to HomeAssistant is not working |


| Support | Connectors |
|:---:|:---:|
| <img src="https://github.com/alexantao/aquaeye/blob/main/images/suporte-sensor.jpg?raw=true" width="150"> | <img src="https://github.com/alexantao/aquaeye/blob/main/images/connectors.png?raw=true" width="150"> |
| I also had to print a support for the sonic sensor and temperature from <a href="https://www.thingiverse.com/thing:5439668">https://www.thingiverse.com/thing:5439668</A> | To make it portable, I decided to connect the sensors with pluggable connectors. I tested several ones, like those audio Jack. Soldering was a challenge for me. To make things easier, I bought on Ali an magnetic connector that already comes with wires soldered. Very high quality. |

</div>

### **Schematic and PCB**

The electronic schematic and PCB board I designed on EasyEDA. This is my very first project, so it may not be optimized and also may contain some error. 
The only thing you must taka a look are those 2 resistors between Flux Signal Output to ESP32 pin. That's because depending on the sensor's current, they may be necessary to be adjusted. They must reduce the input voltage on ESP32 pin to 3.3V maximum. I put 2x100 ohms, because my sensors consumes 10mA and 15mA each, so the perfect resistors should be a 170ohms and a 113ohms to reduce to that voltage. Bigger should be OK if not too high, because the minimum voltage onsidered as a HIGH signal on ESP32 is 0.7V.
There's also a divider for the PH sensor, to transform the voltage (VIN) to the pin (maximum 3.3v). To make thing more flexible, I put 1 Resistor for R2 (connected to Ground) and 2 for R1, connected to VIN. Those values are reference because that were the resistor I had. Feel free to chose yours.

<div align="center">

| Schematic | FOOTPRINT | PCB (top layer) |PCB (bottom layer) | 3D view |
|---|:---:|:---:|:---:|:---:|
| <img src="https://github.com/alexantao/aquaeye/blob/main/images/esquematico.png?raw=true" width="150"> | <img src="https://github.com/alexantao/aquaeye/blob/main/images/PCB-FOOTPRINT.png?raw=true" width="150"> | <img src="https://github.com/alexantao/aquaeye/blob/main/images/2d-toplayer.png?raw=true" width="150"> | <img src="https://github.com/alexantao/aquaeye/blob/main/images/2d-bottomlayer.png?raw=true" width="150"> | <img src="https://github.com/alexantao/aquaeye/blob/main/images/3D-PCB.png?raw=true" width="150"> |

**BOOM** - Lista de Materiais
|  Component | Detail | Quantity |
|:---:|:---:|:---:|
| Led SMD | LED0805-R-RD | 1 |
| Capacitor | Ceramic 100uF 16V | 6 |
| Diode | SMA_L4.3-W2.6-LS5.2-RD | 6 |
| Conector | CONN-TH_PH2.0D-L-3P | 3 |
| Conector | CONN-TH_WAFER-PH2.0-2PZZ-Y62 | 2 |
| Conector | CONN-TH_PH2.0-4P | 3 |
| Regulator | AMS1117-3.3 BUCK CONVERTER BREAKOUT | 1 |
| Resistor | R0603 100 ohms | 3 |
| Resistor | R0603 150 ohms | 2 |
| Resistor | R0603 470 ohms | 1 |
| Resistor | R0603 5.1 Kohms | 1 |
| Resistor | R0603 4.7 Kohms | 1 |
| Chip | TPS2553DBVR | 1 |
| Module | ESP32-C3-SUPER-MINI | 1 | 
| Connector | DC-022 Jack P4 | 1 |
| Box | Generic | 1 |

</div>

# OPTIONAL DISPLAY
As an optional display, I bought a 1.28 inches round IPS Smart Display powered by a ESP32-C3. Wifi and Bluetooth integrated, 240*240 resolution and alsowith touch control.
This display is autonomous and is not part of the AquaEye, but it eases a lot data visualization, and with it, there's no need of an extra button to control the aquarium devices.
All you only need is a USB-C cable to power it, And it takes all information from HomeAssistant. 
Since it it touch, I integrated controls, like light, UV, air, etc... 

<div align=center>
<img src="https://github.com/alexantao/aquaeye/blob/main/images/round-display.jpeg?raw=true" width="150">
</div>

The code for the display is in the code directory of this git (round-display.yaml).
**Don't forget to allow actions from the display to HomeAssistant Instance, or the command buttons will not work !**

# **PROJECT FILES AND ESPHOME CODE**

  The circuit board, since it's core is a ESP32, it needs a firmware to run. I choose to run ESPHOME, because it is very easy to work woth and it is HomeAssistant compatible and easy to integrate.
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

You will also need to make some adjustments in the panel YAML, for example, the Aquarium light entity, circulation pump, etc....

The idea is to use HA packages feature: 
1. Create a _packages_ directory inside HomeAssistant config dir;
2. copy YAML files there (careful, there's YAML for ESPHOME and for HomeAssistant);
3. inside _configuration.yaml_, include the statements:
   ```
    packages: !include_dir_named packages
   ```
4. Check and restart HomeAssistant.
5. Copy _aquaeye-images_ directory contents to /config/www/aquaeye-images directory
6. Add a lovelace Picture-Elements card on a new dashboard or on an existent one with the contents of the <a href="https://github.com/alexantao/aquaeye/blob/main/code/panel-card.yaml">panel-card.yaml</A> file. My lovelace Dashboard (in development)  that includes my 3 aquariums is on the code directory as the name lovelace.yaml.

Enjoy

Code files are located at: <A HREF="https://github.com/alexantao/aquaeye/tree/main/code">https://github.com/alexantao/aquaeye/tree/main/code</A>
   
