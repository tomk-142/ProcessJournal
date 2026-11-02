### Project Runthrough

---

## Below is a rundown of the project including materials used and setup information

---

The Narcissus Project has three parts: the camera unit, the PC running TouchDesigner, and the printer station.
• Camera unit: the plastic jiffy box with the ESP32-S3-CAM camera, the HW-827 heart rate sensor, the shutter button, the LED on/off switch and a charged 18650 battery.
• PC: the narcissus_project folder (TouchDesigner project, captures folder, narcissus_print.py), plus Python with pyserial and pillow installed.
• Display: a monitor or TV works best, but any screen will do
• Printer station: the thermal printer loaded with paper, the Arduino Uno with its USB cable, the Laptop USB-C charger and the 9V PD trigger module.
• WiFi: the network the camera is programmed for. The laptop must join the same network and have the correct IP address.

---

## Setup

---

1. PC: connect to the WiFi network, open the Narcissus_Project folder
2. Printer: plug the charger into the wall first, then flip the printer's switch on.
3. Arduino: plug the Uno into the PC via USB.
4. Camera unit: switch it on at the rocker switch. The on/off switch and shutter button light will come on.
5. TouchDesigner: open the project. Check the camera image is moving and the ECG line responds to a finger on the sensor.
6. Output window: pulse Open as Separate Window on the Window COMP to show the output on the display.
7. Print script: open Command Prompt in the project folder and run py narcissus_print.py. Leave the window open.
8. Test: on screen prompts will tell you to put your finger on the sensor, and then press the shutter to print.

---

## Materials Used

---

## Camera unit
- GOOUUU ESP32-S3-CAM (OV3660 camera)
- HW-827 PulseSensor (AliExpress dupe)
- 16mm Illuminated Push Button
- 220Ω Resistor (For Button LED)
- 18650 Li-ion Battery (3.7V, 260mAh) + holder
- TP4056 Charger Module
- MT3608 Boost converter
- LED Rocker Switch
- Perfboard, female headers, hookup wire
- Plastic Jiffy box
## Printer
- Thermal Receipt Printer (TTL, 5-9V)
- Thermal Printer rolls
- Arduino UNO R3 + USB Cable
- USB-C PD trigger module (Jaycar)
- USB-C Laptop Charger (I used an old lenovo one)
- Toggle Switch
- Jumper Wires
## PC Setup
- PC with TouchDesigner
- Monitor
## Consumables and tools
- Soldering Iron
- Solder
- Multimeter
- Heatshrink
- wire strippers
- Drill
- Step drill bit
- Needle nose pliers
- double sided foam tape
