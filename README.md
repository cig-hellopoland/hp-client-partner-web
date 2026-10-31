# Partner Web (Hello! Poland)

**Dokumentacja techniczna:** [architektura, konfiguracja, build i referencja frontendu](docs/technical.md).

React / Next.js aplikacja webowa dla **Partnera** w ekosystemie **Hello! Poland**.

Przegląd lokalnego kodu: **31.10.2026**. Aktualny opis architektury, konfiguracji i pracy lokalnej: [przewodnik modułu](docs/development.md). [Mapa systemu](https://github.com/cig-hellopoland/hellopoland-docs/blob/main/README.md) prowadzi do repozytorium dokumentacji na GitHubie.

Poniższe przykłady obrazu, adresów IP, nginx i uruchomienia kontenera zachowują wcześniejszą instrukcję TST. Nie zostały ponownie sprawdzone na serwerze i wymagają potwierdzenia przed użyciem.

---

## Overview

Partner Web to aplikacja **Next.js (Node.js)**. `next.config.js` czyta plik wskazany przez `CONFIG_PATH`, a gdy zmiennej nie ma, używa `./config/develop.config.js`. Dockerfile ustawia domyślnie `/data/etc/config.json`; przykład TST poniżej jawnie nadpisuje tę ścieżkę.

Aplikacja:
- komunikuje się z **HelloPoland Backend (HPL)** przez REST API,
- korzysta z **DMS** montowanego do kontenera jako `/DMS`,
- jest wystawiana na zewnątrz **przez nginx (reverse proxy)**.

---

## Project structure (kod źródłowy)

```
/components        – globalne komponenty UI
/config            – domyślne konfiguracje developerskie (nieużywane na TST/PROD)
/pages             – routing Next.js (kebab-case)
/redux             – globalne reducery
/scripts           – skrypty buildowe
/services          – warstwa serwisów / API
/static             – pliki statyczne (obrazy, manifest)
/utils              – funkcje pomocnicze
/views              – implementacje widoków
```

> Przy ustawionym `CONFIG_PATH` plik zewnętrzny zastępuje domyślny plik developerski. Bez tej zmiennej kod korzysta z katalogu `config`.

---

## Runtime configuration (TST / PROD)

### Zasada

Konfiguracja jest ładowana przez `next.config.js` do `serverRuntimeConfig` i `publicRuntimeConfig`. Sekcja publiczna jest dostępna klientowi. Nie zakładamy, że dowolną konfigurację można podmienić po buildzie bez sprawdzenia wyniku; szczegóły są w przewodniku modułu.

Aplikacja **czyta ją tylko przy starcie** z pliku JSON wskazanego przez:

```
CONFIG_PATH=/data/config.json
```

---

## Struktura katalogów na serwerze (TST)

```
/srv/partner/tst/
├── config.json
├── config.json.bak_YYYYMMDD_HHMMSS
└── deploy_partner_image.sh (opcjonalnie)
```

Mount do kontenera:

```
-v /srv/partner/tst:/data
```

---

## Przykładowy config.json (TST)

```json
{
  "server": {
    "apiURL": "http://127.0.0.1:8180/hellopoland/v1",
    "host": "0.0.0.0"
  },
  "public": {
    "axios": {
      "baseURL": "http://145.239.133.29:8300/api/v1/partner"
    },
    "iconBaseURL": "http://127.0.0.1:8180/hellopoland/v1/static/icons",
    "availableTicketsURL": "http://127.0.0.1:8180/hellopoland/v1/market/sight-events/:id/available-tickets?date=:date",
    "baseAffiliationURL": "https://hello-mazovia.pl/oferty",
    "name": "Hello! Poland – Partner (TST)",
    "brandName": "Hello! Poland"
  }
}
```

---

## Docker

### Obraz

```
hp-partner-web:1.6.2
```

### Uruchomienie kontenera (TST)

```
docker run -d --name hello_poland_partner_tst \
  -p 127.0.0.1:8301:3000 \
  -e NODE_ENV=production \
  -e CONFIG_PATH=/data/config.json \
  -v /srv/partner/tst:/data \
  -v /var/lib/docker/volumes/HELLO_DMS_TST/_data:/DMS:rw \
  --restart unless-stopped \
  hp-partner-web:1.6.2
```

---

## nginx (reverse proxy)

```
server {
  listen 8300;
  server_name _;

  location / {
    proxy_pass http://127.0.0.1:8301/;
  }

  location /api/ {
    proxy_pass http://127.0.0.1:8180/hellopoland/;
  }
}
```

---

## Dostęp (TST)

- UI: http://145.239.133.29:8300/
- API: http://145.239.133.29:8300/api/v1/partner/

---

## SSL

Wcześniejsza instrukcja opisywała TST przez **HTTP (IP)**. README WordPressa wskazuje też domenę `t1.partner.hello-poland.pl`. Aktualny adres i terminację TLS trzeba potwierdzić w konfiguracji serwera; ten przegląd ich nie weryfikował.

---

## Status

Historyczny opis TST zachowano powyżej. Obecne działanie TST nie było testowane podczas przeglądu dokumentacji.
