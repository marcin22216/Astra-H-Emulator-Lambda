# 🎓 Przewodnik dla początkujących - Krok po kroku

## 👋 Witaj!

Ten dokument został stworzony specjalnie dla Ciebie - początkującego majsterkowicza. Wszystko będzie wyjaśnione prostym językiem, krok po kroku, jakbyś rozmawiał z kolegą który już to przeszedł.

---

## 📚 Spis treści
1. [Czym w ogóle jest ten projekt?](#czym-w-ogóle-jest-ten-projekt)
2. [Co musisz wiedzieć na początek?](#co-musisz-wiedzieć-na-początek)
3. [Lista zakupów - co kupić?](#lista-zakupów---co-kupić)
4. [Przygotowanie warsztatu](#przygotowanie-warsztatu)
5. [Teoria dla początkujących](#teoria-dla-początkujących)
6. [Instalacja oprogramowania](#instalacja-oprogramowania)
7. [Pierwszy test - migająca dioda](#pierwszy-test---migająca-dioda)
8. [Budowa emulatora sondy lambda](#budowa-emulatora-sondy-lambda)
9. [FAQ - Najczęstsze pytania](#faq---najczęstsze-pytania)
10. [Co może się zepsuć i jak to naprawić](#co-może-się-zepsuć-i-jak-to-naprawić)

---

## Czym w ogóle jest ten projekt?

### Wyobraź sobie to tak:

**Problem:** 
Twój samochód ma dwa czujniki (sondy lambda), które sprawdzają czy katalizator działa. Jeśli katalizator jest słaby lub usunięty, komputer samochodu wykrywa to i włącza "check engine" 💡.

**Rozwiązanie:**
Zbudujemy małe urządzenie (emulator), które "udaje" że wszystko jest OK. To jak podszycie się pod kogoś - czytamy co mówi pierwszy czujnik i mówimy komputerowi samochodu dokładnie to, co chce usłyszeć.

**CO NOWEGO w tej wersji (ZAAWANSOWANE):**
- 🎯 **Emulacja oparta na obrotach silnika (RPM)** - układ automatycznie dostosowuje sygnał w zależności od tego czy silnik stoi, jedzie spokojnie czy przyspiesza
- ⛽ **Wsparcie dla instalacji LPG** - wykrywa kiedy jeździsz na gazie i odpowiednio koryguje sygnał (gaz jest uboższy niż benzyna)
- 🧠 **Tryb uczący się** - sam dopasowuje parametry podczas jazdy

**Co użyjemy:**
- **ESP32** - to taki mały komputer wielkości pudełka zapałek
- Kilka przewodów
- Kilka elementów elektronicznych (rezystory, kondensatory - wyjaśnię za chwilę)
- **Opcjonalnie: Moduł CAN Bus** (do odczytu obrotów z komputera samochodu)

---

## Co musisz wiedzieć na początek?

### Nie martw się, jeśli nie znasz wszystkiego!

**To co MUSISZ umieć:**
- ✅ Używać komputera i instalować programy
- ✅ Podłączać kabelki USB
- ✅ Być cierpliwym (czasami coś nie działa za pierwszym razem)
- ✅ Umieć używać multimetru (jeśli nie - pokażę Ci!)

**To czego NIE musisz umieć (nauczysz się w trakcie):**
- ❌ Programowania (Claude Code zrobi to za Ciebie)
- ❌ Elektroniki (wszystko wyjaśnię)
- ❌ Znajomości schematów (pokażę obrazki)

### Czas realizacji:
- **Pierwsze uruchomienie ESP32**: 30 minut
- **Budowa emulatora na płytce stykowej (wersja podstawowa)**: 2-3 godziny
- **Dodanie modułu RPM (opcjonalne)**: +1-2 godziny
- **Dodanie detekcji LPG (opcjonalne)**: +30 minut
- **Lutowanie finalnej wersji**: 2-3 godziny
- **Testowanie w samochodzie**: 1 godzina
- **Kalibracja finalna (jazda testowa)**: 2-3 godziny

**RAZEM:**
- **Wersja podstawowa** (bez RPM, bez LPG): ~1 dzień roboczy
- **Wersja zaawansowana** (z RPM i LPG): ~1.5-2 dni robocze

---

## Lista zakupów - co kupić?

### 🛒 Część 1: Podstawowe komponenty (MUSISZ MIEĆ)

#### 1. **ESP32 DevKitC V4** (~30-40 zł)
```
Co to jest? Mały komputer z WiFi i Bluetooth
Gdzie kupić? Allegro, Botland.com.pl, Kamami.pl
Szukaj: "ESP32 DevKitC" lub "ESP32 WROOM-32"
UWAGA: Upewnij się że ma 30 pinów (po 15 z każdej strony)!
```

#### 2. **Kabel USB Micro-B** (~5-10 zł)
```
Co to jest? Taki jak do starego telefonu Android
Po co? Podłączenie ESP32 do komputera
Długość: Min. 1 metr
```

#### 3. **Płytka stykowa (breadboard)** (~10-15 zł)
```
Co to jest? Płytka z dziurkami do wtykania przewodów (bez lutowania!)
Rozmiar: 830 punktów (duża) - polecam do nauki
Po co? Najpierw zbudujemy prototyp bez lutowania
```

#### 4. **Zestaw przewodów połączeniowych (jumper wires)** (~8-12 zł)
```
Co to jest? Kolorowe kabelki z zakończeniami
Rodzaj: Męsko-męskie (M-M) - 40 sztuk
Po co? Połączenia na płytce stykowej
```

#### 5. **Rezystory** (~2-5 zł za zestaw)
```
Potrzebujesz:
- 2x 10kΩ (kiloom) - kolory: brązowy-czarny-pomarańczowy
- 1x 1kΩ (1 kiloom) - kolory: brązowy-czarny-czerwony
- 1x 470Ω (om) - kolory: żółty-fioletowy-brązowy

Kup ZESTAW rezystorów (200-500 szt.) - będzie ~10 zł i masz na przyszłość
```

#### 6. **Kondensatory** (~3-8 zł)
```
Potrzebujesz:
- 1x 100µF (mikrofarad) 25V - kondensator elektrolityczny (DUŻY, czarny, ma + i -)
- 1x 10µF 25V - kondensator elektrolityczny
- 2x 10µF ceramiczny (mały, żółty/niebieski - nie ma biegunowości)

Kup ZESTAW kondensatorów (~10-15 zł) i masz na przyszłość
```

#### 7. **Regulator napięcia LM7805** (~2-3 zł)
```
Co to jest? Zamienia 12V z samochodu na bezpieczne 5V dla ESP32
Wygląd: Czarna kostka z 3 nóżkami
WAŻNE: ESP32 potrzebuje max 5V na pin VIN!
```

#### 8. **Dioda LED** (~0.50 zł)
```
Kolor: Zielona lub niebieska (do sygnalizacji)
UWAGA: Dioda ma kierunek! Dłuższa nóżka to PLUS (+)
```

**SUMA za część 1: ~70-100 zł**

---

### 🛒 Część 1B: Komponenty OPCJONALNE (dla wersji zaawansowanej z RPM i LPG)

#### 9. **Moduł CAN Bus MCP2515 + TJA1050** (~15-25 zł) - OPCJONALNE
```
Co to jest? Moduł do komunikacji z komputerem samochodu (OBD-II)
Po co? Odczyt rzeczywistych obrotów silnika (RPM) z ECU
Gdzie kupić? Allegro, AliExpress
Szukaj: "MCP2515 CAN Bus module Arduino"
UWAGA: Sprawdź czy ma TJA1050 (transceiver CAN) - potrzebny do samochodu!
```

#### 10. **Opto-izolator PC817** (~2-3 zł za 5 szt.) - OPCJONALNE
```
Co to jest? Element który izoluje ESP32 od wysokich napięć w samochodzie
Po co? Bezpieczne podłączenie do sygnału cewki zapłonowej lub przełącznika LPG
Wygląd: Mała czarna kostka z 4 nóżkami (jak mini-chip)
Kup: 5-10 sztuk (na zapas, łatwo spalić podczas testów)
```

#### 11. **Dodatkowe przewody połączeniowe** (~5 zł)
```
Rodzaj: Męsko-żeńskie (M-F) - 20 sztuk
Po co? Podłączenie modułu CAN Bus
```

#### 12. **Złącze OBD-II męskie** (~10-15 zł) - OPCJONALNE
```
Co to jest? Wtyczka do gniazda diagnostycznego OBD-II
Po co? Podłączenie CAN Bus do komputera samochodu
Gdzie? Allegro, sklepy motoryzacyjne
Szukaj: "OBD-II connector male 16-pin"
UWAGA: Sprawdź pinout dla Opel Astra H (CAN H = pin 6, CAN L = pin 14)
```

**SUMA za część 1B (opcjonalne): ~30-50 zł**

---

### 🛒 Część 2: Narzędzia (jeśli nie masz)

#### 1. **Multimetr cyfrowy** (~25-50 zł)
```
Co to jest? Urządzenie do mierzenia napięcia, prądu
Nie musi być drogi! Prosty chiński wystarczy
Funkcje których potrzebujesz:
- Pomiar napięcia DC (V⎓)
- Pomiar ciągłości (♪ symbol buzzer)
- Pomiar rezystancji (Ω)
```

#### 2. **Lutownica** (~30-80 zł) - **OPCJONALNE na początek**
```
Typ: 30-60W z regulacją temperatury
Po co? Do finalnej wersji (lutowanie na płytce uniwersalnej)
Możesz kupić później! Najpierw zbuduj na breadboardzie
```

#### 3. **Ostrożniaki, szczypce, obcinak** (~20-30 zł za zestaw)
```
Zestaw majsterkowicza elektronika
Gdzie? Allegro, sklepy z elektroniką
```

**SUMA za część 2: ~45-130 zł (multimetr jest KONIECZNY!)**

---

### 🛒 Część 3: Do montażu w samochodzie

#### 1. **Obudowa hermetyczna** (~10-20 zł)
```
Rozmiar: ~100x60x40mm
Materiał: Plastik ABS (odporny na temperaturę)
IP Rating: Min. IP65 (odporność na wilgoć)
```

#### 2. **Złącza automotive** (~15-25 zł)
```
Typ: Superseal 2-pin lub 4-pin
Po co? Profesjonalne połączenia, łatwy demontaż
Możesz też użyć standardowych konektorów faston
```

#### 3. **Przewody samochodowe** (~10-15 zł)
```
Przekrój: 0.75mm² (wystarczy)
Długość: 2-3 metry
Kolory: Czerwony, czarny, niebieski, żółty (dla przejrzystości)
```

#### 4. **Taśma izolacyjna / rurki termokurczliwe** (~5-10 zł)
```
Po co? Zabezpieczenie połączeń
Rurki są lepsze - profesjonalnie wyglądają
```

#### 5. **Opaski zaciskowe** (~5 zł)
```
Po co? Mocowanie wiązki przewodów
Rozmiar: 100-150mm, czarne
```

**SUMA za część 3: ~45-85 zł**

---

## 🧰 Przygotowanie warsztatu

### Twoje miejsce pracy:

**Potrzebujesz:**
1. **Stół** - stabilny, dobrze oświetlony
2. **Lampka** - najlepiej LED, biała (do precyzyjnej pracy)
3. **Podkładka** - antystatyczna mata lub kawałek kartonu
4. **Organizery** - pudełka na elementy (po tabletki, śrubki - idealne!)
5. **Laptop/PC** - z Windows, Linux lub Mac (wszystko działa)

**Dobre praktyki:**
- 🔌 Zawsze odłączaj USB przed podłączaniem czegoś do ESP32
- 🧲 Trzymaj magnesy z dala od ESP32 (może uszkodzić pamięć)
- 💧 Pracuj w suchym miejscu (wilgoć = zwarcie)
- 🚬 Nie pal przy elektronice (popiół = zwarcie)

---

## 📖 Teoria dla początkujących

### Lekcja 1: Napięcie, prąd i rezystancja

**Wyobraź sobie wodę w rurach:**

🌊 **Napięcie (V - Volt)** = **Ciśnienie wody**
- Im wyższe napięcie, tym większa "siła" elektryczna
- Samochód: 12V
- ESP32: 3.3V (BARDZO WAŻNE!)
- Sonda lambda: 0-1V (sygnał)

⚡ **Prąd (A - Amper)** = **Ilość wody** płynąca przez rurę
- Im więcej prądu, tym więcej energii
- ESP32 potrzebuje: ~100-200mA (miliamper)
- LED: ~20mA

🔒 **Rezystancja (Ω - Om)** = **Zwężenie w rurze**
- Im wyższa rezystancja, tym mniejszy prąd
- Używamy do ograniczania prądu

**Prawo Ohma (najważniejsze równanie):**
```
U = I × R
Napięcie = Prąd × Rezystancja

Przykład:
Jeśli mamy 5V i rezystor 1000Ω (1kΩ):
I = U / R = 5V / 1000Ω = 0.005A = 5mA
```

---

### Lekcja 2: Co to jest ESP32?

**ESP32 to mały komputer który:**
- ✅ Ma procesor (dwurdzeniowy, 240MHz - szybki!)
- ✅ Ma pamięć RAM (jak w komputerze, ale mniejszą)
- ✅ Ma WiFi i Bluetooth
- ✅ Ma wiele "pinów" (nóżek) do podłączania rzeczy
- ✅ Kosztuje grosze (~30 zł)

**Czym się różni od Arduino?**
- ESP32 jest **mocniejszy** (szybszy procesor, więcej pamięci)
- ESP32 ma **WiFi i Bluetooth** (Arduino nie ma)
- ESP32 jest **tańszy**
- Programuje się podobnie (przez USB)

**Dlaczego ESP32 a nie Arduino?**
- Więcej pinów = możemy podłączyć więcej modułów (lambda, skrzynia, łopatki)
- WiFi = możemy zrobić konfigurację bezprzewodowo
- Bluetooth = możemy podłączyć aplikację na telefon (w przyszłości)
- Pamięć = możemy zapisywać logi, ustawienia

---

### Lekcja 3: Co to są piny GPIO?

**GPIO = General Purpose Input/Output**
Po polsku: "Wielozadaniowe wejście/wyjście"

**ESP32 ma ~34 piny GPIO które możesz użyć do:**
- 📥 **Wejścia (Input)**: Czytanie sygnału (np. z sondy lambda, przycisku)
- 📤 **Wyjścia (Output)**: Wysyłanie sygnału (np. do LED, do ECU samochodu)

**Numery pinów:**
```
GPIO 34, 35, 36, 39 → TYLKO wejście (nie można użyć jako wyjście!)
GPIO 0, 2, 4, 5, 12-19, 21-23, 25-27, 32-33 → Wejście/Wyjście (najlepsze)
GPIO 1, 3 → TX/RX (do komunikacji Serial, lepiej nie używać)
GPIO 6-11 → Podłączone do pamięci Flash (NIE UŻYWAJ!)
```

**W naszym projekcie używamy:**
- **GPIO 34** → Wejście (czytamy sygnał z pierwszej sondy lambda)
- **GPIO 25** → Wyjście DAC (wysyłamy sygnał do ECU)
- **GPIO 2** → Wyjście (kontrolka LED)

---

### Lekcja 4: Co to jest ADC i DAC?

#### **ADC = Analog to Digital Converter**
*"Przetwornik analogowo-cyfrowy"*

**Prostymi słowami:**
Zamienia napięcie (sygnał analogowy) na liczbę (sygnał cyfrowy) którą komputer rozumie.

**Przykład:**
```
Sonda lambda wysyła: 0.65V (napięcie analogowe)
ESP32 przez ADC czyta: 2730 (liczba od 0 do 4095)

Jak to przeliczyć?
0V = 0
3.3V = 4095 (maksymalna wartość)

Wzór: napięcie = (wartość ADC / 4095) × 3.3V
Czyli: 2730 / 4095 × 3.3V ≈ 0.65V ✓
```

**UWAGA:**
ESP32 ma ADC który mierzy **0-3.3V**. Jeśli sonda lambda wysyła **0-5V**, musimy użyć **dzielnika napięcia** (wyjaśnię za moment).

---

#### **DAC = Digital to Analog Converter**
*"Przetwornik cyfrowo-analogowy"*

**Prostymi słowami:**
Zamienia liczbę (sygnał cyfrowy) na napięcie (sygnał analogowy).

**Przykład:**
```
Chcemy wysłać do ECU: 0.52V (napięcie analogowe)
ESP32 przelicza: 0.52V / 3.3V × 255 ≈ 40 (liczba od 0 do 255)
DAC generuje: 0.52V
```

**ESP32 ma 2 piny DAC:**
- GPIO 25 (DAC1) ← używamy tego
- GPIO 26 (DAC2)

---

### Lekcja 5: Dzielnik napięcia

**Problem:**
Sonda lambda wysyła sygnał **0-5V**, ale ESP32 przyjmuje max **3.3V**. Jeśli podłączymy 5V, **SPALIMY ESP32**! 💥

**Rozwiązanie: Dzielnik napięcia**

**Jak to działa?**
Używamy dwóch rezystorów żeby "podzielić" napięcie.

```
Schemat:
         
5V ----[R1: 10kΩ]----+----[R2: 10kΩ]---- GND (masa)
                     |
                     +--> Do ESP32 (2.5V bezpieczne!)
```

**Wzór:**
```
U_out = U_in × (R2 / (R1 + R2))
U_out = 5V × (10kΩ / (10kΩ + 10kΩ))
U_out = 5V × 0.5 = 2.5V ✓ Bezpieczne dla ESP32!
```

**W praktyce:**
Jeśli sonda lambda wysyła:
- **0V** → dzielnik da **0V** → ESP32 czyta 0
- **1V** → dzielnik da **0.5V** → ESP32 czyta 620
- **5V** → dzielnik da **2.5V** → ESP32 czyta 3100 (bezpieczne!)

Potem w kodzie mnożymy wynik × 2 i mamy oryginalną wartość.

---

### Lekcja 6: Kondensatory - po co są?

**Kondensator = "Elektryczny zbiorniczek"**

**Dwa główne zastosowania:**

#### 1. **Stabilizacja napięcia (Power Supply)**
```
Samochód: 12V... ale to nie jest idealne 12V!
Rzeczywistość: 11.8V... 12.3V... 11.9V... (drgania!)

Kondensator 100µF: "Wygładza" te drgania
Efekt: Stabilne 12V dla ESP32
```

#### 2. **Filtrowanie sygnału (RC Filter)**
```
Sygnał z DAC: 0101010101 (szybkie drgania PWM)
Kondensator 10µF + Rezystor 1kΩ: Wygładza to do płynnej linii
Efekt: ECU widzi ładny, analogowy sygnał
```

**Rodzaje kondensatorów:**

**Elektrolityczny** (duży, czarny, ma + i -):
- Pojemność: 10µF, 100µF, 1000µF
- UWAGA: **Ma biegunowość!** Minus do GND, plus do +
- Jak podłączysz źle = WYBUCH 💥 (nie żartuję, może eksplodować!)

**Ceramiczny** (mały, żółty/niebieski):
- Pojemność: 0.1µF, 1µF, 10µF
- Nie ma biegunowości - możesz podłączyć jak chcesz
- Bezpieczniejszy dla początkujących

---

### Lekcja 7: Jak działa sonda lambda?

**Sonda lambda = Czujnik tlenu w spalinach**

**W Twoim Oplu są DWA czujniki:**

#### **1. Pierwsza sonda (przed katalizatorem)**
```
Zadanie: Mówi komputerowi czy mieszanka jest:
- UBOGA (Lean): Za dużo powietrza → Napięcie ~0.1V
- BOGATA (Rich): Za dużo paliwa → Napięcie ~0.9V
- IDEALNA (Stoichiometric): λ=1 → Napięcie ~0.45V

Zachowanie: Szybko "skacze" 0.1V ↔ 0.9V (kilka razy na sekundę)
To NORMALNE! Komputer tak reguluje wtrysk paliwa.
```

#### **2. Druga sonda (za katalizatorem)**
```
Zadanie: Sprawdza czy katalizator działa
- Sprawny katalizator: "Wygładza" sygnał → ~0.45-0.55V (stabilne)
- Zepsuty katalizator: Sygnał "skacze" jak pierwsza sonda

Komputer porównuje obie sondy:
- Jeśli druga sonda jest stabilna → Katalizator OK ✓
- Jeśli druga sonda "skacze" → Katalizator FAILED → P0420 ✗
```

**Nasz emulator:**
Czyta pierwszą sondę (skaczesz sygnał) i "udaje" drugą sondę (stabilny sygnał) → Komputer myśli że katalizator działa!

---

## 💻 Instalacja oprogramowania

### Krok 1: Zainstaluj Visual Studio Code

**Visual Studio Code (VSC) = Edytor kodu (za darmo!)**

1. Idź na: https://code.visualstudio.com/
2. Kliknij **"Download for Windows"** (lub Mac/Linux)
3. Zainstaluj normalnie (Next → Next → Install)
4. Uruchom VSC

**Wygląd:** Czarny ekran, menu po lewej stronie, miejsce na kod w środku

---

### Krok 2: Zainstaluj PlatformIO

**PlatformIO = Narzędzie do programowania ESP32**

**W Visual Studio Code:**

1. Kliknij ikonę **"Extensions"** po lewej stronie (ikona 4 kwadratów)
2. W wyszukiwarkę wpisz: **"PlatformIO IDE"**
3. Znajdź oficjalną wtyczkę (od **PlatformIO**)
4. Kliknij **"Install"**
5. Poczekaj ~2-5 minut (pobiera dużo plików)
6. Zrestartuj VSC (zamknij i otwórz ponownie)

**Sprawdzenie:**
- Po lewej stronie powinna pojawić się ikona "👽" (alien) lub "🏠"
- To jest PlatformIO!

---

### Krok 3: Zainstaluj sterowniki USB

**ESP32 używa chipa CH340 lub CP2102 do komunikacji USB**

**Sprawdź który masz:**
Podłącz ESP32 przez USB do komputera i zobacz nazwę w Menedżerze urządzeń.

#### **Dla Windows:**

**CH340:**
1. Idź na: http://www.wch-ic.com/downloads/CH341SER_EXE.html
2. Pobierz **CH341SER.EXE**
3. Uruchom i zainstaluj

**CP2102:**
1. Idź na: https://www.silabs.com/developers/usb-to-uart-bridge-vcp-drivers
2. Pobierz dla Windows
3. Zainstaluj

**Sprawdzenie:**
1. Podłącz ESP32 przez USB
2. Otwórz **Menedżer urządzeń** (Device Manager)
3. Rozwiń **"Porty (COM & LPT)"**
4. Powinieneś zobaczyć: **"USB-SERIAL CH340 (COM3)"** lub podobne
5. Zapamiętaj numer COM (np. COM3) - będzie potrzebny!

---

### Krok 4: Zainstaluj Claude Code (OPCJONALNE - ale mega pomocne!)

**Claude Code = AI które pisze kod za Ciebie!**

**Instalacja:**

1. Otwórz Terminal w VSC (**Ctrl + `** lub Menu: Terminal → New Terminal)
2. Wpisz:
```bash
npm install -g @anthropic-ai/claude-code
```
3. Poczekaj na instalację
4. Sprawdź czy działa: wpisz `claude code --version`

**Pierwsze użycie:**
```bash
claude code
```

Claude zapyta Cię o API key - ale to tylko jeśli chcesz używać wersji płatnej. 
**Dla naszego projektu wystarczy wersja darmowa!**

---

## 🎯 Pierwszy test - migająca dioda

**CEL:** Sprawdzić czy ESP32 działa ZANIM zaczniemy budować emulator

### Test 1: Blink (migająca dioda wbudowana)

ESP32 ma wbudowaną diodę LED na GPIO 2. Sprawdźmy czy działa!

#### **Krok po kroku:**

1. **Podłącz ESP32 do komputera przez USB**

2. **Otwórz PlatformIO:**
   - Kliknij ikonę PlatformIO (domek) po lewej
   - Kliknij **"+ New Project"**

3. **Konfiguracja projektu:**
   ```
   Name: test_blink
   Board: wpisz "ESP32" i wybierz "Espressif ESP32 Dev Module"
   Framework: Arduino
   Location: zostaw domyślnie
   ```
   Kliknij **"Finish"** i poczekaj (pobiera pliki)

4. **Otwórz plik `src/main.cpp`**
   
5. **Skopiuj ten kod:**
   ```cpp
   #include <Arduino.h>
   
   // Pin wbudowanej diody LED
   #define LED_PIN 2
   
   void setup() {
     // Ustaw pin LED jako wyjście
     pinMode(LED_PIN, OUTPUT);
     
     // Uruchom komunikację Serial (do debugowania)
     Serial.begin(115200);
     Serial.println("ESP32 uruchomiony!");
   }
   
   void loop() {
     digitalWrite(LED_PIN, HIGH);  // Włącz LED
     Serial.println("LED ON");
     delay(1000);                  // Czekaj 1 sekundę
     
     digitalWrite(LED_PIN, LOW);   // Wyłącz LED
     Serial.println("LED OFF");
     delay(1000);                  // Czekaj 1 sekundę
   }
   ```

6. **Skompiluj i wgraj:**
   - Na dole VSC znajdź ikonę **"→"** (strzałka w prawo)
   - Kliknij **"Upload"** (lub Ctrl+Alt+U)
   - Poczekaj ~30 sekund

7. **Obserwuj:**
   - Niebieska dioda na ESP32 powinna **migać co sekundę**!
   - Jeśli miga = SUKCES! ✓

8. **Otwórz Serial Monitor:**
   - Kliknij ikonę **"🔌"** (wtyczka) na dole
   - Wybierz odpowiedni port COM
   - Ustaw **115200 baud**
   - Powinieneś widzieć:
     ```
     LED ON
     LED OFF
     LED ON
     LED OFF
     ...
     ```

### Co się właśnie stało?

**Wyjaśnienie kodu:**

```cpp
#include <Arduino.h>
```
↑ To jak "import" biblioteki Arduino (zestaw gotowych funkcji)

```cpp
#define LED_PIN 2
```
↑ Mówimy że LED jest na pinie nr 2

```cpp
pinMode(LED_PIN, OUTPUT);
```
↑ Ustawiamy pin 2 jako WYJŚCIE (będziemy wysyłać sygnał)

```cpp
digitalWrite(LED_PIN, HIGH);
```
↑ Ustawiamy pin 2 na HIGH (3.3V) = LED się zapala

```cpp
digitalWrite(LED_PIN, LOW);
```
↑ Ustawiamy pin 2 na LOW (0V) = LED gaśnie

```cpp
delay(1000);
```
↑ Czekaj 1000 milisekund (1 sekunda)

```cpp
Serial.println("LED ON");
```
↑ Wyślij tekst "LED ON" przez USB do komputera (widzisz w Serial Monitor)

---

### Test 2: Czytanie napięcia (ADC)

Teraz sprawdźmy czy potrafimy czytać napięcie z pinu.

**Co będziesz potrzebował:**
- ESP32 podłączony do USB
- 1x przewód połączeniowy (jumper)
- Opcjonalnie: potencjometr 10kΩ (pokrętło do regulacji napięcia)

#### **Prosty test (bez potencjometru):**

1. **Stwórz nowy projekt:**
   ```
   Name: test_adc
   Board: Espressif ESP32 Dev Module
   Framework: Arduino
   ```

2. **Kod do `src/main.cpp`:**
   ```cpp
   #include <Arduino.h>
   
   #define ADC_PIN 34  // Pin GPIO34 (tylko wejście!)
   
   void setup() {
     Serial.begin(115200);
     Serial.println("Test ADC - czytanie napięcia");
     
     // Ustaw pin jako wejście (opcjonalne, domyślnie już jest)
     pinMode(ADC_PIN, INPUT);
   }
   
   void loop() {
     // Odczytaj wartość z ADC (0-4095)
     int rawValue = analogRead(ADC_PIN);
     
     // Przelicz na napięcie (0-3.3V)
     float voltage = (rawValue / 4095.0) * 3.3;
     
     // Wyświetl
     Serial.print("Raw: ");
     Serial.print(rawValue);
     Serial.print(" | Napięcie: ");
     Serial.print(voltage, 3);  // 3 miejsca po przecinku
     Serial.println(" V");
     
     delay(500);  // Co pół sekundy
   }
   ```

3. **Wgraj kod** (Upload)

4. **Otwórz Serial Monitor** (115200 baud)

5. **Eksperyment:**
   - **Nie dotykaj pinu 34** → Powinieneś widzieć losowe wartości (szum)
   - **Połącz pin 34 z GND** (masa) → Powinieneś widzieć ~0V
   - **Połącz pin 34 z 3.3V** → Powinieneś widzieć ~3.3V

**Wynik:**
```
Raw: 0 | Napięcie: 0.000 V       ← Pin 34 podłączony do GND
Raw: 2050 | Napięcie: 1.650 V    ← Pin niepodłączony (szum)
Raw: 4095 | Napięcie: 3.300 V    ← Pin 34 podłączony do 3.3V
```

**Jeśli to działa = ESP32 gotowy do projektu! ✓**

---

## 🔧 Budowa emulatora sondy lambda

**UWAGA: To jest wersja PROTOTYPOWA na płytce stykowej (breadboard)**
**Nie podłączaj jeszcze do samochodu! Najpierw testujemy na stole.**

### Etap 1: Schemat połączeń (na breadboardzie)

#### **Lista połączeń:**

| Od (Źródło) | Do (Cel) | Opis |
|-------------|----------|------|
| **Zasilanie ESP32** |
| +5V (zasilacz) | VIN (ESP32) | Zasilanie 5V |
| GND (zasilacz) | GND (ESP32) | Masa |
| +5V | Kondensator 100µF (+) | Stabilizacja |
| Kondensator 100µF (-) | GND | |
| **Wejście sondy lambda (symulacja)** |
| +3.3V (ESP32) | Rezystor 10kΩ (R1) | Dzielnik napięcia |
| Rezystor 10kΩ (R1) | Punkt środkowy | |
| Punkt środkowy | GPIO 34 (ESP32) | Sygnał do ADC |
| Punkt środkowy | Rezystor 10kΩ (R2) | Druga część dzielnika |
| Rezystor 10kΩ (R2) | GND | |
| Punkt środkowy | Rezystor 10kΩ pull-down | Stabilizacja |
| **Wyjście do "ECU" (symulacja)** |
| GPIO 25 (ESP32) | Rezystor 1kΩ | Filtr RC |
| Rezystor 1kΩ | Punkt wyjściowy | |
| Punkt wyjściowy | Kondensator 10µF | Wygładzanie |
| Kondensator 10µF | GND | |
| **LED diagnostyczny** |
| GPIO 2 (ESP32) | Rezystor 470Ω | Ograniczenie prądu |
| Rezystor 470Ω | LED (anoda +) | Nóżka dłuższa |
| LED (katoda -) | GND | Nóżka krótsza |

---

### Etap 2: Budowa krok po kroku (ZDJĘCIA SŁOWNIE)

**Będziesz potrzebował:**
- Breadboard (płytka stykowa)
- ESP32
- Wszystkie elementy z listy zakupów
- Przewody jumper

#### **Krok 1: Umieść ESP32 na breadboardzie**

```
Umieść ESP32 W ŚRODKU breadboarda:
- Lewa strona pinów w kolumnie E
- Prawa strona pinów w kolumnie F
- Środek ESP32 nad "rowkiem" płytki

TO JEST WAŻNE! Jeśli źle umieścisz, piny się zewrą!
```

#### **Krok 2: Zasilanie**

```
1. Czerwony przewód: 
   5V (zasilacz USB) → Szyna + (czerwona linia na breadboardzie)
   
2. Czarny przewód:
   GND (zasilacz) → Szyna - (niebieska/czarna linia)
   
3. Połącz szynę + z pinem VIN ESP32
4. Połącz szynę - z pinem GND ESP32

5. Kondensator 100µF:
   Nóżka + (dłuższa) → Szyna +
   Nóżka - (krótsza, z minusem na obudowie) → Szyna -
   
UWAGA: Kondensator ma kierunek! Źle podłączony = wybuch!
```

#### **Krok 3: LED diagnostyczny**

```
1. Rezystor 470Ω (żółty-fioletowy-brązowy):
   Jedna nóżka → Pin GPIO 2 na ESP32
   Druga nóżka → Wolna dziurka na breadboardzie (np. wiersz 10)
   
2. LED:
   Nóżka DŁUGA (anoda +) → Ta sama dziurka co rezystor (wiersz 10)
   Nóżka KRÓTKA (katoda -) → Szyna - (GND)
```

#### **Krok 4: Dzielnik napięcia (wejście ADC)**

```
1. Rezystor 10kΩ #1:
   Jedna nóżka → Pin 3.3V na ESP32
   Druga nóżka → Wolna dziurka (np. wiersz 15) = "punkt środkowy"
   
2. Rezystor 10kΩ #2:
   Jedna nóżka → Punkt środkowy (wiersz 15)
   Druga nóżka → Szyna - (GND)
   
3. Przewód:
   Punkt środkowy (wiersz 15) → Pin GPIO 34 na ESP32
   
4. Rezystor 10kΩ #3 (pull-down):
   Jedna nóżka → Pin GPIO 34
   Druga nóżka → Szyna - (GND)

TO JEST SYMULACJA! Później podłączymy tu prawdziwą sondę lambda.
```

#### **Krok 5: Filtr RC (wyjście DAC)**

```
1. Rezystor 1kΩ (brązowy-czarny-czerwony):
   Jedna nóżka → Pin GPIO 25 na ESP32
   Druga nóżka → Wolna dziurka (np. wiersz 20) = "punkt wyjściowy"
   
2. Kondensator 10µF ceramiczny:
   Jedna nóżka → Punkt wyjściowy (wiersz 20)
   Druga nóżka → Szyna - (GND)

TO BĘDZIE WYJŚCIE DO ECU. Na razie nie podłączamy!
```

---

### Etap 3: Sprawdzenie połączeń (BARDZO WAŻNE!)

**Przed podłączeniem zasilania ZAWSZE sprawdź:**

#### **Test 1: Continuity (ciągłość)**

Ustaw multimetr na tryb ciągłości (symbol ♪ buzzer)

```
1. Sprawdź czy szyna + NIE jest zwarta z szyną -
   Multimetr: Szyna + (czerwona) ↔ Szyna - (czarna)
   Wynik: Powinien NIE być dźwięk = OK ✓
   Jeśli JEST dźwięk = ZWARCIE! Znajdź błąd!

2. Sprawdź czy pin VIN jest połączony ze szyną +
   Multimetr: Pin VIN ↔ Szyna +
   Wynik: Powinien być dźwięk = OK ✓

3. Sprawdź czy pin GND jest połączony ze szyną -
   Multimetr: Pin GND ↔ Szyna -
   Wynik: Powinien być dźwięk = OK ✓
```

#### **Test 2: Napięcie (po podłączeniu zasilania)**

Podłącz zasilacz 5V (lub USB) i ustaw multimetr na tryb DC Voltage

```
1. Zmierz napięcie na szynie +
   Multimetr (+) → Szyna +
   Multimetr (-) → Szyna -
   Wynik: Powinno być 4.8-5.2V = OK ✓

2. Zmierz napięcie na pinie VIN
   Multimetr (+) → Pin VIN
   Multimetr (-) → GND
   Wynik: Powinno być 4.8-5.2V = OK ✓

3. Zmierz napięcie na pinie 3.3V
   Multimetr (+) → Pin 3.3V ESP32
   Multimetr (-) → GND
   Wynik: Powinno być 3.2-3.4V = OK ✓
```

**Jeśli wszystko OK = Możesz iść dalej! ✓**
**Jeśli coś nie gra = ODŁĄCZ ZASILANIE i sprawdź połączenia ponownie!**

---

### Etap 4: Kod programu (wersja podstawowa)

**Stwórz nowy projekt:**
```
Name: lambda_emulator_v1
Board: Espressif ESP32 Dev Module
Framework: Arduino
```

**Plik `src/main.cpp` (wersja UPROSZCZONA dla nauki):**

```cpp
#include <Arduino.h>

// ===== KONFIGURACJA PINÓW =====
#define SENSOR_INPUT_PIN 34    // Wejście: Pierwsza sonda lambda (ADC)
#define ECU_OUTPUT_PIN 25      // Wyjście: Sygnał do ECU (DAC)
#define LED_PIN 2              // LED diagnostyczny

// ===== PARAMETRY KALIBRACJI =====
const float SMOOTHING_FACTOR = 0.15;     // Ile wygładzamy sygnał (0.1-0.3)
const float AMPLITUDE_REDUCTION = 0.4;   // O ile zmniejszamy amplitudę (0.3-0.6)
const float VOLTAGE_OFFSET = 0.50;       // Docelowe napięcie bazowe (0.45-0.55V)

// ===== ZMIENNE GLOBALNE =====
float sensor1_voltage = 0.0;             // Napięcie z pierwszej sondy
float sensor2_emulated = 0.0;            // Napięcie emulowane (druga sonda)
float smoothed_value = 0.50;             // Wartość wygładzona (startujemy od środka)

// ===== FUNKCJE POMOCNICZE =====

// Odczytaj napięcie z ADC (GPIO 34)
float readSensor1() {
  int raw = analogRead(SENSOR_INPUT_PIN);
  // Przelicz ADC (0-4095) na napięcie (0-3.3V)
  float voltage = (raw / 4095.0) * 3.3;
  // Pomnóż ×2 bo mamy dzielnik napięcia 1:1
  return voltage * 2.0;
}

// Wygładź sygnał (filtr dolnoprzepustowy)
float smoothSignal(float current, float new_value) {
  return (current * (1.0 - SMOOTHING_FACTOR)) + (new_value * SMOOTHING_FACTOR);
}

// Emuluj drugą sondę (logika główna)
float emulateSensor2(float sensor1) {
  // 1. Wygładź sygnał
  smoothed_value = smoothSignal(smoothed_value, sensor1);
  
  // 2. Oblicz odchylenie od środka (0.5V)
  float deviation = smoothed_value - 0.5;
  
  // 3. Zmniejsz amplitudę odchylenia
  deviation = deviation * AMPLITUDE_REDUCTION;
  
  // 4. Dodaj z powrotem do offsetu
  float output = VOLTAGE_OFFSET + deviation;
  
  // 5. Ogranicz do zakresu 0.3-0.7V (bezpieczny zakres)
  if (output < 0.3) output = 0.3;
  if (output > 0.7) output = 0.7;
  
  return output;
}

// Wyślij napięcie na DAC (GPIO 25)
void outputToECU(float voltage) {
  // DAC ma zakres 0-3.3V, ale mapujemy do 0-255
  int dac_value = (int)((voltage / 3.3) * 255.0);
  
  // Ogranicz do 0-255
  if (dac_value < 0) dac_value = 0;
  if (dac_value > 255) dac_value = 255;
  
  // Wyślij na DAC
  dacWrite(ECU_OUTPUT_PIN, dac_value);
}

// ===== SETUP (uruchamiane raz na początku) =====
void setup() {
  // Uruchom komunikację Serial
  Serial.begin(115200);
  Serial.println("========================================");
  Serial.println("Lambda Emulator v1.0 - OPEL Astra H");
  Serial.println("========================================");
  Serial.println();
  
  // Ustaw piny
  pinMode(SENSOR_INPUT_PIN, INPUT);      // GPIO 34 jako wejście
  pinMode(LED_PIN, OUTPUT);              // GPIO 2 jako wyjście (LED)
  
  // Start z wyłączonym LED
  digitalWrite(LED_PIN, LOW);
  
  Serial.println("[OK] System uruchomiony!");
  Serial.println("[INFO] Czekam na stabilizację sygnału...");
  delay(2000);
  
  // Migij LED 3 razy = gotowy
  for (int i = 0; i < 3; i++) {
    digitalWrite(LED_PIN, HIGH);
    delay(200);
    digitalWrite(LED_PIN, LOW);
    delay(200);
  }
  
  Serial.println("[OK] Emulator gotowy do pracy!");
  Serial.println();
}

// ===== LOOP (główna pętla, powtarzana w nieskończoność) =====
void loop() {
  // 1. Odczytaj pierwszą sondę
  sensor1_voltage = readSensor1();
  
  // 2. Emuluj drugą sondę
  sensor2_emulated = emulateSensor2(sensor1_voltage);
  
  // 3. Wyślij sygnał do "ECU"
  outputToECU(sensor2_emulated);
  
  // 4. Migaj LED (wolno = OK)
  static unsigned long last_blink = 0;
  if (millis() - last_blink > 1000) {
    digitalWrite(LED_PIN, !digitalRead(LED_PIN));
    last_blink = millis();
  }
  
  // 5. Wyświetl dane w Serial Monitor (co 500ms)
  static unsigned long last_print = 0;
  if (millis() - last_print > 500) {
    Serial.print("Sensor 1: ");
    Serial.print(sensor1_voltage, 3);
    Serial.print("V | Sensor 2 (Emulated): ");
    Serial.print(sensor2_emulated, 3);
    Serial.print("V | Smoothed: ");
    Serial.print(smoothed_value, 3);
    Serial.println("V");
    
    last_print = millis();
  }
  
  // 6. Małe opóźnienie (10ms = 100Hz częstotliwość próbkowania)
  delay(10);
}
```

---

### Etap 5: Testowanie na stole (bez samochodu)

**Wgraj kod na ESP32 i otwórz Serial Monitor (115200 baud)**

#### **Test 1: Sygnał wejściowy (symulacja)**

Ponieważ nie mamy jeszcze prawdziwej sondy, zasymulujemy sygnał:

```
1. Odłącz "punkt środkowy" od GPIO 34

2. Podłącz przewód jumper do GPIO 34

3. Eksperyment:
   a) Dotknij przewodem pinu 3.3V → Powinieneś zobaczyć w Serial Monitor:
      Sensor 1: ~3.3V (przez dzielnik = 1.65V po korekcji)
      Sensor 2: wzrośnie do ~0.6V
      
   b) Dotknij przewodem GND → Powinieneś zobaczyć:
      Sensor 1: ~0V
      Sensor 2: spadnie do ~0.4V
      
   c) Szybko dotykaj 3.3V i GND na przemian → Sensor 2 powinien być STABILNY (~0.5V)
```

**Jeśli Sensor 2 jest stabilny (nie skacze jak Sensor 1) = Algorytm działa! ✓**

#### **Test 2: Wyjście DAC**

Teraz sprawdźmy czy GPIO 25 wysyła prawidłowe napięcie:

```
1. Ustaw multimetr na tryb DC Voltage

2. Podłącz multimetr:
   Multimetr (+) → "Punkt wyjściowy" (wiersz 20, po rezystorze 1kΩ)
   Multimetr (-) → GND

3. Obserwuj:
   - Powinieneś widzieć napięcie ~0.4-0.6V (stabilne)
   - Gdy poruszasz "Sensor 1", wyjście powinno LEKKO się zmieniać, ale WOLNO

Jeśli napięcie jest stabilne i w zakresie 0.4-0.6V = Wyjście działa! ✓
```

---

## ❓ FAQ - Najczęstsze pytania

### Q1: Czy mogę użyć innego ESP32?
**A:** Tak! Działa każdy ESP32 (ESP32-S2, ESP32-C3, ESP32-S3), ale:
- Sprawdź numery pinów (mogą się różnić!)
- Upewnij się że ma ADC i DAC

### Q2: Czy mogę użyć Arduino zamiast ESP32?
**A:** Nie polecam. Arduino Uno/Nano:
- Ma tylko 1 ADC (potrzebujemy minimum 2 dla przyszłych modułów)
- Nie ma DAC (musisz użyć PWM + filtr, mniej dokładne)
- Mniej pamięci (za mało na rozbudowę)

### Q3: Ile prądu zużywa emulator?
**A:** ~100-150mA (mniej niż ładowarka telefonu)

### Q4: Czy to bezpieczne dla mojego samochodu?
**A:** Jeśli zbudujesz według instrukcji - TAK. Emulator tylko CZYTA pierwszą sondę i WYSYŁA sygnał do ECU. Nie może uszkodzić silnika.
**UWAGA:** Jeśli podłączysz źle (np. +12V do ESP32) = spalenie ESP32 (ale nie samochodu).

### Q5: Czy to zadziała w moim Oplu?
**A:** Tak, dla Astra H 2004-2010 z silnikami benzynowymi. Dla innych modeli może wymagać kalibracji.

### Q6: Czy to jest legalne?
**A:** Zależy od kraju:
- **Polska**: Nie jest zabronione, CHYBA ŻE masz obowiązek badań emisji spalin (przy rejestracji, przeglądzie)
- **UE**: Generalnie nielegalne na drogach publicznych
- **Prywatne użytkowanie**: Twoja odpowiedzialność

### Q7: Co jeśli emulator się zepsuje podczas jazdy?
**A:** ECU wykryje brak sygnału z drugiej sondy i:
- Włączy "check engine"
- Przejdzie w tryb awaryjny (używa tylko pierwszej sondy)
- Silnik będzie działał normalnie (może być minimalnie gorsze spalanie)

### Q8: Jak długo ESP32 wytrzyma w samochodzie?
**A:** Jeśli dobrze zamkniesz w obudowie:
- **Temperatura**: ESP32 działa -40°C do +85°C (komora silnika OK)
- **Wilgoć**: IP65 obudowa chroni
- **Wibracje**: Jeśli dobrze przykleisz/przykręcisz - lata

### Q9: Czy mogę to zrobić bez lutowania?
**A:** TAK! Prototyp na breadboardzie działa, ale:
- NIE montuj go w samochodzie (wibracje rozłączą przewody)
- Używaj tylko na stole do testów

### Q10: Ile kosztuje cały projekt?
**A:** ~150-250 zł (wszystko włącznie z narzędziami)

---

## 🔥 Co może się zepsuć i jak to naprawić

### Problem 1: ESP32 się nie odpala (brak diody power)

**Możliwe przyczyny:**
- Nie ma zasilania na VIN lub GND
- Zwarcie na breadboardzie
- Uszkodzony port USB

**Rozwiązanie:**
1. Sprawdź multimetrem napięcie na VIN (powinno być 5V)
2. Sprawdź czy szyna + nie jest zwarta z szyną -
3. Podłącz ESP32 bezpośrednio do USB (omiń breadboard)
4. Spróbuj innego kabla USB

---

### Problem 2: Komputer nie widzi ESP32 (brak COM port)

**Możliwe przyczyny:**
- Brak sterowników USB (CH340/CP2102)
- Zły kabel USB (tylko do ładowania, nie ma pinów data)
- Uszkodzony port USB na komputerze

**Rozwiązanie:**
1. Zainstaluj sterowniki ponownie (rozdział: Instalacja oprogramowania)
2. Sprawdź inny kabel USB (musi być kabel DATA, nie tylko power!)
3. Spróbuj inny port USB na komputerze
4. Sprawdź Menedżer urządzeń → Porty COM (powinien być CH340 lub CP2102)

---

### Problem 3: Błąd podczas uploadu "Failed to connect"

**Możliwe przyczyny:**
- Wybrany zły port COM
- ESP32 jest zajęty (np. Serial Monitor otwarty)

**Rozwiązanie:**
1. Zamknij Serial Monitor przed uploadem!
2. Sprawdź czy wybrałeś dobry port COM w PlatformIO
3. Przytrzymaj przycisk BOOT na ESP32 podczas uploadu
4. Po pojawieniu się "Connecting....." puść BOOT

---

### Problem 4: Serial Monitor pokazuje śmieci (krzaki, losowe znaki)

**Możliwa przyczyna:**
- Zła szybkość transmisji (baud rate)

**Rozwiązanie:**
1. Ustaw Serial Monitor na **115200 baud**
2. Restart ESP32 (przycisk EN lub odłącz/podłącz USB)

---

### Problem 5: Sensor 1 pokazuje 0V lub losowe wartości

**Możliwe przyczyny:**
- Pin GPIO 34 nie jest podłączony
- Dzielnik napięcia źle połączony
- Brak rezystora pull-down

**Rozwiązanie:**
1. Sprawdź czy GPIO 34 jest połączony z punktem środkowym dzielnika
2. Sprawdź czy rezystory 10kΩ są dobrze włożone w breadboard
3. Dodaj rezystor 10kΩ pull-down (GPIO 34 → GND)

---

### Problem 6: Sensor 2 nie wygładza sygnału (skacze jak Sensor 1)

**Możliwe przyczyny:**
- Za niski SMOOTHING_FACTOR
- Brak kondensatora 10µF na wyjściu
- Zbyt szybka pętla loop()

**Rozwiązanie:**
1. Zwiększ SMOOTHING_FACTOR (np. z 0.15 na 0.25)
2. Sprawdź czy kondensator 10µF jest podłączony do GPIO 25
3. Dodaj `delay(10);` na końcu loop()

---

### Problem 7: ESP32 się restartuje (ciągły reset)

**Możliwe przyczyny:**
- Za słabe zasilanie (niewystarczający prąd)
- Zwarcie na którymś pinie
- Uszkodzony kondensator

**Rozwiązanie:**
1. Podłącz zasilacz USB z większym prądem (min. 500mA)
2. Dodaj kondensator 100µF na VIN (jeśli nie masz)
3. Odłącz wszystko oprócz zasilania i sprawdź czy dalej restartuje
4. Sprawdź czy kondensator 100µF jest dobrze podłączony (+ do VIN, - do GND)

---

### Problem 8: Wyjście DAC (GPIO 25) pokazuje 0V

**Możliwe przyczyny:**
- Pin GPIO 25 nie jest ustawiony jako DAC
- Kod nie wywołuje `dacWrite()`
- Zwarcie na wyjściu

**Rozwiązanie:**
1. Sprawdź w kodzie czy jest `dacWrite(ECU_OUTPUT_PIN, dac_value);`
2. NIE używaj `pinMode(25, OUTPUT)` - DAC nie potrzebuje pinMode!
3. Zmierz napięcie bezpośrednio na pinie GPIO 25 (bez rezystora)

---

### Problem 9: LED nie miga

**Możliwe przyczyny:**
- LED podłączony odwrotnie (zamienione nóżki)
- Brak rezystora 470Ω (LED się spalił)
- Pin GPIO 2 nie działa

**Rozwiązanie:**
1. Sprawdź polaryzację LED (długa nóżka to +)
2. Sprawdź czy jest rezystor 470Ω w szeregu z LED
3. Zmień pin w kodzie na GPIO 13 i sprawdź czy działa

---

## 🎉 Gratulacje!

Jeśli doszedłeś do tego miejsca i wszystko działa - **BRAWO!** 🎊

Właśnie zbudowałeś swój pierwszy emulator sondy lambda na ESP32!

### Co dalej?

1. **Testuj na stole** przez kilka dni - obserwuj stabilność
2. **Przygotuj obudowę** - wodoodporna, odporna na temperaturę
3. **Zalutuj finalną wersję** - breadboard NIE nadaje się do samochodu!
4. **Zamontuj w samochodzie** - podłącz do prawdziwych sond
5. **Rozbuduj projekt** - dodaj moduł skrzyni biegów i łopatki!

---

## 📞 Potrzebujesz pomocy?

**Jeśli coś nie działa:**
1. Przeczytaj dokładnie ten dokument od początku
2. Sprawdź sekcję "Co może się zepsuć"
3. Użyj Claude Code - wpisz: "Problem: [opisz co nie działa]"
4. Forum OPEL Astra H: https://www.astra-owners-network.co.uk/

**Powodzenia i bezpiecznych majsterkowań!** 🔧⚡🚗

---

*Dokument stworzony specjalnie dla początkujących majsterkowiczów*  
*Wersja 1.0 | Ostatnia aktualizacja: 2025-10-23*
