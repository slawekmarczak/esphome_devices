# Monitor zbiornika

  ## Raw User Input

  Tutaj uzytkownik dopisuje luzne wymagania, notatki, pomysly,
  zmiany decyzji, zdjecia/pomiary opisane tekstowo, obserwacje z testow.

  Agent nie kasuje tej sekcji bez wyraznej prosby.

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
  Priorytetem jest stabilny odczyt odleglosci i jego widocznosc w Home Assistant oraz na stronie webowej ESPHome.

  ### Hardware

  ESP32 klasy dev board.
  Czujnik DYP A02 z komunikacja UART w trybie automatycznym.
  Zasilanie modulu zgodnie z jego specyfikacja, ze wspolna masa z ESP32.

  ### Pinout

  - Sensor TX -> ESP32 GPIO5
  - Sensor RX -> ESP32 GPIO17
  - Sensor VCC -> zasilanie zgodne z wersja modulu
  - Sensor GND -> masa wspolna

  ### Funkcje firmware

  - Odczyt odleglosci z DYP A02 przez UART 9600
  - Publikacja odczytu jako sensor ESPHome
  - Web server z widokiem encji
  - API dla Home Assistant
  - OTA do aktualizacji firmware
  - Fallback AP i captive portal na wypadek problemow z Wi-Fi

  ### Encje ESPHome

  - Sensor odleglosci w metrach, widoczny w web_server i Home Assistant

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
  - Na razie nie liczymy poziomu procentowego zbiornika, bo nie znamy wysokosci referencyjnej.
  - Nie dodajemy dodatkowych encji jakosciowych bez potwierdzenia z rzeczywistego outputu czujnika.

  ### Otwarte pytania

  - Czy sensor pracuje w wariancie UART auto zgodnym z ramka A02YYUW.
  - Jaka jest wysokosc zbiornika, jesli ma byc liczony poziom procentowy.
  - Czy potrzebne sa filtry wygładzajace, czy odczyt jest wystarczajaco stabilny.

  ### Historia zmian

  - 2026-05-07: Utworzono spec dla monitora zbiornika i zalozono integracje UART dla DYP A02.
