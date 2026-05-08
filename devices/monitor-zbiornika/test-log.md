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
- [x] Zastapiono komponent `a02yyuw` wlasnym parserem UART ramek DYP A02, zeby poprawne ramki ponizej 30 mm publikowaly `0.00 m` zamiast warningu `Invalid data read from sensor`.
- [x] Kompilacja YAML przez MCP po zmianie parsera DYP A02 — OK, 94 s. Ostrzezenia tylko dla znanych pinow strapping GPIO0/GPIO5.
- [x] Flash OTA na DUT po zmianie parsera DYP A02 — OK, 6 s.
- [x] Test web_server na DUT po zmianie parsera — odleglosc publikuje 0.643-0.644 m, temperatura 26.7 °C, suma przeplywu 0.00 L.
- [x] Test logow UART przez MCP po zmianie parsera — brak linii i brak bledow w 25 s odczytu; warning `a02yyuw.sensor` nie wystapil w odczycie MCP.
- [x] Zdiagnozowano cykliczne restarty co ok. 15 min z logiem `[api:127] No clients; rebooting` / `[app:247] Forcing a reboot` jako domyslny watchdog Native API, a nie crash/brownout.
- [x] Ustawiono `api.reboot_timeout: 0s`, zeby brak klienta Home Assistant/API nie wymuszal restartu urzadzenia.
- [x] Kompilacja YAML przez MCP po zmianie API reboot timeout — OK, 18 s. Ostrzezenia tylko dla znanych pinow strapping GPIO0/GPIO5.
- [x] Flash OTA na DUT po zmianie API reboot timeout — OK, 6 s.
- [x] Szybki test po OTA — web_server v3 odpowiada, odleglosc 0.65 m, temperatura 26.8 °C, suma przeplywu 0.00 L.

### Uwagi

- FS400A-G1 przeliczany wg `Hz = 4.8 * L/min`, czyli `L/min = pulses/min / 288` i `L = pulses / 288`.
- Wewnetrzny pull-up GPIO33 wystarcza do pierwszych testow na krotkich przewodach. Przy dluzszych przewodach lub niestabilnym odczycie dodac zewnetrzny pull-up 4.7 kOhm do 3V3.
- Przed resetem endpoint temperatury zwracal `NA`; po resecie sensor zostal wykryty i publikuje odczyt.
- Trwaly licznik sumy minimalizuje zuzycie flash kosztem mozliwej utraty nieutrwalonego przyrostu ponizej 1 L przy naglym zaniku zasilania.
- Web server v3 dostarcza wykresy historii sensorow po kliknieciu encji w interfejsie ESPHome.
- Bliski zakres DYP A02 `<=30 mm` jest teraz mapowany na `0.00 m`. Test z fizycznym dystansem 0-3 cm wymaga ustawienia przeszkody przy czujniku; zdalnie potwierdzono normalny odczyt i brak komponentu `a02yyuw` w firmware.
- Brak klienta Native API nie powinien juz restartowac urzadzenia. Pelne potwierdzenie runtime wymaga obserwacji ponad 15 minut po OTA.
