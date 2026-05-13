# Device Hardware

## Module

ESP32-C2 Super Mini A (ESP8684)
- RISC-V single-core 120 MHz
- 272 KB SRAM, 576 KB ROM
- 4 MB flash (quad SPI)
- WiFi 802.11b/g/n + BLE 5
- USB-C connector, onboard 3.3 V LDO
- Onboard LED on GPIO8

## RF Transceiver

Ebyte E07-M1101D (CC1101-based)
- Frequency: 433.42 MHz (Somfy RTS) — programmable via SPI
- Modulation: OOK/ASK (for Somfy RTS)
- Interface: SPI (MOSI, MISO, SCK, CSN) + GDO0 (TX) + GDO2 (RX)
- Supply: 3.3 V
- Antenna: SMA or spring (check variant)

## Wiring

| E07-M1101D Pin | Signal | ESP32-C2 GPIO | Notes |
|----------------|--------|---------------|-------|
| 1 (GND) | GND | GND | Common ground |
| 2 (VCC) | 3.3 V | 3V3 | From onboard LDO |
| 3 (MOSI/SI) | SPI MOSI | GPIO7 | FSPI default |
| 4 (SCK) | SPI SCK | GPIO6 | FSPI default |
| 5 (MISO/SO) | SPI MISO | GPIO2 | FSPI default |
| 6 (GDO2) | RX data | GPIO5 | Demodulated RX output |
| 7 (GDO0) | TX data | GPIO4 | Modulation TX input |
| 8 (CSN) | SPI CS | GPIO10 | FSPI default |

## Strapping Pin Notes

- GPIO0: Strapping (SPI boot). Left unconnected / not used for CC1101.
- GPIO8: Strapping (SPI boot mode, internal pullup). Onboard LED. Not used for CC1101.
- GPIO9: Strapping (boot button). Not used for CC1101.

## Flashpoint Connection

| Signal | ESP32-C2 Pin | Flashpoint |
|--------|--------------|------------|
| UART TX | GPIO20 (RXD0) | Read via USB-C |
| UART RX | GPIO20 | Read via USB-C |
| Boot | GPIO9 (BOOT) | Hold LOW + reset to enter download mode |
| Reset | EN | Pull LOW to reset |
| Power | USB-C 5 V | USB cable |

The ESP32-C2 Super Mini has a built-in USB-to-UART bridge. Flash via USB-C
cable; hold BOOT button during reset to enter download mode.
