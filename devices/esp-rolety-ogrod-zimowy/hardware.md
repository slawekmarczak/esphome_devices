# Device Hardware

## Module

ESP32 DevKit V1 (ESP32-WROOM-32)
- Xtensa dual-core LX6 @ 240 MHz
- 520 KB SRAM, 448 KB ROM
- 4 MB flash (quad SPI)
- WiFi 802.11b/g/n + Bluetooth 4.2 / BLE
- micro-USB connector (CH340G USB-UART)
- Onboard LED on GPIO2
- BOOT button on GPIO0, EN button for reset

## RF Transceiver

Ebyte E07-M1101D (CC1101-based)
- Frequency: 433.42 MHz (Somfy RTS) — programmable via SPI
- Modulation: OOK/ASK (for Somfy RTS)
- Interface: SPI (MOSI, MISO, SCK, CSN) + GDO0 (TX) + GDO2 (RX)
- Supply: 3.3 V
- Antenna: SMA or spring (check variant)

## Wiring

ESPSomfy-RTS recommended pin mapping for E07-M1101D:

| E07-M1101D Pin | Signal | ESP32 GPIO | Notes |
|----------------|--------|------------|-------|
| 1 (GND) | GND | GND | Common ground |
| 2 (VCC) | 3.3 V | 3V3 | From onboard LDO |
| 3 (MOSI/SI) | SPI MOSI | GPIO23 | VSPI default |
| 4 (SCK) | SPI SCK | GPIO18 | VSPI default |
| 5 (MISO/SO) | SPI MISO | GPIO19 | VSPI default |
| 6 (GDO2) | RX data | GPIO12 | Demodulated RX output |
| 7 (GDO0) | TX data | GPIO13 | Modulation TX input |
| 8 (CSN) | SPI CS | GPIO5 | Chip select |

## Strapping Pin Notes

- GPIO0: Must be LOW at boot to enter download mode. BOOT button handles this.
- GPIO2: Onboard LED. LOW at boot is normal.
- GPIO12: Strapping (MTDI - flash voltage). Internal pull-down is OK.
- GPIO5: Outputs PWM at boot (not a strapping issue for CC1101 CS).

## Flash/Update Procedure

### First Flash (USB-UART)

1. Connect ESP32 DevKit to computer via micro-USB cable.
2. Hold BOOT button, press EN, release EN, release BOOT.
3. Verify device in download mode: `ls /dev/ttyUSB0` (Linux) or check Device Manager.
4. Download latest `SomfyController.ino.esp32.bin` from
   https://github.com/rstrouse/ESPSomfy-RTS/releases
5. Flash via esptool:
   ```
   esptool.py --chip esp32 --port /dev/ttyUSB0 write_flash 0x0 SomfyController.ino.esp32.bin
   ```

### OTA Update

ESPSomfy-RTS has built-in OTA. After first flash:
1. Open the device web interface (http://<device-ip>).
2. Navigate to Firmware → Update.
3. Upload the latest pre-built binary.

### Flashpoint Connection

| Signal | ESP32 Pin | Flashpoint |
|--------|-----------|------------|
| UART TX | GPIO1 (TXD0) | Read via USB-UART |
| UART RX | GPIO3 (RXD0) | Read via USB-UART |
| Boot | GPIO0 (BOOT) | Hold LOW + reset to enter download mode |
| Reset | EN | Pull LOW to reset |
| Power | USB 5 V | USB cable |

The ESP32 DevKit has a built-in CH340G USB-to-UART bridge. Flash via micro-USB
cable; hold BOOT button during reset to enter download mode.
