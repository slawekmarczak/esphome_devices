# Hardware

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
- Piny 16/17 nie sa tu wykorzystywane, poza GPIO17 jako TX ESP32
