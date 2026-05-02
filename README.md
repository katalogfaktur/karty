# 🔐 Moje Karty - PWA z kartami lojalnościowymi

Progresywna aplikacja webowa (PWA) do trzymania kodów kart lojalnościowych (Biedronka, Lidl, Żabka, Action, Rossmann, IKEA, Decathlon i inne). Działa w przeglądarce, instaluje się na ekranie głównym iPhone'a jak natywna aplikacja, **nie wymaga logowania** (tylko hasło dostępu) i **zabezpiecza dane szyfrowaniem**, więc można hostować repo publicznie na GitHubie.

## ✨ Funkcje

- **Szyfrowanie AES-256-GCM** - dane kart trzymane są jako zaszyfrowany plik. Bez hasła nie da się ich odczytać, nawet mając dostęp do repozytorium.
- **Hasło + zapamiętanie urządzenia** - wpisujesz hasło raz, urządzenie zapamiętuje je lokalnie (zaszyfrowane fingerprintem urządzenia)
- **Menu w stylu Klarny** - kolorowe kafelki kart 2 kolumny
- **20 czcionek do wyboru** - wszystkie systemowe czcionki Apple oraz inne czytelne (SF Pro, Helvetica, Avenir, Georgia, Palatino, Times, Courier, Menlo, Monaco, Arial, Verdana, Tahoma, Trebuchet, Gill Sans, Futura, Optima, Didot, Baskerville)
- **Regulacja rozmiaru tekstu** (13-22px)
- **Obsługuje** kody QR, EAN-13, CODE128 oraz QR z URL (jak Żabka)
- **Działa offline** - po pierwszym wczytaniu Service Worker cachuje aplikację

## 🚀 Pierwsza konfiguracja

### Krok 1: Wgraj pliki na GitHub

1. Stwórz nowe repozytorium na GitHubie (publiczne lub prywatne - dane są zaszyfrowane więc publiczne też jest bezpieczne)
2. Wgraj wszystkie pliki z tego folderu do repo
3. Wejdź w **Settings → Pages → Source: Deploy from branch → main → /(root)** i zapisz
4. Po chwili dostaniesz adres typu `https://twoja-nazwa.github.io/nazwa-repo/`

### Krok 2: Zmień hasło i wpisz swoje karty

**Domyślny plik `cards.enc.json` jest zaszyfrowany hasłem `zmień-to-hasło-123`** i zawiera 7 kart (z Twoich screenshotów). **MUSISZ** to zmienić:

1. Otwórz w przeglądarce plik `encrypt-tool.html` (dwuklik na komputerze - działa offline)
2. **Aby wczytać domyślne karty:** wybierz `cards.enc.json`, wpisz hasło `zmień-to-hasło-123`, kliknij "Wczytaj"
3. Edytuj karty wg potrzeb (szczególnie **Żabka** - tam jest placeholder URL który musisz podmienić - szczegóły niżej)
4. Wpisz **swoje nowe hasło** (min. 8 znaków - polecam coś łatwego do zapamiętania, ale niezgadywalnego)
5. Kliknij "Zaszyfruj i pobierz"
6. Pobrany plik `cards.enc.json` wgraj do repo na GitHubie (zastępując stary)

### Krok 3: Zainstaluj na iPhone'ach

**Na obu telefonach (Twoim i żony):**

1. Otwórz Safari (musi być Safari, nie Chrome - tylko Safari pozwala instalować PWA na iOS)
2. Wejdź na adres `https://twoja-nazwa.github.io/nazwa-repo/`
3. Kliknij ikonę **udostępnij** (kwadrat ze strzałką)
4. Przewiń i wybierz **"Dodaj do ekranu początkowego"**
5. Otwórz aplikację z ekranu głównego, wpisz swoje hasło, zaznacz "Zapamiętaj to urządzenie", kliknij "Odblokuj"

Od tej pory aplikacja otwiera się od razu, bez pytania o hasło, na obu telefonach.

## 📋 Jak dodać kod Żabki (URL z QR)

QR kod Żabki w aplikacji koduje URL specyficzny dla Twojego konta. Aby go wyciągnąć:

1. Otwórz aplikację Żabka, pokaż swój QR kod
2. Zrób screenshot
3. Otwórz screenshot w innej aplikacji która umie odczytać QR (np. ShareMe, lub na komputerze: https://zxing.org/w/decode upload)
4. Skopiuj URL który zawiera QR (zaczyna się od `https://zappka.pl/...` lub podobnie)
5. W `encrypt-tool.html` znajdź kartę Żabka, kliknij "Edytuj", wklej URL w polu "Wartość", zapisz

## 🔄 Synchronizacja między telefonami

Karty żyją w pliku `cards.enc.json` na GitHubie. Aplikacja przy każdym otwarciu pobiera świeżą wersję pliku.

**Aby dodać nową kartę:**

1. Edytuj na komputerze przez `encrypt-tool.html` (wczytaj stary plik z hasłem, dodaj kartę, pobierz nowy)
2. Wgraj `cards.enc.json` do repo na GitHubie
3. Po chwili (max 1-2 min) GitHub Pages udostępni nową wersję
4. **Na telefonie:** zamknij aplikację (przesuń w górę), otwórz ponownie - załaduje się świeży plik

⚠️ Aplikacja ma też **lokalny edytor** (ikonka koła zębatego → "Dodaj kartę") ale on **tylko tymczasowo** zapisuje kartę w pamięci sesji, **nie aktualizuje GitHubu**. Aby zmiana była trwała i zsynchronizowana, użyj eksportu (Ustawienia → Eksportuj zaszyfrowany plik) i wgraj na GitHub.

## 🛡️ Bezpieczeństwo

**Co jest zaszyfrowane:**
- Cała zawartość kart (nazwy, kody, kolory) w pliku `cards.enc.json`
- Algorytm: **AES-256-GCM** z kluczem wyprowadzanym przez PBKDF2 (250 000 iteracji, SHA-256)
- Bez znajomości hasła rozszyfrowanie metodą siłową jest praktycznie niewykonalne

**Co NIE jest zaszyfrowane:**
- Sam kod aplikacji (HTML/JS) - jest publiczny, ale to tylko logika prezentacji
- Plik `cards.enc.json` w repo jest publicznie dostępny, ale bez hasła wygląda jak losowy szum bajtów

**"Zapamiętanie urządzenia":**
- Hasło jest szyfrowane lokalnie kluczem wyprowadzonym z fingerprintu urządzenia (User-Agent, rozdzielczość ekranu, język, strefa czasowa, losowy klucz wygenerowany raz dla tego urządzenia)
- Jest zapisywane w `localStorage` (sandbox przeglądarki, niedostępny dla innych stron)
- Po wybraniu "Zablokuj i wyloguj" hasło i klucz urządzenia są usuwane

**Dlaczego aplikacja nie używa MAC adresu?**
- Przeglądarki na iOS i Androidzie **nie udostępniają MAC adresu** stronom WWW (kwestie prywatności)
- Zamiast tego stosujemy fingerprint urządzenia + lokalny losowy klucz - to praktycznie tak samo bezpieczne, a działa w przeglądarce

## 🎨 Personalizacja

- **Czcionka i rozmiar:** ikona koła zębatego w prawym górnym rogu
- **Kolory karty:** ikona ⋮ w widoku karty (lub przycisk + na liście) - możesz dostosować dowolny kolor tła i tekstu

## 🐛 Rozwiązywanie problemów

**"Nieprawidłowe hasło"** - sprawdź dokładnie hasło. Polskie znaki w haśle są dozwolone.

**Aplikacja nie aktualizuje się** - Service Worker cachuje pliki. Aby wymusić odświeżenie:
- Zamknij PWA całkowicie (przeciągnij w górę)
- Albo usuń aplikację z ekranu głównego i zainstaluj ponownie

**Nie widzę zmian zapisanych przez "Dodaj kartę" w aplikacji na drugim telefonie** - to oczekiwane. Ten edytor zapisuje tylko lokalnie. Aby zsynchronizować, użyj **Ustawienia → Eksportuj** i wgraj plik na GitHub.

**Kod kreskowy nie skanuje się w sklepie** - zwiększ jasność ekranu na maks i przybliż telefon do skanera.

## 📁 Struktura plików

```
karty-pwa/
├── index.html              # Główna aplikacja (HTML + CSS + JS)
├── cards.enc.json          # Zaszyfrowane karty (PUBLICZNY plik na GitHub)
├── encrypt-tool.html       # Narzędzie offline do edycji + szyfrowania (uruchamiasz lokalnie)
├── manifest.webmanifest    # Manifest PWA
├── sw.js                   # Service Worker (działanie offline)
├── icons/
│   ├── icon-192.png
│   ├── icon-512.png
│   └── icon-180.png        # Apple Touch Icon
└── README.md               # Ten plik
```

## 📝 Domyślnie załączone karty (do edycji)

Plik `cards.enc.json` zawiera 7 kart przygotowanych z Twoich screenshotów:

| Karta | Typ kodu | Numer | Kolory |
|---|---|---|---|
| Biedronka | EAN-13 | 9955388661422 | żółty / czerwony |
| Lidl | QR | 77480000459099338 | granat / żółty |
| Żabka | QR (URL) | placeholder - **podmień!** | zielony / biały |
| Rossmann | EAN-13 | 2640032050871 | czerwony / biały |
| IKEA Family | QR | 6275980435046046730 | niebieski / żółty |
| Decathlon | EAN-13 | 2091258936102 | granat / biały |
| Action | QR | L7468855336324 | granat / biały |

**Hasło domyślne:** `zmień-to-hasło-123` (zmień je przez `encrypt-tool.html` po pierwszym uruchomieniu!)
