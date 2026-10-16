# UI layer

This repository is a backend-first CAP sample. It does **not** currently contain an `app/` directory or a packaged SAPUI5/Fiori application. The UI boundary is therefore the CatalogService API, which can be consumed by a SAPUI5 app, another web client, or an API tool.

## Recommended catalog flow

A client can implement the following screens with the existing projections:

1. **Book list**: call `GET /browse/ListOfBooks`; display title, author, genre, price, currency and stock. The projection intentionally omits the long description.
2. **Book detail**: navigate to `GET /browse/Books(<id>)` when a user selects a row; show the full description and denormalized author/genre names.
3. **Order**: submit the selected book and quantity to `submitOrder`. On success, refresh the list. Display `409` as an out-of-stock/insufficient-stock message and `404` as a missing-book message.
4. **Administration**: an internal UI can use `/admin/Authors`, `/admin/Books` and `/admin/Genres` for maintenance, subject to authentication and authorization configured by the deployment.

The `@readonly` projections are safe defaults for browse screens. Writes should go through `AdminService`, where CAP validations from `srv/admin-constraints.cds` are applied.

## SAPUI5 integration notes

For a UI5 application:

- configure an OData V4 model with `/browse/` as its service URL;
- bind list controls to `/ListOfBooks` and detail controls to `/Books(<id>)`;
- use the service action for ordering rather than updating stock directly;
- surface validation messages from the CAP response instead of duplicating the database rules in the client;
- send credentials with requests because `submitOrder` requires `authenticated-user`.

A UI5 application can be added under `app/` without changing the database model. Keep the service projections as the contract between that application and the CAP backend.

## Running and exploring the API

```sh
npm install
npm run watch
```

Open `http://localhost:4004` to inspect the CAP service index. For a quick smoke test, request:

```sh
curl http://localhost:4004/browse/ListOfBooks
```
