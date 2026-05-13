# Device Hardware

## Module

ESP32 DevKit V1 (ESP32-WROOM-32)
- Xtensa dual-core LX6 @ 240 MHz
- 520 KB SRAM
- 4 MB flash
- WiFi 802.11b/g/n
- micro-USB (CH340G USB-UART)
- BOOT button on GPIO0, EN button for reset
- Onboard LED on GPIO2

## RF Transceiver

Ebyte E07-M1101D (CC1101-based)
- Frequency: 433.42 MHz (Somfy RTS)
- Modulation: OOK/ASK
- Interface: SPI + GDO0 + GDO2
- Supply: 3.3 V
- SMA or spring antenna

## Wiring

ESPSomfy-RTS recommended pin mapping for E07-M1101D on ESP32 DevKit:

```
E07-M1101D          ESP32 DevKit
+--------+          +-----------+
| 1 GND  |--------- | GND       |
| 2 VCC  |--------- | 3V3       |
| 3 MOSI |--------- | GPIO23    |
| 4 SCK  |--------- | GPIO18    |
| 5 MISO |--------- | GPIO19    |
| 6 GDO2 |--------- | GPIO12    |
| 7 GDO0 |--------- | GPIO13    |
| 8 CSN  |--------- | GPIO5     |
+--------+          +-----------+
```

Użyj przewodów Dupont F-F (8 sztuk).

## First Flash (USB-UART)

1. Podłącz ESP32 DevKit do komputera przez micro-USB.
2. Wejdź w tryb download: przytrzymaj BOOT, naciśnij EN, puść EN, puść BOOT.
3. Sprawdź port: `ls /dev/ttyUSB*` (Linux) lub Device Manager (Windows).
4. Wgraj firmware:

```bash
esptool.py --chip esp32 --port /dev/ttyUSB0 write_flash 0x0 SomfyController.ino.esp32.bin
```

5. Po wgraniu naciśnij EN (reset). Urządzenie wystawi AP WiFi `ESPSomfy-RTS`.
6. Połącz się z AP, otwórz http://192.168.4.1, skonfiguruj docelową sieć WiFi.
7. W panelu webowym przejdź do Radio Configuration i ustaw piny:
   - SCK: 18, MOSI: 23, MISO: 19, CSN: 5, GDO0: 13, GDO2: 12
   - Frequency: 433.42
   - Włącz transceiver (Enable)
8. Sparuj rolety przez zakładkę Shades.

## Strapping Pins

- GPIO0: LOW przy boot → download mode. Obsługiwany przyciskiem BOOT.
- GPIO2: Onboard LED. Normalny stan LOW.
- GPIO12: MTDI (flash voltage). Wewnętrzny pull-down — OK.
- GPIO5: Wysyła PWM przy boot, nie przeszkadza dla CS.
