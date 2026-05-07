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

## Polaczenia

- Sensor TX -> ESP32 GPIO5
- Sensor RX -> ESP32 GPIO17
- Sensor VCC -> zasilanie zgodne z wersja modulu
- Sensor GND -> masa wspolna

## Uwagi

- UART pracuje z predkoscia 9600
- Na starcie warto sprawdzic, czy frame output jest stabilny i czy nie trzeba odwracac linii RX/TX
- GPIO5 to pin strapping — nalezy monitorowac stabilnosc bootowania
