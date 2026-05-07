# Monitor zbiornika

ESPHome dla modulu DYP A02 podlaczonego po UART.

## Co robi

- mierzy odleglosc od czujnika do lustra cieczy
- wystawia encje w Home Assistant
- pokazuje odczyt na web_server ESPHome
- wspiera OTA

## Pliki

- `device.yaml` - konfiguracja ESPHome
- `PROJECT.md` - specyfikacja projektu
- `hardware.md` - pinout i uwagi montazowe
- `test-log.md` - zapis testow urzadzenia

## Wymagane dane

- `wifi_ssid`
- `wifi_password`
- `monitor_zbiornika_api_key`
- `monitor_zbiornika_ota_password`
- `monitor_zbiornika_ap_password`
