# Hardware

## Plytka

WT32-ETH01 V1.2 z Ethernetem LAN8720.
ESP32 (bez PSRAM), 4MB Flash.

## Ethernet

LAN8720 (wbudowany):
- MDC: GPIO23
- MDIO: GPIO18
- CLK: GPIO0 (CLK_EXT_IN)
- PHY addr: 1
- Power pin: GPIO16

## Czujnik

DYP A02, UART automatyczny.

## Przeplywomierz

FS400A-G1, wyjscie impulsowe Halla.

Charakterystyka do przeliczenia:
- czestotliwosc impulsow: `Hz = 4.8 * przeplyw[L/min]`
- `1 L = 288 impulsow`

## Temperatura

DS18B20 na magistrali 1-Wire.
Magistrala na GPIO33 obsluguje docelowo dwa czujniki na tych samych trzech przewodach.
W firmware wlaczony jest wewnetrzny pull-up GPIO33.
Zewnetrzny rezystor podciagajacy 4.7 kOhm miedzy DATA/GPIO33 i 3V3 jest nadal zalecany przy dluzszych przewodach lub niestabilnym odczycie.

## Polaczenia

- Sensor TX -> ESP32 GPIO5
- Sensor RX -> ESP32 GPIO17
- Sensor VCC -> zasilanie zgodne z wersja modulu
- Sensor GND -> masa wspolna
- FS400A-G1 yellow/signal -> ESP32 GPIO32
- FS400A-G1 red/VCC -> 5V lub zasilanie zgodne z czujnikiem
- FS400A-G1 black/GND -> masa wspolna
- DS18B20 DATA -> ESP32 GPIO33 z wewnetrznym pull-up
- DS18B20 VDD -> ESP32 3V3
- DS18B20 GND -> masa wspolna

## Uwagi

- UART pracuje z predkoscia 9600
- Na starcie warto sprawdzic, czy frame output jest stabilny i czy nie trzeba odwracac linii RX/TX
- GPIO5 to pin strapping — nalezy monitorowac stabilnosc bootowania
- GPIO32 wybrany dla FS400A-G1, bo nie koliduje z Ethernetem LAN8720, UART DYP A02 ani pinami strapping.
- GPIO33 wybrany dla DS18B20, bo jest wolnym pinem wejscia/wyjscia i nadaje sie na magistrale 1-Wire.
- Jesli FS400A-G1 jest zasilany z 5V i jego wyjscie nie jest otwartym kolektorem z pull-up do 3V3, trzeba zastosowac dzielnik lub konwerter poziomow przed GPIO32.
- Drugi DS18B20 nalezy dodac do YAML po odczytaniu adresow czujnikow z logow ESPHome albo po potwierdzeniu stabilnego mapowania indeksow.
