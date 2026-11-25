# ADHD board

Tablica zadań pod ADHD: jedno zadanie naraz, epiki w swimlane'ach, kroki i minutnik skupienia.

## Co tu jest

W głównym katalogu leży gotowa aplikacja webowa (PWA), z której PWABuilder robi paczkę `.apk`:

- `index.html` — cała tablica w jednym pliku
- `manifest.json` — nazwa, ikony, kolory, tryb pełnoekranowy
- `sw.js` — service worker: aplikacja otwiera się bez zasięgu
- `icon.svg`, `icon-192.png`, `icon-512.png`, `icon-maskable-512.png` — ikony

`web/index.html` to starsza kopia tablicy z 12.11.2026, zostawiona dla historii.

## Uruchomienie

Otwórz `index.html` w przeglądarce albo wystaw katalog przez GitHub Pages
(Settings → Pages → Deploy from a branch → `main` / `/ (root)`).

## Gdzie są dane

Ta wersja trzyma zadania w `localStorage` przeglądarki (klucz `adhd-board.db.v1`),
czyli na tym urządzeniu i tylko tam. To **osobna baza** niż tablica w claude.ai —
nic się między nimi nie synchronizuje.

Funkcje, które są usługami claude.ai, poza nim się nie włączają i tablica je ukrywa:
dyktowanie, „Rozbij z AI”, załączniki, Poczta oraz Zakupy (łącznik Arkuszy Google).

## Paczka APK

1. Włącz GitHub Pages jak wyżej.
2. Wklej adres `https://teddybadboy.github.io/adhd-board/` na `pwabuilder.com`.
3. Manifest i Service Worker powinny być na zielono → **Package for stores → Android**.

## Zapis danych poza claude.ai (od 26.11.2026)

Ta wersja tablicy nie ma bazy z claude.ai, więc zapisuje dane u siebie — dwiema
drogami naraz, bo jedna bywa odcięta:

1. `localStorage` — czytany synchronicznie przy starcie, klucz `adhd-board.db.v1`,
2. IndexedDB (`adhd-board` / `kv` / `db`) — zapas; ratuje sytuacje, w których
   `localStorage` rzuca wyjątkiem, i odtwarza dane po starcie.

Gdy obie drogi są zamknięte, strona **nie udaje, że zapisuje**. Pokazuje czerwony
pasek: „Ta strona jest osadzona w ramce, która blokuje zapis. Zadania znikną po
odświeżeniu." plus przycisk „Otwórz w osobnej karcie" i „Zapisz kopię do pliku".

Tak dzieje się przy osadzeniu w **Google Sites**: Sites wrzuca stronę do ramki
z sandboxem na `googleusercontent.com`, bez `allow-same-origin`. Taka ramka dostaje
pusty origin i przeglądarka odcina jej cały magazyn. Nie da się tego obejść kodem
strony — trzeba ją podlinkować zamiast osadzać, albo trzymać dane na serwerze.

Niezależnie od tego w prawym dolnym rogu są dwa przyciski: **⤓ Kopia** (eksport
wszystkiego do JSON) i **⤒ Wczytaj** (import z pliku). W konsoli `adhdStorage()`
pokazuje, które drogi zapisu działają.
