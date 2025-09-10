<p align="center"><img src="https://github.com/alexantao/aquaeye/blob/main/images/aquaeye-logo.jpeg?raw=true" width="350"></p>

This is a Aquarium monitoring system.
It has a circuit board part, with some sensors (pH, Flux, Water Level and temperature) and the HomeAssistant part, that will show the values and control some equipment connected.

It has a lot of improvements to make.

# Description

It consists on two parts: hardware and software:

* Hardware

  The hardware is based on a ESP32-C3 Supermini module, wich contains all tecnology necessary to work with the sensors and also integrate with Homeassistant (see below).
  I designed a PCB with some connectors to the sensors and protections for the ESP32 module aginst short circuit from the sensors.
  Althrough I could power the ESP32 module directly on USB, and the sensors getting their power from the ESP32 module pins, I decided to power all of them directly from the external power source, to elmininate the possibility the module cannot send enouth energy for all sensors at once.
  With this modification, the module is still powered by USB (some does not accepts powering directly from the pin) and the sensors gets a dedicated circuit controlled by a TPS2553 to avoid short circuits, that if happens, the module will still work, the energy for the sensors are cut and a signal is sent to ESP32 and consequantly, HomeAssistant.


* Software

  Software is based on Homeassistant. As explained earlier, the HW uses a ESP32 module wich contains Wi-Fi and connects itself to a HomeAssistant instance.
  All parameters are configured there. If you will not use a HASSIO server, you will have in hardcode the parameters on Esphome.
