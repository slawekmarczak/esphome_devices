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
