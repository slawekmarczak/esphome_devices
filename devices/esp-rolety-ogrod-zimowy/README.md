# Rolety Ogrod Zimowy

Sterownik Somfy RTS dla rolet w ogrodzie zimowym.

## Sprzęt

- ESP32 DevKit V1 (ESP32-WROOM-32)
- Ebyte E07-M1101D (transceiver CC1101 433 MHz)
- Połączenie przez przewody Dupont (8 pinów)

## Okablowanie

Szczegółowa tabela pinów w `hardware.md`.

## Firmware

[ESPSomfy-RTS](https://github.com/rstrouse/ESPSomfy-RTS) — gotowy firmware
dla ESP32, obsługa do 32 rolet, interfejs webowy, MQTT, integracja z
Home Assistant (HACS).

### Pierwsze wgranie

```bash
# Pobierz binarkę
wget https://github.com/rstrouse/ESPSomfy-RTS/releases/latest/download/SomfyController.ino.esp32.bin

# Wgraj przez USB (ESP32 w trybie download: przytrzymaj BOOT, naciśnij EN, puść EN, puść BOOT)
esptool.py --chip esp32 --port /dev/ttyUSB0 write_flash 0x0 SomfyController.ino.esp32.bin
```

### Konfiguracja

Po wgraniu firmware, urządzenie utworzy sieć WiFi `ESPSomfy-RTS`.
Połącz się, skonfiguruj docelową sieć WiFi i sparuj rolety przez panel webowy.

## Integracja z Home Assistant

[ESPSomfy-RTS HA Integration](https://github.com/rstrouse/ESPSomfy-RTS-HA) —
dostępna przez HACS.
