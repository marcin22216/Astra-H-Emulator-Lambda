# Modularny System Tuningu - OPEL Astra H (2005)

## 📋 Informacje o projekcie

Rozbudowany, modularny projekt systemu tuningu dla **OPEL Astra H** rok 2005, wersja kombi z silnikiem 1.8 benzyna i automatyczną skrzynią biegów 4-biegową.

### Cel projektu
Stworzenie uniwersalnej platformy ESP32 umożliwiającej:
- ✅ **Moduł 1 (v1.0)**: Emulację drugiej sondy lambda (post-cat) - eliminacja błędów P0420/P0430
- 🔜 **Moduł 2 (v2.0)**: Ręczne sterowanie trybami pracy skrzyni automatycznej (tiptronic emulation)
- 🔜 **Moduł 3 (v3.0)**: Łopatki zmiany biegów przy kierownicy (paddle shifters)
- 🔜 **Moduł 4 (przyszłość)**: Inne rozszerzenia (boost control, launch control, itp.)

### Architektura modularna
Projekt wykorzystuje wzorzec modułowy umożliwiający łatwe dodawanie nowych funkcji bez modyfikacji istniejącego kodu.

## 🔧 Specyfikacja techniczna

### Pojazd
- **Model**: OPEL Astra H Kombi
- **Rok**: 2005
- **Silnik**: 1.8 benzyna (Z18XE lub Z18XER)
- **Skrzynia biegów**: Automatyczna 4-biegowa
- **System sterowania**: Bosch ME7.6.2 lub podobny

### Platforma sprzętowa
- **Mikrokontroler**: ESP32-DevKitC V4 (rekomendowany) lub ESP32-WROOM-32
- **Czujnik**: Wbudowana emulacja oparta na sygnale pierwszej sondy
- **Napięcie pracy**: 12V (z regulatorem na 3.3V dla ESP32)

## 🎯 Zasada działania

### Charakterystyka sondy lambda
1. **Pierwsza sonda** (pre-cat): Oscyluje między 0.1V-0.9V, szybkie przełączanie (lean/rich)
2. **Druga sonda** (post-cat): Powinna być stabilniejsza, około 0.45V-0.65V (sprawny katalizator wygładza sygnał)

### Algorytm emulacji - WERSJA ZAAWANSOWANA (RPM-based)
Emulator:
1. **Odczytuje obroty silnika (RPM)** z sygnału OBD-II lub bezpośrednio z ECU
2. **Odczytuje sygnał z pierwszej sondy lambda** (ADC ESP32)
3. **Wykrywa tryb pracy**: Benzyna vs LPG (samochód ma instalację gazową)
4. **Stosuje algorytm adaptacyjny wygładzania**:
   - Na biegu jałowym (700-900 RPM): Maksymalne wygładzanie
   - Przy przyspieszaniu (1500-4000 RPM): Średnie wygładzanie
   - Przy wysokich obrotach (4000+ RPM): Minimalne wygładzanie
5. **Kompensuje opóźnienie katalityczne** proporcjonalnie do RPM
6. **Generuje sygnał dla ECU** symulujący sprawny katalizator (wyjście DAC → RC filtr)
7. **Dostosowuje parametry w czasie rzeczywistym** bazując na:
   - Aktualnych obrotach
   - Trybie pracy (benzyna/LPG)
   - Historii sygnału (learning mode)

## 📊 Parametry kalibracji

### Podstawowe wartości (do dostrojenia)
```cpp
// Napięcia referencyjne sondy lambda
const float LAMBDA_MIN_VOLTAGE = 0.1;    // Lean
const float LAMBDA_MAX_VOLTAGE = 0.9;    // Rich
const float LAMBDA_STOICH = 0.45;        // Stoichiometric

// Parametry filtracji adaptacyjnej (zależne od RPM)
struct FilterParams {
    int rpm_min;          // Dolna granica RPM
    int rpm_max;          // Górna granica RPM
    float smoothing;      // Współczynnik wygładzania
    float amplitude_red;  // Redukcja amplitudy
    int delay_ms;         // Opóźnienie odpowiedzi
};

// Profile dla różnych zakresów obrotów
FilterParams IDLE_MODE =     {0,    1000, 0.25, 0.50, 150};  // Bieg jałowy
FilterParams CRUISE_MODE =   {1000, 2500, 0.18, 0.40, 100};  // Jazda miejska
FilterParams ACCEL_MODE =    {2500, 4000, 0.12, 0.35, 80};   // Przyspieszanie
FilterParams SPORT_MODE =    {4000, 7000, 0.08, 0.30, 60};   // Wysokie obroty

// Kompensacja dla LPG
const float LPG_VOLTAGE_OFFSET = 0.03;   // LPG jest uboższy → wyższe napięcie bazowe
const float LPG_SMOOTHING_BOOST = 1.15;  // LPG wymaga więcej wygładzania

// Korekcja offset (dla drugiej sondy)
const float VOLTAGE_OFFSET_PETROL = 0.50;  // Benzyna: docelowe napięcie bazowe
const float VOLTAGE_OFFSET_LPG = 0.53;     // LPG: docelowe napięcie bazowe
```

### Źródła sygnału RPM
Emulator może odczytywać obroty z trzech źródeł (priorytet od najlepszego):

1. **OBD-II przez CAN Bus** (najdokładniejsze):
   - Wymagany moduł: MCP2515 CAN Bus module
   - PID: 0x0C (Engine RPM)
   - Odświeżanie: ~100ms
   
2. **Impuls z cewki zapłonowej** (bezpośrednie):
   - Sygnał: 4 impulsy na obrót (silnik 4-cylindrowy)
   - Wymagany: Dzielnik napięcia + opto-izolator
   - Odświeżanie: Natychmiastowe
   
3. **Sygnał VSS (Vehicle Speed Sensor)** (pośrednie):
   - Oszacowanie RPM na podstawie prędkości + przełożenia
   - Mniej dokładne, ale wystarczające
   - Tylko jako fallback

## 🔌 Schemat połączeń

### Pinout ESP32
```
GPIO 34 (ADC1_CH6)  →  Sygnał pierwszej sondy lambda (przez dzielnik napięcia)
GPIO 35 (ADC1_CH7)  →  Sygnał RPM z cewki zapłonowej (przez opto-izolator) LUB
GPIO 16 (RX2)       →  CAN Bus RX (jeśli używasz MCP2515)
GPIO 17 (TX2)       →  CAN Bus TX (jeśli używasz MCP2515)
GPIO 25 (DAC1)      →  Wyjście sygnału emulowanego (do ECU przez filtr RC)
GPIO 26 (DAC2)      →  Opcjonalnie: Drugi kanał (rezerwowy)
GPIO 33 (ADC1_CH5)  →  Detekcja trybu LPG (HIGH = LPG, LOW = Benzyna)
GPIO 2              →  LED diagnostyczny (zielony = OK)
GPIO 4              →  LED trybu LPG (niebieski = LPG aktywny)
GND                 →  Masa pojazdu (wspólna z ECU)
VIN (5V)            →  +12V przez regulator napięcia (LM7805 lub DC-DC buck)
```

### Dodatkowe komponenty
- **Dzielnik napięcia** (wejście sondy): 10kΩ + 10kΩ (dla sygnału 0-5V → 0-2.5V)
- **Filtr RC** (wyjście DAC): 1kΩ + 10µF (wygładzenie sygnału)
- **Opto-izolator** (wejście RPM): PC817 lub 6N137 (izolacja od cewki zapłonowej)
- **Rezystor pull-down**: 10kΩ na wejściu ADC (GPIO 34)
- **Rezystor pull-down**: 10kΩ na wejściu RPM (GPIO 35)
- **Kondensatory stabilizujące**: 100µF na VIN, 10µF na 3.3V
- **Bezpiecznik**: 1A w linii zasilania +12V
- **Moduł CAN Bus** (opcjonalnie): MCP2515 + TJA1050 (do OBD-II)

## 🚀 Instalacja i konfiguracja

### Wymagania systemowe
- Visual Studio Code
- PlatformIO IDE (rozszerzenie do VSC)
- Claude Code (narzędzie AI do wspomagania programowania)

### Struktura projektu
```
lambda-emulator/
├── README.md (ten plik - dla Claude Code)
├── PRZEWODNIK_DLA_POCZATKUJACYCH.md (dla Ciebie - teoria i praktyka)
├── platformio.ini
├── src/
│   ├── main.cpp
│   ├── lambda_sensor.h
│   ├── lambda_sensor.cpp
│   ├── signal_processor.h
│   ├── signal_processor.cpp
│   ├── rpm_reader.h              // Nowy moduł: Odczyt obrotów
│   ├── rpm_reader.cpp
│   ├── lpg_detector.h            // Nowy moduł: Detekcja trybu LPG
│   └── lpg_detector.cpp
├── include/
│   └── config.h
└── test/
    └── test_emulator.cpp
```

### Kroki instalacji
1. Sklonuj repozytorium lub rozpakuj projekt
2. Otwórz folder w Visual Studio Code
3. Upewnij się, że PlatformIO jest zainstalowane
4. Podłącz ESP32 przez USB
5. Uruchom Claude Code: `claude code` w terminalu VSC
6. Build i upload: `pio run -t upload`

## 🧪 Tryby pracy i diagnostyka

### Tryby operacyjne
1. **Normal Mode**: Standardowa emulacja (produkcja)
2. **Calibration Mode**: Kalibracja parametrów (przez Serial Monitor)
3. **Debug Mode**: Szczegółowe logi diagnostyczne
4. **Passthrough Mode**: Przepuszczanie sygnału bez modyfikacji (testowanie)

### Diagnostyka LED
**LED zielony (GPIO 2) - Status główny:**
- **Wolne miganie** (1Hz): Normalna praca, RPM odczytywane poprawnie
- **Szybkie miganie** (5Hz): Tryb kalibracji aktywny
- **Pulsowanie** (fade in/out): Learning mode - adaptacja parametrów
- **Stałe świecenie**: Błąd czujnika/sygnału
- **Wyłączony**: Brak zasilania

**LED niebieski (GPIO 4) - Status LPG:**
- **Włączony**: Samochód jedzie na LPG (kompensacja aktywna)
- **Wyłączony**: Samochód jedzie na benzynie
- **Miganie**: Przełączanie benzyna ↔ LPG w trakcie

**Kombinacje (diagnostyka problemów):**
- **Oba migają naprzemiennie**: Brak sygnału RPM (używa fallback)
- **Oba świecą**: Krytyczny błąd (sprawdź Serial Monitor)

### Serial Monitor (115200 baud)
```
Lambda Emulator v1.0 - OPEL Astra H (RPM-based with LPG detection)
====================================================================
[RPM Source]: CAN Bus (OBD-II) | Current: 1850 RPM
[Fuel Mode]: LPG Active | Compensation: +0.03V
[Filter Profile]: CRUISE_MODE (Smoothing: 0.18, Delay: 100ms)
--------------------------------------------------------------------
Sensor 1 (Pre-cat):  0.78V (Rich)
Sensor 2 (Emulated): 0.52V (Stable)
Smoothing: Active | Response Delay: 100ms | Amplitude Reduction: 40%
--------------------------------------------------------------------
Status: OK | Runtime: 00:15:34 | Learn Mode: Enabled
Adaptation: Petrol→LPG transitions detected: 3 | Profile optimized
```

## ⚙️ Konfiguracja zaawansowana

### Automatyczne dostrajanie
Emulator może działać w trybie auto-tune:
- Analizuje wzorce sygnału z pierwszej sondy przez 5 minut
- Dostosowuje parametry SMOOTHING_FACTOR i AMPLITUDE_REDUCTION
- Zapisuje optymalne wartości w pamięci EEPROM

### Over-The-Air (OTA) Updates
Możliwość aktualizacji firmware przez WiFi (opcjonalne):
```cpp
// W config.h
#define ENABLE_OTA true
#define WIFI_SSID "TwojaSSID"
#define WIFI_PASSWORD "TwojeHaslo"
```

## 📈 Procedura kalibracji

### Krok 1: Wstępne uruchomienie
1. Podłącz tylko zasilanie (bez sygnałów lambda i RPM)
2. Sprawdź logi Serial Monitor
3. Potwierdź poprawne napięcia referencyjne
4. Sprawdź czy ESP32 wykrywa brak sygnału RPM (powinien pokazać warning)

### Krok 2: Konfiguracja źródła RPM
**WYBIERZ METODĘ:**

**Opcja A: OBD-II przez CAN Bus (zalecane)**
1. Podłącz moduł MCP2515 do ESP32 (SPI + CS pin)
2. Połącz CAN H i CAN L do portu OBD-II
3. W config.h ustaw: `#define RPM_SOURCE RPM_OBD2`
4. Uruchom silnik, sprawdź czy odczytuje RPM (powinno być ~850 na jałowym)

**Opcja B: Sygnał z cewki zapłonowej**
1. Znajdź przewód sygnałowy cewki (zwykle cienki przewód idący do cewki)
2. Podłącz przez opto-izolator PC817 do GPIO 35
3. W config.h ustaw: `#define RPM_SOURCE RPM_COIL`
4. Uruchom silnik, sprawdź częstotliwość impulsów (4 impulsy/obrót dla 4-cyl)

**Opcja C: Fallback (oszacowanie)**
1. W config.h ustaw: `#define RPM_SOURCE RPM_FALLBACK`
2. ESP32 będzie szacować RPM na podstawie czasu między zmianami sondy
3. Mniej dokładne, ale działa jako backup

### Krok 3: Kalibracja detekcji LPG
1. Znajdź przewód sygnałowy przełącznika LPG (zwykle zielony lub niebieski)
2. Podłącz do GPIO 33 przez opto-izolator lub bezpośrednio (jeśli 12V → użyj dzielnika!)
3. Przełącz samochód na LPG
4. Sprawdź w Serial Monitor: powinno pokazać "Fuel Mode: LPG Active"
5. Przełącz na benzynę
6. Sprawdź: powinno pokazać "Fuel Mode: Petrol"

### Krok 4: Podłączenie sygnału wejściowego (pierwsza sonda)
1. Podłącz sygnał z pierwszej sondy lambda do GPIO 34 (przez dzielnik napięcia!)
2. Uruchom silnik i pozostaw na biegu jałowym (benzyna)
3. Obserwuj odczyty przez 2-3 minuty
4. Zapisz wzorzec oscylacji przy różnych RPM:
   - Jałowy: ~850 RPM
   - Lekkie przyspieszenie: ~2000 RPM
   - Mocne przyspieszenie: ~4000 RPM

### Krok 5: Auto-kalibracja profili RPM
1. Włącz tryb kalibracji (wysłanie 'C' przez Serial)
2. Włącz Learning Mode (wysłanie 'L')
3. Wykonaj jazdę testową:
   - 5 min na biegu jałowym (benzyna)
   - 5 min jazdy miejskiej 1500-2500 RPM (benzyna)
   - 5 min przyspieszania 2500-4000 RPM (benzyna)
4. Przełącz na LPG i powtórz punkty a-c
5. System automatycznie dostosuje parametry SMOOTHING i AMPLITUDE_REDUCTION dla każdego profilu
6. Zapisz parametry (wysłanie 'S')

### Krok 6: Weryfikacja na LPG
1. Przełącz na LPG
2. Sprawdź czy emulator stosuje kompensację (Serial Monitor powinien pokazać "+0.03V offset")
3. Obserwuj czy sygnał Sensor 2 jest stabilny (~0.53V dla LPG)
4. Porównaj z benzyną (~0.50V dla benzyny)

### Krok 7: Test drogowy
1. Podłącz wyjście emulatora do ECU (zamiast prawdziwej drugiej sondy)
2. Wyczyść kody błędów (OBD-II scanner)
3. Wykonaj jazdę testową (20-30 min):
   - 10 min na benzynie (różne obroty)
   - Przełącz na LPG
   - 10 min na LPG (różne obroty)
   - Przełącz z powrotem na benzynę
4. Sprawdź czy błąd P0420 nie powrócił
5. Sprawdź logi adaptacji w Serial Monitor

## ⚠️ Ostrzeżenia i disclaimer

### Bezpieczeństwo
- **UWAGA**: Modyfikacja systemu emisji spalin może być nielegalna w Twoim kraju
- Projekt tylko do celów edukacyjnych i prywatnego użytku
- Nie zaleca się używania na drogach publicznych w krajach z kontrolą emisji
- Użycie na własne ryzyko

### Elektryczne
- Zawsze odłączaj akumulator przed ingerencją w instalację elektryczną
- Sprawdź polaryzację przed podłączeniem
- Nie zwieraj pinów ESP32 z masą lub +12V

### Diagnostyczne
- Backup oryginalnej konfiguracji ECU przed instalacją
- Monitoruj pozostałe parametry silnika (temperatura, ciśnienie paliwa)
- W razie problemów, natychmiast przywróć fabryczny układ

## 🔍 Rozwiązywanie problemów

### Problem: Kod błędu P0420 nadal się pojawia
**Rozwiązanie**: 
- Zwiększ SMOOTHING_FACTOR (0.2 → 0.25)
- Zmniejsz AMPLITUDE_REDUCTION (0.4 → 0.35)
- Sprawdź połączenia elektryczne

### Problem: Silnik pracuje nierówno
**Rozwiązanie**:
- Zmniejsz RESPONSE_DELAY (100ms → 80ms)
- Sprawdź czy pierwsza sonda działa poprawnie
- Włącz tryb Passthrough do weryfikacji

### Problem: ESP32 się restartuje
**Rozwiązanie**:
- Sprawdź stabilność zasilania (dodaj kondensatory)
- Zmniejsz częstotliwość próbkowania
- Sprawdź czy nie ma zwarcia na pinach

## 📚 Zasoby dodatkowe

### Dokumentacja techniczna
- [ESP32 Datasheet](https://www.espressif.com/sites/default/files/documentation/esp32_datasheet_en.pdf)
- [Bosch LSU 4.9 Lambda Sensor Datasheet](https://www.bosch-motorsport.com/content/downloads/Raceparts/Resources/pdf/Data_Sheet_LSU_49.pdf)
- [OPEL Astra H Service Manual](https://www.opel.com/)

### Przydatne narzędzia
- **OBD-II Scanner**: Do odczytu kodów błędów
- **Oscyloskop**: Do analizy sygnałów (opcjonalne)
- **Multimetr**: Do pomiarów napięć

### Społeczność
- Forum OPEL Astra H: [www.astra-owners-network.co.uk](https://www.astra-owners-network.co.uk/)
- ESP32 Community: [esp32.com](https://esp32.com/)

## 📝 Historia wersji

### v1.0.0 (Planowana)
- Pierwsza wersja robocza
- Podstawowa emulacja sygnału
- Tryb kalibracji
- Diagnostyka Serial Monitor

### v1.1.0 (Przyszła)
- Auto-tuning parameters
- OTA updates
- Web interface do konfiguracji
- Zapis logów na SD card

## 👨‍💻 Autor i licencja

**Projekt**: Lambda Sensor Emulator for OPEL Astra H  
**Platforma**: ESP32  
**Licencja**: MIT License (do użytku edukacyjnego)

---

## 🚦 Quick Start dla Claude Code

Gdy uruchomisz Claude Code w tym projekcie, możesz użyć następujących promptów:

```
"Wygeneruj kompletny kod main.cpp dla emulatora sondy lambda"
"Stwórz strukturę projektu PlatformIO z wszystkimi plikami"
"Dodaj tryb kalibracji z interakcją przez Serial Monitor"
"Zaimplementuj algorytm adaptacyjnego wygładzania sygnału"
"Stwórz testy jednostkowe dla modułu przetwarzania sygnału"
```

**Gotowy do startu!** Uruchom `claude code` w terminalu VSC aby rozpocząć pracę nad projektem.

---

*Ostatnia aktualizacja: 2025-10-23*  
*Kompatybilność: ESP32 DevKitC V4, PlatformIO Core 6.0+*
