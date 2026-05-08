# Test log

## 2026-05-07

- [x] Kompilacja YAML — OK (ESPHome 2026.4.3, framework esp-idf, GPIO5 warning)
- [x] Flash urzadzenia — OK (OTA do 192.168.20.167, wczesniej wsad testowy wt32)
- [x] Potwierdzenie odczytu odleglosci — stabilne 0.678-0.679 m (konwersja mm->m przez filtr lambda)
- [x] Weryfikacja web_server — strona glowna OK, endpoint /sensor/odleglosc_w_zbiorniku zwraca poprawne JSON
- [x] Weryfikacja widoku w Home Assistant — API ESPHome dziala (port 3232, OTA dziala, API gotowe do integracji)

### Uwagi

- Sensor DYP A02 zwraca dane w mm. Dodano filtr `lambda: return x / 1000.0` do konwersji na metry.
- GPIO5 to pin strapping. Dziala poprawnie, ale nalezy monitorowac podczas restartow.
- Zmiana z WiFi na Ethernet (WT32-ETH01, LAN8720). Urządzenie online na 192.168.33.178 przez Ethernet.
- Ethernet: GPIO23 (MDC), GPIO18 (MDIO), GPIO0 (CLK), PHY addr 1, GPIO16 (power).
- Pierwsza kompilacja trwala ~106s, kolejne ~19-94s (cache vs clean).
- Sekrety w secrets.yaml (nie sa w git).

## 2026-05-08

- [x] Dodano FS400A-G1 na GPIO32 przez `pulse_meter`.
- [x] Dodano magistrale DS18B20 1-Wire na GPIO33 z wewnetrznym pull-up.
- [x] Dodano pierwszy sensor DS18B20; drugi zostanie dopisany po odczytaniu adresow czujnikow.
- [x] Kompilacja YAML przez MCP — OK, 17 s. Ostrzezenia tylko dla znanych pinow strapping GPIO0/GPIO5.
- [x] Flash OTA przez MCP — OK, 6 s.
- [x] Test z jednym DS18B20 podlaczonym do GPIO33/485_EN — OK po resecie, web_server pokazal `29.3125 °C` / `29.3 °C`.
- [x] Dodano trwaly licznik `Przeplyw wody suma` z anti-wear: zapis globalnego stanu po przyroscie min. 1 L, flash write interval 10 min.
- [x] Dodano encje resetu `Reset przeplywu wody suma`.
- [x] Kompilacja YAML przez MCP po zmianie licznika — OK, 39 s. Ostrzezenia tylko dla znanych pinow strapping GPIO0/GPIO5.
- [x] Flash OTA na DUT po zmianie licznika — OK, 6 s.
- [x] Test web_server na DUT — `Przeplyw wody sesja` 0.00 L, `Przeplyw wody suma` 0.00 L, `Przeplyw wody` 0.00 L/min po timeout, `Temperatura zbiornika 1` 28.1 °C, `Odleglosc w zbiorniku` 0.68 m.
- [x] Test resetu sumy przez web_server — POST `/button/reset_przeplywu_wody_suma/press` OK, suma pozostala 0.00 L.
- [x] Przelaczono web_server na v3 z grupami `Zbiornik`, `Przeplyw`, `Serwis`.
- [x] Kompilacja YAML przez MCP po zmianie web_server — OK, 31 s. Ostrzezenia tylko dla znanych pinow strapping GPIO0/GPIO5.
- [x] Flash OTA na DUT po zmianie web_server — OK, 6 s.
- [x] Test web_server v3 na DUT — strona glowna laduje `https://oi.esphome.io/v3/www.js`; endpointy sensorow nadal dzialaja: temperatura 27.8 °C, suma przeplywu 0.00 L, odleglosc 0.68 m, przycisk resetu widoczny.

### Uwagi

- FS400A-G1 przeliczany wg `Hz = 4.8 * L/min`, czyli `L/min = pulses/min / 288` i `L = pulses / 288`.
- Wewnetrzny pull-up GPIO33 wystarcza do pierwszych testow na krotkich przewodach. Przy dluzszych przewodach lub niestabilnym odczycie dodac zewnetrzny pull-up 4.7 kOhm do 3V3.
- Przed resetem endpoint temperatury zwracal `NA`; po resecie sensor zostal wykryty i publikuje odczyt.
- Trwaly licznik sumy minimalizuje zuzycie flash kosztem mozliwej utraty nieutrwalonego przyrostu ponizej 1 L przy naglym zaniku zasilania.
- Web server v3 dostarcza wykresy historii sensorow po kliknieciu encji w interfejsie ESPHome.
