# Device Name
esp-rolety-ogrod-zimowy
## Raw User Input

Na bazie tego repozytorium chcę zrobić urządzenie, które będzie służyło do integracji rolet SOMFI RTS z moim Home Assistantem, na bazie ESPHome lub na bazie tego softu, który jest w repozytorium https://github.com/rstrouse/ESPSomfy-RTS

Bazuje na ESP32-C2 Super Mini A mój frontend radiowy to E07-M1101D Podłączę go do optymalnych PIN-ów, które zaproponujesz na wstępie.

Później będziemy testować. Jeśli coś jest niejasne, dopytaj przed kontynuowaniem finalnej pracy 

i will change HW into supported one, give me list of suppoirted

zacznijmy raz jeszce w uproszczonej formie. mam HW ESP32-S2-Saola-1_V1.2 i E07-M1101D przygotuj co trzeba z repo https://github.com/rstrouse/ESPSomfy-RTS wgraj soft na DUT i przetestuj. jeszcze jest nie podłączony, potrzebuje info o detalach połączeń

## Agent-Maintained Spec

### Goal

Integrate Somfy RTS roller blinds with Home Assistant via
**ESPSomfy-RTS** pre-built firmware on an ESP32 DevKit + E07-M1101D CC1101.

### Hardware

- Board: **ESP32 DevKit V1 (ESP32-WROOM-32)**
- RF: **Ebyte E07-M1101D** (CC1101 @ 433.42 MHz)
- Connection: 8× Dupont F-F wires

### Pinout

| E07-M1101D | Signal | ESP32 GPIO |
|-----------|--------|-----------|
| 1 (GND) | GND | GND |
| 2 (VCC) | 3.3 V | 3V3 |
| 3 (MOSI) | SPI MOSI | GPIO23 |
| 4 (SCK) | SPI SCK | GPIO18 |
| 5 (MISO) | SPI MISO | GPIO19 |
| 6 (GDO2) | RX | GPIO12 |
| 7 (GDO0) | TX | GPIO13 |
| 8 (CSN) | SPI CS | GPIO5 |

### Firmware

- **ESPSomfy-RTS** v2.4.6, pre-built binary
- Binary: `SomfyController.ino.esp32.bin` (1.3 MB, z GitHub Releases)
- Config via web UI (piny, częstotliwość, rolety)

### Flashing

```bash
esptool.py --chip esp32 --port /dev/ttyUSB0 write_flash 0x0 SomfyController.ino.esp32.bin
```

Wejdź w tryb download: BOOT+EN, puść EN, puść BOOT.

### Configuration After Flash

1. AP `ESPSomfy-RTS` → połącz się, http://192.168.4.1
2. Ustaw docelowe WiFi
3. Radio Configuration: SCK=18, MOSI=23, MISO=19, CSN=5, GDO0=13, GDO2=12, Freq=433.42
4. Enable transceiver
5. Shades → Add shade → PROG button to pair
