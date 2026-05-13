# Device Name
esp-rolety-ogrod-zimowy
## Raw User Input

Append user requirements here. This section is append-only unless the user
explicitly asks for edits.

Na bazie tego repozytorium chcę zrobić urządzenie, które będzie służyło do integracji rolet SOMFI RTS z moim Home Assistantem, na bazie ESPHome lub na bazie tego softu, który jest w repozytorium https://github.com/rstrouse/ESPSomfy-RTS

Bazuje na ESP32-C2 Super Mini A mój frontend radiowy to E07-M1101D Podłączę go do optymalnych PIN-ów, które zaproponujesz na wstępie.

Później będziemy testować. Jeśli coś jest niejasne, dopytaj przed kontynuowaniem finalnej pracy 


## Agent-Maintained Spec

### Goal

Integrate Somfy RTS roller blinds (winter garden) with Home Assistant using
an ESP32-C2 Super Mini A and an E07-M1101D (CC1101) 433.42 MHz transceiver,
running ESPHome firmware.

### Decisions

- **ESPHome over ESPSomfy-RTS**: ESPSomfy-RTS firmware only supports ESP32 and
  ESP32-S3. The ESP32-C2 is not supported. ESPHome supports ESP32-C2 via the
  esp-idf framework, so we implement the Somfy RTS protocol as a custom
  ESPHome component.
- **esp-idf framework**: Required — Arduino does not support the ESP32-C2.
- **WiFi only**: ESP32-C2 has no Ethernet PHY; connectivity is WiFi 802.11b/g/n.
- **SPI bus on FSPI defaults**: SCK=GPIO6, MOSI=GPIO7, MISO=GPIO2, CS=GPIO10.
  These are the hardware SPI2 (FSPI) default pins, giving best performance.
- **CC1101 GDO0→GPIO4 (TX), GDO2→GPIO5 (RX)**: Safe GPIOs that avoid all
  strapping pins (GPIO0, GPIO8, GPIO9).

### Hardware

- Module/board: ESP32-C2 Super Mini A (ESP8684, RISC-V, 4 MB flash, WiFi+BLE5)
- Power: USB-C (5 V), onboard 3.3 V LDO
- Network: WiFi 802.11b/g/n (2.4 GHz)
- Peripherals: E07-M1101D CC1101 433 MHz transceiver (SPI + GDO0/GDO2)

### Pinout

| Signal | GPIO | E07-M1101D Pin | Notes |
|--------|------|----------------|-------|
| SPI SCK | GPIO6 | 5 (SCK) | FSPI default |
| SPI MOSI | GPIO7 | 3 (MOSI/SI) | FSPI default |
| SPI MISO | GPIO2 | 5 (MISO/SO) | FSPI default |
| SPI CS | GPIO10 | 8 (CSN) | FSPI default |
| CC1101 GDO0 | GPIO4 | 7 (GDO0) | TX data pin |
| CC1101 GDO2 | GPIO5 | 6 (GDO2) | RX data pin |
| VCC 3.3 V | 3V3 | 2 (VCC) | From LDO |
| GND | GND | 1 (GND) | Common ground |

### Firmware Behavior

- Connect to WiFi, expose Home Assistant Native API and web server.
- Drive CC1101 over SPI at 433.42 MHz with OOK modulation.
- Implement Somfy RTS protocol (manchester-encoded 56-bit frames, rolling codes
  stored in NVS via ESPHome preferences).
- Expose each paired blind as an ESPHome `cover` entity (open/close/stop/position).
- Support pairing (PROG command) via a service or button entity.

### ESPHome Entities

- `cover.roleta_1` … `cover.roleta_N` — Somfy RTS cover per blind
- `button.paruj_rolete_N` — Trigger PROG pairing for blind N
- `sensor.cc1101_rssi` — RSSI from last received frame (diagnostic)
- `text_sensor.wifi_ip` — Device IP address (diagnostic)

### Flashing And Update Path

- Compile through `~/esphome_agent`.
- First flash via UART through flashpoint.
- Prefer OTA once networking works.

### Validation Plan

- Compile via MCP.
- Flash via UART (first) or OTA (subsequent).
- Reset via MCP.
- Read UART logs — verify WiFi connection and SPI init.
- Check HTTP/web and Native API.
- Record results in `test-log.md`.

### Open Questions

- How many blinds to configure? (Starting with 1, user can expand.)
- What is the target WiFi SSID/password? (Will use `!secret` references.)
