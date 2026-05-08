# Monitor zbiornika

ESPHome dla modulu DYP A02 podlaczonego po UART.

## Co robi

- mierzy odleglosc od czujnika do lustra cieczy
- mierzy przeplyw wody z FS400A-G1
- utrzymuje trwaly licznik sumarycznego przeplywu z ograniczonym zapisem flash
- udostepnia przycisk resetu sumarycznego przeplywu
- mierzy temperature z DS18B20 na magistrali 1-Wire
- wystawia encje w Home Assistant
- pokazuje odczyt na web_server ESPHome
- wspiera OTA

## Pliki

- `device.yaml` - konfiguracja ESPHome
- `PROJECT.md` - specyfikacja projektu
- `hardware.md` - pinout i uwagi montazowe
- `test-log.md` - zapis testow urzadzenia

## Wymagane dane

- `monitor_zbiornika_api_key`
- `monitor_zbiornika_ota_password`

## Pinout dodatkowych czujnikow

- FS400A-G1 signal -> GPIO32
- DS18B20 DATA -> GPIO33 / pin `485_EN` z wewnetrznym pull-up; zewnetrzny 4.7 kOhm do 3V3 zalecany przy dluzszych przewodach
