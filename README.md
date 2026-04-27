# USB ELRS Sim Receiver

Use your ExpressLRS transmitter as a USB joystick for PC simulators using a Seeed Studio XIAO board and an ELRS receiver.

This project is based on [pfeerick/USB_ELRS_Receiver](https://github.com/pfeerick/USB_ELRS_Receiver), with added Web Serial configuration, live channel monitoring, calibration, persistent settings, and configurable HID button mapping.

---

## Overview

The ELRS receiver sends CRSF channel data to the XIAO board. The firmware converts that CRSF data into USB HID joystick axes and buttons, allowing the device to be used directly in PC simulators.

The included web configurator communicates with the device through Web Serial and allows live configuration from a browser.

The device appears on the computer as:

- a USB HID joystick / gamepad
- a USB Serial configuration interface

---


## Features

- USB HID joystick output
- ExpressLRS / CRSF receiver input
- 8 analog joystick axes
- 16 HID buttons
- Web Serial configurator
- Live CRSF channel monitor
- Live HID axis preview
- Live button state preview
- Axis calibration
- Axis inversion and deadband configuration
- Button mapping from any CRSF channel
- LOW / MID / HIGH switch-position detection
- Persistent configuration saving
- Default configuration restore
- Support for Seeed Studio XIAO SAMD21
- Support for Seeed Studio XIAO RP2040

---

## Hardware Requirements / Recommendations

### Microcontroller

- [Seeed Studio XIAO SAMD21](https://www.seeedstudio.com/Seeeduino-XIAO-Arduino-Microcontroller-SAMD21-Cortex-M0+-p-4426.html)
- [Seeed Studio XIAO RP2040](https://www.seeedstudio.com/XIAO-RP2040-v1-0-p-5026.html)

The XIAO SAMD21 is the recommended target if you want the simplest setup.

The XIAO RP2040 is also supported, but the RP2040 PlatformIO toolchain may require a larger download and extra driver handling on some Windows systems.

### ELRS Receiver

Any receiver with CRSF serial output should work.

Recommended compact receivers:

- Happymodel EP1 / EP2
- Radiomaster RP series
- iFlight ELRS receiver
- BetaFPV ELRS receiver

The receiver choice is not critical as long as it outputs CRSF and uses 3.3 V-compatible serial signaling.

---

## Device Connections

Connect the ELRS receiver to the XIAO UART pins.

```text
XIAO   ---   ELRS Receiver
==========================
5V     <-->  5V
GND    <-->  GND
RX     <-->  TX
TX     <-->  RX
````

Both signal lines are 3.3 V logic.

Because CRSF uses serial communication, RX must be connected to TX and TX must be connected to RX.

---

## Wiring Images

### XIAO SAMD21

![XIAO SAMD21 wiring](https://raw.githubusercontent.com/pfeerick/USB_ELRS_Receiver/master/doc/xiao-samd21-wiring.png)

### XIAO RP2040

![XIAO RP2040 wiring](https://raw.githubusercontent.com/pfeerick/USB_ELRS_Receiver/master/doc/xiao-rp2040-wiring.png)

The XIAO boards have VIN and GND available at the end of the board. When USB is connected, VIN provides 5 V, which makes the wiring compact and clean.

---

## Finished Build Examples

### XIAO SAMD21 with Happymodel EP2 Receiver

![Built SAMD21 CRSF receiver](https://raw.githubusercontent.com/pfeerick/USB_ELRS_Receiver/master/doc/xiao-samd21-built-unit.png)

### XIAO RP2040 with iFlight 2.4 GHz Receiver

![Built RP2040 CRSF receiver](https://raw.githubusercontent.com/pfeerick/USB_ELRS_Receiver/master/doc/xiao-rp2040-built-unit.png)

---

## Building and Uploading

This project is intended to be built with Visual Studio Code and PlatformIO.

Install:

* [Visual Studio Code](https://code.visualstudio.com/)
* [PlatformIO IDE](https://platformio.org/platformio-ide)

Open the full project folder in VS Code, not only `main.cpp`.

Then open the PlatformIO sidebar and select the correct environment.

![PlatformIO upload](https://raw.githubusercontent.com/pfeerick/USB_ELRS_Receiver/master/doc/pio-upload.png)

---

## PlatformIO Environments

The project supports two targets:

```text
xiao_samd21
xiao_rp2040
```

If upload fails, double-tap the reset button to enter bootloader mode and run upload again.

For RP2040 boards, enter bootloader mode if needed:

```text
Hold BOOT
Press RESET
Release RESET
Release BOOT
```

On some Windows systems, RP2040 upload may require installing a driver with [Zadig](https://zadig.akeo.ie/) for `RP2 Boot2`.

---

## Web Configurator

The web configurator is provided in:

```text
[OPERN WEB CONFIGURATOR](https://fak3r.github.io/usb-elrs-sim-receiver/)
```

It uses the browser Web Serial API to communicate with the firmware.

To use it:

1. Flash the firmware.
2. Connect the USB ELRS receiver to the Computer.
3. Power on and bind your Radio.
4. Open `[index.html](https://fak3r.github.io/usb-elrs-sim-receiver/)`.
5. Click **Connect configuration**.
6. Select the USB Serial port for the device.
7. Use the live monitor, calibration, and button mapping tools.

---


## Calibration

Calibration should be done after flashing the firmware or when the joystick range does not feel correct in the simulator.

Recommended procedure:

1. Center roll, pitch, and yaw.
2. Set throttle to 50%.
3. Click **Capture center**.
4. Click **Start travel calibration**.
5. Move all sticks to their full minimum and maximum positions.
6. Click **Stop travel calibration**.
7. Click **Save**.

Calibration values are stored persistently on the USB xIAO board.

Storage backend:
  
```text
SAMD21  -> FlashStorage
RP2040  -> EEPROM emulation
```

---

## Button Mapping

Each HID button can be mapped to a CRSF channel and a switch position.

Available positions:

```text
OFF
LOW
MID
HIGH
```

Example:

| HID Button | CRSF Channel | Position |
| ---------- | -----------: | -------- |
| Button 1   |          CH5 | LOW      |
| Button 2   |          CH5 | MID      |
| Button 3   |          CH5 | HIGH     |
| Button 4   |         CH10 | HIGH     |

After changing mappings:

1. Click **Apply buttons**.
2. Click **Save**.

---

Axis configuration:

```text
CFG AXIS <axis> <min> <mid> <max> <invert> <deadband>
```

Example:

```text
CFG AXIS 1 172 992 1811 0 3
```

Button configuration:

```text
CFG BTN <button> <channel> <position>
```

Example:

```text
CFG BTN 1 5 HIGH
```

---

## Troubleshooting

### The browser does not show the serial port

Use Chrome or Edge on desktop.

Make sure no other application is using the serial port.

Close:

* PlatformIO Serial Monitor
* Arduino Serial Monitor
* Betaflight Configurator
* any other serial terminal

Only one application can use the serial port at a time.

---

### No CRSF signal

Check:

* The ELRS receiver is powered.
* The ELRS receiver is bound to the transmitter.
* RX and TX are crossed correctly.
* The receiver output is configured as CRSF.
* The transmitter model is outputting the expected channels.

---

### Joystick appears but axes do not move

Check:

* CRSF signal status in the web configurator.
* Receiver wiring.
* UART pin selection.
* Channel order.
* Axis calibration values.

---

### Buttons do not work

Check:

* Button mapping.
* Channel values in the live monitor.
* LOW / MID / HIGH position selection.
* Whether the mapping was saved.

---


## Acknowledgments

Based on the project originally developed by Peter Feerick:

* [https://github.com/pfeerick/USB_ELRS_Receiver](https://github.com/pfeerick/USB_ELRS_Receiver)

This version adds Web Serial configuration, live monitoring, calibration, persistent settings, and configurable HID button mapping.
```
The image paths and part of the readme come from the original `pfeerick/USB_ELRS_Receiver` `doc/xiao-samd21-wiring.png`, `doc/xiao-rp2040-wiring.png`, `doc/xiao-samd21-built-unit.png`, `doc/xiao-rp2040-built-unit.png`, and `doc/pio-upload.png`. 
```
---

## License

This project is released under the MIT License.

Keep the original license notices when redistributing modified versions.
