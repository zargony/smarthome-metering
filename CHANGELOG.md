# Smart Home Energy Metering Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## Unreleased

- Fixed contact labels order on silkscreen and front label

## 1.1 - 2025-06-30

- Submitted for manufacturing (JLCPCB)
- Added more GND vias
- Adjusted board constrains to allow easier (cheaper) manufacturing

## 1.0 - 2025-06-27

- Posted for review on [r/PrintedCircuitBoard](https://www.reddit.com/r/PrintedCircuitBoard/comments/1llyu23/review_request_esp32c3_board_for_reading_energy/)
- 2-layer base and vertical PCB for CNMB-2-2 DIN rail enclosure
- 3.3V UART for reading electrical meter's IR interface
- 3 debounced digital inputs for reading gas and water reed contacts
- status/activity LEDs
- power supply via 5V DC IN
- external SMA antenna connector
- USB-C for updating/debugging/power (safe to use while connected to 5V DC)

## 0.1 - 2023-11-28

- NodeMCU ESP8266 prototype on perfboard
