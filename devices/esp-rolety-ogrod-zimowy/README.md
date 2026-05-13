# Rolety Ogrod Zimowy

ESPHome controller for Somfy RTS roller blinds in the winter garden.

## Hardware

- ESP32-C2 Super Mini A
- Ebyte E07-M1101D (CC1101 433 MHz transceiver)
- Dupont wiring (8 connections)

## Wiring

See `hardware.md` for full pin-by-pin wiring table.

## Setup

1. Copy `secrets.yaml` and fill in real credentials.
2. Compile and flash via the MCP pipeline (`~/esphome_agent`).
3. Pair blinds using the pairing button in Home Assistant.

## Firmware

ESPHome with esp-idf framework. The Somfy RTS protocol is implemented as
custom C++ code driving the CC1101 transceiver over SPI.
