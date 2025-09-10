<p align="center"><img src="https://github.com/alexantao/aquaeye/blob/main/aquaeye-logo.jpeg?raw=true" width="350"></p>

This is a Aquarium monitoring system.
It has a circuit board part, with some sensors (pH, Flux, Water Level and temperature) and the HomeAssistant part, that will show the values and control some equipment connected.

It has a lot of improvements to make.

# Description

It consists on two parts: hardware and software:

* Hardware
  


* Software

  Software is based on Homeassistant. As explained earlier, the HW uses a ESP32 module wich contains Wi-Fi and connects itself to a HomeAssistant instance.
  All parameters are configured there. If you will not use a HASSIO server, you will have in hardcode the parameters on Esphome.
