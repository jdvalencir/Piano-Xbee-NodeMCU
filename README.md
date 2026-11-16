# Piano-Xbee-NodeMCU

Arduino/ESP8266 sketches that send text typed into a web page to a remote LCD over XBee radios. A NodeMCU (ESP8266) hosts a small web form, an Arduino Mega builds XBee API frames and transmits them, and a second Arduino Mega receives them and prints the text on a 16x2 LCD. The commit message identifies it as a second-term ("Parcial2") IoT exam project.

## Features

- NodeMCU runs an HTTP server on port 80 that serves a form with a single text input ("Ingrese el texto a mostrar").
- The submitted `input` value is forwarded over serial (9600 baud) to the next stage.
- Transmitter sketch (`xbeeT.ino`) reads text from `Serial2`, builds an XBee API transmit frame (0x7E start delimiter, length, frame type 0x00, 64-bit destination address, payload, checksum) and writes it to the XBee on `Serial3`.
- Receiver sketch (`XbeeR.ino`) reads the incoming frame from `Serial3`, strips the frame header and checksum byte, and shows the payload on a 16x2 LCD and the serial monitor.
- `index.html` is a standalone copy of the form page that the NodeMCU sketch embeds.

## Tech stack

- Arduino C++ (`.ino` sketches)
- ESP8266 (NodeMCU) with `ESP8266WiFi` and `ESP8266WebServer`
- XBee modules in API mode
- Arduino Mega-style boards (uses `Serial2`/`Serial3`)
- `LiquidCrystal` library, 16x2 LCD
- HTML/CSS

## Project structure

```
ServidorArduino/ServidorArduino.ino   NodeMCU web server (form -> serial)
xbeeT.ino                             XBee transmitter (builds API frames)
XbeeR.ino                             XBee receiver (prints to LCD)
index.html                            Static copy of the web form
```

## Getting started

There is no build system; the sketches are compiled and uploaded with the Arduino IDE (or Arduino CLI).

1. Install the ESP8266 board package and select your NodeMCU board.
2. In `ServidorArduino/ServidorArduino.ino`, set `ssid` and `password` to your own Wi-Fi network, then upload it.
3. In `xbeeT.ino`, set `macXbeeR` to the 64-bit address of the receiving XBee, and upload to a board with `Serial2` and `Serial3` (e.g. Arduino Mega).
4. Upload `XbeeR.ino` to the receiving board, with the LCD wired to pins 48, 49, 45, 44, 43, 42 and the XBee on `Serial3`.
5. Open the NodeMCU's IP address (shown by your router) in a browser, submit text, and it should appear on the LCD.

The wiring between the NodeMCU and the transmitter board's `Serial2` is not documented in the repo; the NodeMCU sketch writes to `Serial1`.

## Context

University IoT course project (second partial exam, "Parcial 2"). Despite the repository name, the code does not implement a piano; it transmits text messages.
