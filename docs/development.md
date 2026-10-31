# Partner Web — architektura i praca lokalna

Stan: **31.10.2026**, na podstawie kodu. Weryfikacja nie obejmowała serwera TST ani PROD.

## Odpowiedzialność i mapa kodu

Panel partnera udostępnia m.in. obsługę biletów, pracowników sprawdzających bilety, sprzedaży, danych firmy i konta. Operacje wykonuje przez API backendu HP. Autoryzacja musi być egzekwowana po stronie serwera.

| Miejsce | Rola |
| --- | --- |
| [pages](../pages) | Strony Next.js, m.in. `tickets`, `ushers`, `sales`, `company-card`, `account` |
| [views](../views) | Widoki formularzy i list |
| [components](../components) | Komponenty współdzielone |
| [redux](../redux) | Stan aplikacji i logika komunikacji |
| [services](../services) | Klient HTTP, autoryzacja i infrastruktura Redux |
| [server/routes.js](../server/routes.js) | Parametryczne trasy biletów i pracowników |
| [server/server.js](../server/server.js) | Express, start Next.js i proxy w development |

Zmianę ekranu śledzimy od strony przez widok i Redux do payloadu API. Przykładowo przy dodawaniu pracownika trzeba sprawdzić także komunikat błędu zwracany przez backend HP/HT, a nie tylko walidację w formularzu.

## Narzędzia

[package.json](../package.json) deklaruje Next.js 7, React 16, Redux, Material-UI 3 i Jest 24. `engines` podaje Node `>=8.15.0` i npm `>=6.4.1`, a [Dockerfile](../Dockerfile) używa Node `10-alpine`. Sam szeroki zakres `engines` nie potwierdza poprawności z aktualnym LTS. Odtwarzamy wersję narzędzi zgodną ze sprawdzonym buildem.

Pakiety `@fream/*` i `@hello-poland/*` wymagają właściwego rejestru. Dane dostępowe do niego pozostają poza dokumentacją.

## Konfiguracja

[next.config.js](../next.config.js) wczytuje `CONFIG_PATH` lub domyślnie `./config/develop.config.js`. Plik może być modułem JS albo JSON obsługiwanym przez `require` i ma sekcje:

- `server`: ustawienia serwera, w tym `host`, `port`, `apiURL`;
- `public`: ustawienia klienta, m.in. Axios i adresy zasobów. Są dostępne przeglądarce i nie mogą zawierać sekretów.

Serwer odczytuje host z `server.host` (domyślnie `127.0.0.1`) i port z `server.port` (domyślnie `3000`). W odróżnieniu od Helpdesku kod nasłuchu Partnera nie korzysta z `NODE_HOST` ani `NODE_PORT`; `NODE_PORT` występuje jedynie w konfiguracji proxy developerskiego.

W development [proxy](../server/proxy.js) kieruje `/api` na `server.apiURL`, przepisując prefiks. Przy `NODE_ENV=production` serwer nie montuje tego middleware. Wtedy routing API musi zapewnić reverse proxy lub adres skonfigurowany w kliencie.

Używamy bezwzględnego `CONFIG_PATH`, ponieważ konfigurację ładują pliki w różnych katalogach. Dockerfile domyślnie ustawia `/data/etc/config.json`; wcześniejszy przykład TST w README nadpisuje to na `/data/config.json`. Montowanie musi odpowiadać faktycznie wybranej ścieżce.

Konfiguracja jest odczytywana przez `require` i Next.js. Uwzględniamy ją podczas budowania i startu; sama podmiana pliku w działającym procesie nie jest przeładowaniem konfiguracji. Dostęp do DMS zależy od backendu i infrastruktury. Serwer w tym repozytorium nie montuje `/DMS` jako katalogu statycznego Express.

## Praca lokalna

Katalog roboczy: `hp-client-partner-web`. Wymagane są uzgodnione Node/npm, rejestr pakietów i dostęp do przygotowanego API TST.

```sh
npm ci
npm run dev
```

`npm ci` odtwarza lockfile i zastępuje istniejący `node_modules`. Niezgodność instalacji wyjaśniamy przed zmianą lockfile.

Dla zewnętrznego pliku w PowerShell:

```powershell
$env:CONFIG_PATH = 'D:\config\partner-tst.js'
npm run dev
```

Domyślnie aplikacja nasłuchuje na `http://127.0.0.1:3000`. Sprawdzamy komunikat startowy, bo plik konfiguracji może zmienić host i port. Tryb produkcyjny wymaga `npm run build`, a potem `npm run start`, z właściwą konfiguracją.

Obecny Dockerfile kopiuje lokalny `node_modules` oraz zawartość projektu z przygotowanym `.next`; nie wykonuje samodzielnie pełnego buildu aplikacji. Samo `docker build` nie potwierdza aktualności bundla.

## Testowanie

```sh
npm test -- --runInBand
npm run lint
```

Testy obejmują m.in. Redux dla biletów, partnerów, profilu i pracowników oraz serializację formularzy w `utils/form-generator`. Przy zmianie wybieramy właściwy test i sprawdzamy na TST zapis/odczyt, błędy walidacji i zakres dostępu partnera.

Przy zmianie definicji biletów sprawdzamy też cenę, typ, pule i zachowanie portalu po zapisie. Test pracownika powinien uwzględniać duplikat e-maila i komunikat backendu, bez zakładania, że walidacja UI wystarczy.

## Wdrożenie i diagnostyka

Najpierw przygotowujemy wymagany kontrakt backendu, potem build panelu i konfigurację reverse proxy. Zachowujemy poprzedni obraz oraz konfigurację. Przy 404 API porównujemy trasę przeglądarki, proxy i bazowy adres backendu; przy braku zmian w UI sprawdzamy wiek `.next` oraz właściwy plik konfiguracji.

Stare IP i tag obrazu z README są materiałem historycznym. W ramach dokumentowania nie budowano obrazu, nie instalowano zależności, nie uruchamiano testów aplikacji i nie potwierdzano adresów środowisk.
