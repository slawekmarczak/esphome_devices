# Device Test Log

## 2026-05-13 10:04 UTC — Increment 1: ESP32-C2 basic platform

- Firmware/YAML: ESP32-C2 basic platform (WiFi, API, OTA, Logger, Web Server, SPI bus, status LED, WiFi sensors)
- Compile: SUCCESS (134.60s, ESPHome 2026.4.3, toolchain-riscv32-esp 14.2.0+20260121)
- RAM: 11.3% (31600 / 278528 bytes)
- Flash: 47.2% (866436 / 1835008 bytes)
- Flash: Not performed.
- Notes: ESP32-C2 RISC-V toolchain required ~2.1GB disk. Platform successfully validated.

## 2026-05-13 10:06 UTC — Hardware switch: ESP32-C2 → ESP32 DevKit

- Decision: ESP32-C2 dropped. ESPSomfy-RTS only supports standard ESP32 (Xtensa).
- New board: ESP32 DevKit V1 (ESP32-WROOM-32, esp32dev).
- Firmware: ESPSomfy-RTS pre-built binary (v2.4.6, 1.3 MB).
- Pin mapping: Follows ESPSomfy-RTS wiki recommendations for E07-M1101D.
- Flash: Not yet performed (hardware not connected).
- Binary: SomfyController.ino.esp32.bin downloaded from GitHub releases.
