# Device Test Log

## 2026-05-13 10:04 UTC — Increment 1: Basic platform

- Firmware/YAML: ESP32-C2 basic platform (WiFi, API, OTA, Logger, Web Server, SPI bus, status LED, WiFi sensors)
- Compile: SUCCESS (134.60s, ESPHome 2026.4.3, toolchain-riscv32-esp 14.2.0+20260121)
- RAM: 11.3% (31600 / 278528 bytes)
- Flash: 47.2% (866436 / 1835008 bytes)
- Flash: Not yet performed (no device connected).
- UART logs: N/A.
- Network/API/Web: N/A.
- Result: Compilation validated. Ready for flash when hardware available.
- Notes: RISC-V toolchain required ~2.1GB on ESPHome host. GPIO8 strapping pin warning (acceptable for status LED output).
