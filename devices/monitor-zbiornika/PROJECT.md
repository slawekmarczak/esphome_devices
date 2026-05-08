# Monitor zbiornika

  ## Raw User Input

  Tutaj uzytkownik dopisuje luzne wymagania, notatki, pomysly,
  zmiany decyzji, zdjecia/pomiary opisane tekstowo, obserwacje z testow.

  Agent nie kasuje tej sekcji bez wyraznej prosby.



Teraz robimy:
1. Dodaj obsługe przepływomierza FS400A G1 - dokumnetacja tutaj https://www.mantech.co.za/datasheets/products/FS400A-G1.pdf?srsltid=AfmBOoq0SgIM_mBQgy1jwUvrI6Lbp-gfcGNTOo2EjXBvaCKfmXy3srTY
   Wybierz najodpowiedniejszy pin, na razie będizemy testować na sucho bo przeprlywomierz mam gdzieś indziej potem podłącze na finalne testy.
2. Dodaj obsługe dwóch czujników temperatury DS18B20 - zaproponuj gdzie go podłączyć, na początek będize podłaczony jeden










TO już  mamy zrobione:

Uzywamy modulu DYP A02 pracujacego po UART.
Modul jest podlaczony do pinow RX/TX, odpowiednio GPIO5 i GPIO17.


https://gist.github.com/roypeter/1e2edde11bd346078c234aaf22d18b3b
https://community.home-assistant.io/t/esp32-dyp-a02-ultrasonic-configuration/601051
https://www.dypcn.com/uploads/A02-Output-Interfaces.pdf
https://www.dypcn.com/uploads/A02-Datasheet.pdf


Wystawiona strona webowa ma zawierac wskazania miernika, czyli pomiar odleglosci.
Jesli modul udostepnia dodatkowe dane przydatne do oceny jakosci pomiaru, to warto je dodac.
Wymagane sa tez integracja z Home Assistant przez API oraz zdalne aktualizacje OTA.


  ## Agent-Maintained Spec

  Ta sekcja jest utrzymywana przez agenta.

  ### Cel

  Monitor poziomu zbiornika na podstawie odleglosci mierzonej przez ultradzwiekowy modul DYP A02.
  Priorytetem jest stabilny odczyt odleglosci, przeplywu wody i temperatury oraz ich widocznosc w Home Assistant i na stronie webowej ESPHome.

  ### Hardware

  WT32-ETH01 (LAN8720 Ethernet PHY).
  Czujnik DYP A02 z komunikacja UART w trybie automatycznym.
  Przeplywomierz FS400A-G1 z wyjsciem impulsowym Halla.
  Do dwoch czujnikow temperatury DS18B20 na wspolnej magistrali 1-Wire.
  Zasilanie modulu zgodnie z jego specyfikacja, ze wspolna masa z ESP32.

  ### Pinout

  - Sensor TX -> ESP32 GPIO5
  - Sensor RX -> ESP32 GPIO17
  - Sensor VCC -> zasilanie zgodne z wersja modulu
  - Sensor GND -> masa wspolna
  - FS400A-G1 Signal (zolty) -> ESP32 GPIO32
  - FS400A-G1 VCC (czerwony) -> 5V lub zasilanie zgodne z czujnikiem
  - FS400A-G1 GND (czarny) -> masa wspolna
  - DS18B20 DATA -> ESP32 GPIO33 / pin `485_EN` z wlaczonym wewnetrznym pull-up
  - DS18B20 VDD -> 3V3
  - DS18B20 GND -> masa wspolna
  - Zalecany dodatkowy rezystor podciagajacy 4.7 kOhm miedzy GPIO33/485_EN/DATA i 3V3 przy dluzszych przewodach lub niestabilnym odczycie

  Ethernet LAN8720 (wbudowany w WT32-ETH01):
  - MDC: GPIO23, MDIO: GPIO18, CLK: GPIO0, PHY addr: 1, Power: GPIO16

  ### Funkcje firmware

  - Odczyt odleglosci z DYP A02 przez UART 9600
  - Odczyt przeplywu z FS400A-G1 przez licznik impulsow
  - Odczyt temperatury z DS18B20 przez 1-Wire
  - Publikacja odczytu jako sensor ESPHome
  - Web server z widokiem encji
  - API dla Home Assistant
  - OTA do aktualizacji firmware
  - Ethernet jako domyślny interfejs sieciowy (LAN8720)

  ### Encje ESPHome

  - Sensor odleglosci w metrach, widoczny w web_server i Home Assistant
  - Sensor przeplywu w L/min, widoczny w web_server i Home Assistant
  - Sensor sumarycznego przeplywu w litrach, widoczny w web_server i Home Assistant
  - Sensor temperatury DS18B20 nr 1, widoczny w web_server i Home Assistant
  - Sensor temperatury DS18B20 nr 2, docelowo po podaniu adresu lub potwierdzeniu indeksu na magistrali 1-Wire

  ### Zachowanie po restarcie

  Po restarcie ESP32 ponownie inicjalizuje UART i czeka na poprawne ramki z czujnika.
  Brak danych przez dluzszy czas powoduje, ze encja staje sie niewazna po czasie wygaszenia.

  ### OTA / flashowanie

  Pierwszy flash przez kabel. Nastepnie aktualizacje przez OTA ESPHome.
  Dostep do OTA zabezpieczony haslem z secrets.

  ### Test plan

  - Kompilacja YAML
  - Flash firmware
  - Weryfikacja, czy sensor publikuje stabilne wartosci
  - Sprawdzenie widoku w web_server
  - Porownanie odczytu z rzeczywista odlegloscia

  ### Decyzje projektowe

  - Uzywamy wbudowanego komponentu ESPHome dla A02YYUW, bo pasuje do automatycznego UART i nie wymaga lokalnego parsera C++ na start.
  - FS400A-G1 podlaczamy do GPIO32, bo jest wolnym pinem wejscia/wyjscia na ESP32 i nie koliduje z LAN8720, UART DYP A02 ani pinami strapping.
  - Przeplyw FS400A-G1 liczymy z charakterystyki `Hz = 4.8 * Q[L/min]`, czyli `Q = pulses_per_min / 288`.
  - Sume przeplywu liczymy jako `litry = impulsy / 288`.
  - DS18B20 podlaczamy do GPIO33, opisanego na WT32-ETH01 jako `485_EN`, jako wspolna magistrala 1-Wire dla dwoch czujnikow, z wlaczonym wewnetrznym pull-up; zewnetrzny pull-up 4.7 kOhm do 3V3 pozostaje zalecany dla dluzszych przewodow.
  - Pierwszy DS18B20 bedzie uruchomiony bez adresu; drugi zostanie dodany jako stabilna encja po odczytaniu adresow z logow albo po podlaczeniu obu czujnikow.
  - Na razie nie liczymy poziomu procentowego zbiornika, bo nie znamy wysokosci referencyjnej.
  - Nie dodajemy dodatkowych encji jakosciowych bez potwierdzenia z rzeczywistego outputu czujnika.

  ### Otwarte pytania

  - ~~Czy sensor pracuje w wariancie UART auto zgodnym z ramka A02YYUW.~~ TAK — odczyty poprawne, dane w mm.
  - Jaka jest wysokosc zbiornika, jesli ma byc liczony poziom procentowy.
  - ~~Czy potrzebne sa filtry wygładzajace, czy odczyt jest wystarczajaco stabilny.~~ Stabilny ~0.679 m, filtr lambda mm->m wystarcza.
  - Adresy dwoch DS18B20 do wpisania po pierwszym uruchomieniu z czujnikami na magistrali.
  - Czy sygnal FS400A-G1 jest faktycznie typu otwarty kolektor z pull-up do 3V3; jesli wyjscie jest aktywnie podciagane do 5V, wymagany jest dzielnik lub konwerter poziomow.

  ### Historia zmian

  - 2026-05-07: Utworzono spec dla monitora zbiornika i zalozono integracje UART dla DYP A02.
  - Kompilacja + OTA OK. Dodano filtr `lambda: return x / 1000.0` do konwersji mm->m. Odczyty stabilne.
  - 2026-05-07: Zmiana z WiFi na Ethernet (WT32-ETH01, LAN8720). Usunięto wifi, captive_portal. Ethernet jako jedyny interfejs. Flash przez OTA z WiFi → Ethernet, urządzenie online na 192.168.33.178.
  - 2026-05-08: Zaplanowano FS400A-G1 na GPIO32 i DS18B20 1-Wire na GPIO33.
