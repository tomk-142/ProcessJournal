### Thermal Printer  Code

---

## This is currently the final code for the Thermal Printer

---

**Language:** Arduino C++

```cpp
// Narcissus printer bridge
// Passes everything the PC sends over USB straight to the thermal printer.
#include <SoftwareSerial.h>

const long PRINTER_BAUD = 9600;   // from the printer's self-test page (9600 or 19200)

SoftwareSerial printer(5, 6);     // pin 5 = RX (unused), pin 6 = TX -> printer RX

void setup() {
  Serial.begin(PRINTER_BAUD);     // USB from the PC (same speed as the printer)
  printer.begin(PRINTER_BAUD);
}

void loop() {
  while (Serial.available()) {
    printer.write(Serial.read());
  }
}
```
