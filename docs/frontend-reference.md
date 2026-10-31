# Referencja frontendu

Inwentaryzacja lokalnych źródeł: **2026-10-31**. Ostatni commit spoza dokumentacji: `174263e`; uwzględniono również bieżące pliki robocze.

Plik generowany przez `hellopoland-docs/scripts/build-technical-reference.py`. Opis architektury i ograniczeń: [technical.md](technical.md). Wykaz nie potwierdza wdrożenia ani wykonania testów.

## Skrypty npm

| Nazwa | Polecenie z package.json |
| --- | --- |
| `analyze` | `cross-env BUNDLE_ANALYZE=browser next build` |
| `build` | `next build` |
| `dev` | `node ./server/server.js` |
| `lint` | `eslint . --ext .js,.jsx --ignore-path .gitignore --cache` |
| `lint-fix` | `npm run lint -- --fix` |
| `start` | `cross-env NODE_ENV=production node ./server/server.js` |
| `test` | `jest` |
| `test-watch` | `npm test -- --watch` |

## Zależności deklarowane

Zakresy wersji pochodzą z manifestu; rozstrzygnięte wersje instalacji zapisuje lockfile. Nie podano tokenów ani konfiguracji rejestru.

| Pakiet | Zakres | Grupa |
| --- | --- | --- |
| `@fream/axios-commons` | `^2.0.0` | dependencies |
| `@hello-poland/commons` | `^2.0.1` | dependencies |
| `@material-ui/core` | `^3.9.2` | dependencies |
| `@material-ui/docs` | `^3.0.0-alpha.9` | dependencies |
| `@material-ui/icons` | `^3.0.2` | dependencies |
| `@zeit/next-bundle-analyzer` | `^0.1.1` | dependencies |
| `axios` | `^0.18.0` | dependencies |
| `babel-polyfill` | `^6.26.0` | dependencies |
| `classnames` | `^2.2.6` | dependencies |
| `cross-env` | `^5.2.0` | dependencies |
| `date-fns` | `2.0.0-alpha.16` | dependencies |
| `express` | `^4.16.4` | dependencies |
| `formik` | `^1.5.1` | dependencies |
| `formik-material-ui` | `0.0.16` | dependencies |
| `helmet` | `^3.15.1` | dependencies |
| `http-proxy-middleware` | `^0.19.1` | dependencies |
| `i18next` | `^11.9.1` | dependencies |
| `jss` | `^9.8.7` | dependencies |
| `lodash` | `^4.17.11` | dependencies |
| `material-ui-pickers` | `^1.0.0` | dependencies |
| `nanoid` | `^2.0.1` | dependencies |
| `next` | `^7.0.3` | dependencies |
| `next-compose-plugins` | `^2.1.1` | dependencies |
| `nprogress` | `^0.2.0` | dependencies |
| `prop-types` | `^15.7.2` | dependencies |
| `react` | `^16.8.3` | dependencies |
| `react-dom` | `^16.8.3` | dependencies |
| `react-dropzone` | `^7.0.1` | dependencies |
| `react-i18next` | `^7.12.0` | dependencies |
| `react-jss` | `^8.6.1` | dependencies |
| `react-redux` | `^6.0.1` | dependencies |
| `redux` | `^4.0.1` | dependencies |
| `redux-devtools-extension` | `^2.13.8` | dependencies |
| `redux-logic` | `^2.1.1` | dependencies |
| `rxjs` | `^6.4.0` | dependencies |
| `slugify` | `^1.3.4` | dependencies |
| `styled-jsx` | `^3.2.1` | dependencies |
| `yup` | `^0.26.10` | dependencies |
| `babel-eslint` | `^10.0.1` | devDependencies |
| `babel-plugin-module-resolver` | `^3.1.3` | devDependencies |
| `eslint` | `^5.12.1` | devDependencies |
| `eslint-config-airbnb` | `^17.1.0` | devDependencies |
| `eslint-import-resolver-babel-module` | `^5.0.1` | devDependencies |
| `eslint-plugin-import` | `^2.16.0` | devDependencies |
| `eslint-plugin-jsx-a11y` | `^6.2.1` | devDependencies |
| `eslint-plugin-react` | `^7.12.4` | devDependencies |
| `jest` | `^24.1.0` | devDependencies |
| `jest-dom` | `^3.1.2` | devDependencies |
| `json-server` | `^0.14.2` | devDependencies |
| `react-testing-library` | `^6.0.0` | devDependencies |

## Strony Next.js

Pliki poniżej wskazują wejścia stron. `_app` i `_document` są elementami infrastruktury, a trasy parametryczne mogą być przepisywane przez Express.

| Plik strony | Ścieżka plikowa |
| --- | --- |
| [_app.jsx](../pages/_app.jsx) | `/_app` |
| [_document.jsx](../pages/_document.jsx) | `/_document` |
| [index.js](../pages/account/index.js) | `/account/index` |
| [password.js](../pages/account/password.js) | `/account/password` |
| [company-card.jsx](../pages/company-card.jsx) | `/company-card` |
| [index.jsx](../pages/index.jsx) | `/index` |
| [logout.jsx](../pages/logout.jsx) | `/logout` |
| [sales.jsx](../pages/sales.jsx) | `/sales` |
| [sign-in.jsx](../pages/sign-in.jsx) | `/sign-in` |
| [create.js](../pages/tickets/create.js) | `/tickets/create` |
| [edit.js](../pages/tickets/edit.js) | `/tickets/edit` |
| [index.js](../pages/tickets/index.js) | `/tickets/index` |
| [index.js](../pages/ushers/index.js) | `/ushers/index` |
| [password.js](../pages/ushers/password.js) | `/ushers/password` |
| [profile.js](../pages/ushers/profile.js) | `/ushers/profile` |

## Jawne przepisania Express

| Ścieżka HTTP | Strona Next.js |
| --- | --- |
| `/tickets/:itemId/edit` | `/tickets/edit` |
| `/ushers/:usherId` | `/ushers` |
| `/ushers/:usherId/password` | `/ushers/password` |
| `/ushers/:usherId/profile` | `/ushers/profile` |

## Testy w kodzie projektu

Wykaz lokalnych testów; nie oznacza ich wykonania. Zależności i `.next` pominięto.

| Plik testu |
| --- |
| [VoivodeshipSelect.test.js](../components/VoivodeshipSelect/VoivodeshipSelect.test.js) |
| [config.test.js](../redux/config.test.js) |
| [partners.test.js](../redux/partners.test.js) |
| [profile.test.js](../redux/profile.test.js) |
| [ticketDefinitions.test.js](../redux/ticketDefinitions.test.js) |
| [ushers.test.js](../redux/ushers.test.js) |
| [view.test.js](../redux/view.test.js) |
| [deserialize.test.js](../utils/form-generator/data/deserialize.test.js) |
| [populate.test.js](../utils/form-generator/data/populate.test.js) |
| [serialize.test.js](../utils/form-generator/data/serialize.test.js) |
| [index.test.jsx](../utils/form-generator/index.test.jsx) |
| [normalizeValue.test.js](../utils/form-generator/utils/normalizeValue.test.js) |
