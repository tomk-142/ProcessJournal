### DES222 Task 2 and 3 Process Journal
# This process journal will document the stages of ideation to creation for task 2/3's responsive tech design. The format and tone will resemble closer to a personal journal as opposed to a strict research journal, but will provide the necessary information and plans to recreate this project in the future if need be.
---
### Narcissus
---
## The Big Idea
This project aims to respond visually to the users heart rate via a distortion effect applied to a live camera feed.
---

## Entry 1 06/09/26
The actual creation of this process journal was about a week ago, however, I have only started creating entries now that I have an overall plan for this project. After contemplating the overall difficulty of my project idea I decided it would be possible to attempt my ideal project outcome (standalone camera and heart rate monitor device sending data to a computer screen with printing capabilities). After researching which different microcontrollers and devboards would be suitable for my task, I settled on a ESP32-SC-CAM devkit that included a OV3660 camera. At this stage I have tested the device and the camera works great! I tested it by using a pre-made "example" in the Arduino IDE library specifically for this sort of project.

---

![alt text "camera test"](images/CameraTest.jpg)

---

## Entry 2 08/09/26
This entry is when I began creating my pitch presentation and started researching about similiar projects such as [Pulse Topology](https://design-milk.com/rafael-lozano-hemmer-takes-your-pulse/), [Pulse Room](https://www.lozano-hemmer.com/pulse_room.php) and [Reccurent Waiting](https://www.lozano-hemmer.com/recurrent_waiting.php). after reviewing these projects I changed the name of my project to what it is currently called (it was originally called pulse). All three of these projects are created by Rafael Lozano-Hemmer who I now realise is basically the godfather of interactive heart beat art displays. Rafael's work in the "Pulse" series sees lightbulbs either pulsing or flickering in some way to the participants heart rate. when the next participant adds their heart beat to the display, it pushes the previous pulse along until eventually, after enough new participants join, the pulse is gone, representing the inevitability of death in a gentle way. the "Reccurent Waiting" piece was more dramatic in its messaging. "Reccurent Waiting" is an interactive mirror installation that explores the tension between self-perception, loss of control, and digital puppetry. The mirror replicates the viewers image but the reflection blinks and moves erratically following a timed sequence that transmits Lucky's monologue from "Waiting for Godot" in morse code. I chose to mention this work of his as it closeley relates to a "distortion" of some sort on a live reflection of the viewer, something that I'm planning on achieving. the themes he is representing in that piece is also quite similiar to that of my own for my project. I Also tested the heart rate monitor on the esp32 using a [library](https://docs.arduino.cc/libraries/pulsesensor-playground/) that I downloaded that included more examples. at this stage my goal was to make sure both devices could receive and send data to my computer and they are successful in doing so at this stage.

---

## Entry 3 10/09/26
I started this session by finalising my slideshow for the presentation, no script was created for it as I was planning on freestyling with just a few notes. hopefully it didnt backfire. This will be the final entry before the Task 2 Pitch is presented and the next entry/ies will process the physical creation of the project. for now, I will create a list below of products that I purchased for this project, and other items that I already had that im expecting to use. not all products listed will be used and future entries will narrow down the exact products for the final project.

- ESP32 S3 CAM Development Board + OV3660 Camera
- 16mm Illuminated Green Momentary Push Button Switch – 4-Pin
- MT3608 Step Up Module
- 3.7V 2600mAh 18650 Cell Li-Ion Battery With Soldered Tabs – Lithium Ion
- TP4056 Type C 18650 Lithium Battery Charger + Protection
- Copper Prototype Perfboard - 7x9cm
- 170 Tie-points Mini Solderless Breadboard - Yellow
- Assorted m/m m/f f/f jumper wires
- Assorted LEDS
- Multimeter
- Soldering Iron
- Thermal Printer
- USB-C to USB-C Cable

---

