# STM32 BMP280 Environmental Monitor

Embedded C firmware for an STM32 Nucleo board with an STM32F411RE microcontroller and a BMP280 sensor. It reads temperature and atmospheric pressure over I2C, estimates altitude from a reference pressure, and streams measurements to a serial terminal over UART.

## Features

- **Normal mode:** reads and reports measurements every second.
- **Forced mode:** triggers individual sensor measurements and reports every 5 seconds.
- **Altitude calibration:** sets the current pressure as the altitude reference using the on-board button.
- **Configurable filtering:** toggles the BMP280's IIR filter through a serial command.
- **Pressure history:** stores the latest 10 readings in a circular buffer.
- **Alerts:** activates an external LED and prints an alert when temperature exceeds 45 °C, pressure falls more than 100 hPa below the reference, or pressure drops more than 5 hPa within 10 seconds of stored readings.
- **Interrupt-driven controls:** handles buttons, UART input, and a 50 ms timer tick through interrupts.

## Hardware

- STM32 Nucleo board with STM32F411RE
- BMP280 sensor module
- External push button
- External LED with a 220–330 Ω series resistor

| Component / signal | Connection |
| --- | --- |
| BMP280 VCC | 3.3 V |
| BMP280 GND | GND |
| BMP280 SCL | PB8 |
| BMP280 SDA | PB9 |
| BMP280 SDO | GND |
| BMP280 CSB | 3.3 V |
| External button | Between 3.3 V and PA9 (D8) |
| External LED | PC7 (D9) → LED → series resistor → GND |
| On-board button | PC13 |
| On-board status LED | PA5 |
| UART TX / RX | PA2 / PA3 (USART2) |

## Getting started

1. Wire the components as shown above.
2. Open `lab_03.uvprojx` in **Keil µVision**, with ARM Compiler 6 and the project's required CMSIS / STM32 device packs installed.
3. Build the project and flash the board using ST-LINK.
4. Open the board's serial port in a terminal at **115200 baud, 8 data bits, no parity, 1 stop bit**, with flow control disabled.

The firmware starts in normal mode and prints temperature in °C, pressure in hPa, and estimated altitude in metres. The initial reference pressure is **1013.25 hPa**; pressing the on-board button sets it to the current measured pressure.

## Controls

| Input | Action |
| --- | --- |
| External button | Switch between normal and forced modes |
| On-board button | Calibrate the altitude reference |
| `f` / `F` | Toggle the sensor's IIR filter |
| `s` / `S` | Print mode, filter state, reference pressure, and pressure history |
| `c` | Clear an active alarm |

During an active alarm, measurements pause and only the lowercase `c` command is handled. An alarm can trigger again if its condition persists.

## Project layout

- `main.c` — application logic, operating modes, altitude calculation, and alerts.
- `drivers/bmp280.c` / `bmp280.h` — sensor configuration, calibration data, and compensated readings.
- `drivers/` — I2C, UART, GPIO, timer, and queue support.
- `RTE/` — device startup and runtime configuration.
- `lab_03.uvprojx` — Keil µVision project.
