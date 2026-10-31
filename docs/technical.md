# Partner Web — dokumentacja techniczna

Stan lokalnych źródeł: **31.10.2026**. [Referencja frontendu](frontend-reference.md) zawiera skrypty, zależności, strony, przepisania Express i testy. [Przewodnik developerski](development.md) opisuje przygotowanie środowiska.

## Warstwy aplikacji

Serwer wejściowy to [server/server.js](../server/server.js): Express z Next.js, Helmet, opcjonalnym proxy i routingiem parametrycznym. Właściwe wejścia stron są w `pages`, a implementacje UI w `views` i `components`.

Stan jest obsługiwany przez Redux i `redux-logic`; kod infrastruktury znajduje się w `services/redux`, lokalne moduły w `redux`, a część wspólna w zależności `@hello-poland/commons`. Wywołania API przechodzą przez `services/httpClient`, konfigurację Axios i interceptory credentials.

`pages/_app.jsx`, `_document.jsx`, katalog `src` oraz konfiguracja JSS/Material-UI dotyczą infrastruktury renderowania. Projekt używa Next.js 7 z Pages Routerem; nie należy przenosić do niego instrukcji dla nowych generacji Next.js bez osobnej migracji.

## Routing i konfiguracja

| Parametr / plik | Znaczenie |
| --- | --- |
| `next.config.js` | Ładuje plik przez `CONFIG_PATH`, fallback `./config/develop.config.js` |
| `server` | Serwerowa część konfiguracji Next.js |
| `public` | Konfiguracja klienta, w tym Axios, URL zasobów i `assetPrefix` |
| `server.host` | Adres nasłuchu; fallback `127.0.0.1` |
| `server.port` | Port nasłuchu; fallback 3000 |
| `NODE_ENV` | Produkcyjny/developerski tryb Next.js |
| `server/routes.js` | Przepisania parametrycznych tras biletów i pracowników |

W tym module `NODE_HOST` i `NODE_PORT` nie ustawiają nasłuchu aplikacji. `NODE_PORT` występuje w pliku proxy jako część fallbacku celu. To różni się od Helpdesku.

Proxy `/api` z [server/proxy.js](../server/proxy.js) jest montowane wyłącznie w development. Zmienia prefiks ścieżki przed przekazaniem do `server.apiURL`. W production API musi być obsługiwane przez infrastrukturę lub adres w konfiguracji klienta.

`CONFIG_PATH` podajemy jako ścieżkę bezwzględną. Dockerfile domyślnie ustawia `/data/etc/config.json`; wcześniejsza instrukcja TST w README nadpisuje go na `/data/config.json`. Są to różne ścieżki i muszą odpowiadać montowaniu. Konfiguracja jest odczytywana przez `require` i Next.js; wymaga spójności między buildem i startem.

Publiczny config jest dostępny w przeglądarce. Sekretów i tokenów rejestru pakietów nie przenosimy do `public`, przykładów ani raportów builda.

## Klient HTTP i sesja

[requestInterceptors.js](../services/httpClient/requestInterceptors.js) pobiera credentials z Redux. [responseInterceptors.js](../services/httpClient/responseInterceptors.js) konfiguruje odświeżenie tokenu przez akcję profilu i deleguje jego obsługę do `@fream/axios-commons`.

Przy zmianie logowania trzeba sprawdzić także utrwalanie stanu w `services/redux/localStorage.js` i uruchomienie aplikacji po odświeżeniu strony. Widoczność UI nie jest autoryzacją serwerową. Przy problemach 401 analizujemy token, refresh i odpowiedź API, nie wyłącznie komponent strony.

Importy `config`, `redux/...`, `services/...` są rozwiązywane względem projektu zgodnie z Babel/module-resolver. Nie oznaczają pakietu npm o tej samej nazwie.

## Build i obraz

Manifest deklaruje React 16, Next 7 i Material-UI 3. `engines` ma szerokie zakresy Node/npm, natomiast Dockerfile wskazuje Node 10. Nie potwierdzono zgodności z najnowszym LTS.

Obecny Dockerfile kopiuje lokalny `node_modules` i cały przygotowany projekt, w tym `.next`; nie wykonuje pełnego `npm ci` i `next build`. Trzeba osobno zadbać o zgodność platformy zależności i aktualność bundla. Nie należy traktować samego `docker build` jako odtworzenia aplikacji ze źródeł.

DMS w historycznym przykładzie jest montowaniem infrastruktury. Serwer Express z tego repozytorium nie rejestruje `/DMS` jako katalogu statycznego. Finalne adresy obrazów i dokumentów trzeba śledzić z konfiguracji klienta do usługi dostarczającej zasób.

## Testowanie i kontrola zmian

Z katalogu modułu, z przygotowanymi narzędziami i zależnościami:

```sh
npm test -- --runInBand
npm run lint
npm run build
```

Istnieją testy m.in. modułów Redux i transformacji formularzy. Generowany wykaz wskazuje pliki. Test serializera formularza nie zastępuje porównania rzeczywistego JSON z kontraktem backendu; test komponentu nie potwierdza prawidłowej trasy API na serwerze.

Przy zmianie pola sprawdzamy ścieżkę: formularz → serializacja → akcja/logika → HTTP → odpowiedź/błąd → ponowny odczyt. W tej pracy nie uruchamiano testów aplikacji, obrazów ani operacji na danych środowiskowych.
