# Device Name
esp-rolety-ogrod-zimowy
## Raw User Input

Na bazie tego repozytorium chcę zrobić urządzenie, które będzie służyło do integracji rolet SOMFI RTS z moim Home Assistantem, na bazie ESPHome lub na bazie tego softu, który jest w repozytorium https://github.com/rstrouse/ESPSomfy-RTS

Bazuje na ESP32-C2 Super Mini A mój frontend radiowy to E07-M1101D Podłączę go do optymalnych PIN-ów, które zaproponujesz na wstępie.

Później będziemy testować. Jeśli coś jest niejasne, dopytaj przed kontynuowaniem finalnej pracy 

i will change HW into supported one, give me list of suppoirted

## Agent-Maintained Spec

### Goal

Integrate Somfy RTS roller blinds (winter garden) with Home Assistant using
a standard ESP32 DevKit (ESP32-WROOM-32) and an E07-M1101D (CC1101) 433.42 MHz
transceiver, running **ESPSomfy-RTS** pre-built firmware.

### Decisions

- **ESP32-C2 → ESP32 DevKit**: ESP32-C2 (RISC-V) is not supported by
  ESPSomfy-RTS. Standard ESP32 DevKit (Xtensa) is the primary target with
  pre-built binaries.
- **ESPSomfy-RTS, not ESPHome**: ESPSomfy-RTS is a proven, feature-complete
  firmware for Somfy RTS blinds. It handles CC1101, protocol timing, rolling
  codes, pairing, position tracking, web UI, MQTT, and has a dedicated Home
  Assistant integration (HACS). Using the pre-built binary — no compilation
  required.
- **WiFi only**: Standard ESP32 DevKit — no Ethernet.
- **Arduino framework**: Default for standard ESP32.

### Hardware

- Module/board: ESP32 DevKit V1 (ESP32-WROOM-32, Xtensa dual-core 240 MHz)
- Flash: 4 MB
- Power: micro-USB 5 V
- Network: WiFi 802.11b/g/n (2.4 GHz)
- RF: Ebyte E07-M1101D CC1101 433.42 MHz transceiver (SPI + GDO0/GDO2)

### Pinout

ESPSomfy-RTS recommended wiring for E07-M1101D on ESP32 DevKit:

| Signal | ESP32 GPIO | E07-M1101D Pin |
|--------|-----------|----------------|
| GND | GND | 1 (GND) |
| 3.3 V | 3V3 | 2 (VCC) |
| SPI SCK | GPIO18 | 4 (SCK) |
| SPI MOSI | GPIO23 | 3 (MOSI/SI) |
| SPI MISO | GPIO19 | 5 (MISO/SO) |
| SPI CS | GPIO5 | 8 (CSN) |
| CC1101 GDO0 (TX) | GPIO13 | 7 (GDO0) |
| CC1101 GDO2 (RX) | GPIO12 | 6 (GDO2) |

### Firmware

ESPSomfy-RTS from https://github.com/rstrouse/ESPSomfy-RTS
- Pre-built binary: `SomfyController.ino.esp32.bin` (downloaded from releases)
- Supports up to 32 shades, 16 groups
- Web interface at http://<device-ip>
- Home Assistant integration via HACS: `ESPSomfy-RTS HA`

### Flashing

1. Download binary:
   ```
   wget https://github.com/rstrouse/ESPSomfy-RTS/releases/latest/download/SomfyController.ino.esp32.bin
   ```
2. Enter download mode: hold BOOT, press EN, release EN, release BOOT
3. Flash via esptool:
   ```
   esptool.py --chip esp32 --port /dev/ttyUSB0 write_flash 0x0 SomfyController.ino.esp32.bin
   ```
4. Reboot. Device creates WiFi AP `ESPSomfy-RTS`.
5. Connect to AP, configure target WiFi network.
6. Open web interface, pair blinds.
7. OTA updates built into ESPSomfy-RTS web interface.

### Validation Plan

- Flash ESPSomfy-RTS firmware via USB-UART.
- Verify WiFi AP appears.
- Configure WiFi and verify web interface.
- Pair one blind, test open/close/stop/my.
- Verify original Telis remote still works.
- Record results in `test-log.md`.

### Open Questions

- How many blinds to configure initially?
- Flash method: first flash via USB-UART (local) or via flashpoint/RPI?
