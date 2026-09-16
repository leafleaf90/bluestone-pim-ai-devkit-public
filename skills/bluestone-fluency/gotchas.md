# Gotchas

Confirmed against live MAPI. If a call fails, believe the Network tab over OpenAPI.

## Prefixes

OpenAPI lists `/products/{id}`. Runtime is `/api/pim/products/{id}` in a PBC, or `https://api.<env>.bluestonepim.com/pim/products/{id}` for machine clients. Missing prefix is the most common empty/404.

## `resource-id`

Many POSTs return 200/201/204 with an empty body. The new id is the **`resource-id` response header** (lowercase). Also try `Resource-Id`. Axios: `res.headers['resource-id']`. Fetch: `response.headers.get('resource-id')`.

## 409 and reuse

Creates keyed by `number` (products, catalogs, sometimes CLA) return 409 or “already exists”. Lookup by number and continue. Do not retry POST in a loop.

## `context` / `context-fallback`

Omit these on PIM core reads and localised names come back empty or in the wrong locale. PBC: `providedEnv.session.languageId`. Machine: the locale you are writing (`en`, …).

Some **write** routes reject `context-fallback` (`POST /relations`: “context-fallback header is not supported”). Send `context` on those POSTs; add `context-fallback: true` on GETs.

`POST /products/{id}/connections/products` and `DELETE /products/{id}/connections/products/{relationId}/{connectedProductId}` reject **`context`** as well (“context header is not supported”). Omit both locale headers on **connection writes** (create and delete). Body for POST is still `{ relationId, to }`. `GET /products/{id}/connections/products/{relationId}?direction=DIRECT|REVERSE` accepts `context` and also works without it (it only returns `productId`). After a connection POST, `GET /products/{id}` `relatedProducts` can lag; union it with the connections GET so a just-created link shows up. OpenAPI lists `context` only on some operations (e.g. `GET /products/{id}`, `POST /relations`); connection writes do not declare it and do not document the 400. Do not send locale headers globally just because reads need them, and do not copy the write-header rule onto GET.

PIM core `POST /products/{id}/connections/products` stores only `relationId`, `to`, and optional quantity. Typed flags on a link live in the **relation-attributes** app (`https://api.<env>.bluestonepim.com/relation`), not on `ConnectionDto`. Assign the definition at `relation` or `product` level before PUT. `product` level is per related product; `relation` level is one value for that relation type on the product. Completeness `MINIMUM_RELATION_CONNECTIONS` scores the core edge, not whether those relation-attribute values are filled. One connection per relation type per pair — a second exception on the same pair cannot be a second edge; it overwrites the product-level attributes or needs a junction product. Relation-attribute **read** is not documented — do not assume GET of the PUT path. A GET of that path on TEST MAPI returned API Gateway 403; PUT of the same URL returned 204. Walk the core connection first.

## PAPI (Public API, `/v1`)

PAPI is the **Public API**, not a partner API. Machine base: `https://api.<env>.bluestonepim.com/v1`. Header `x-api-key`. `context` still required for names. Useful reads: `POST /products/by-numbers`, `GET /products/{id}`, `GET /categories/{id}/products`, `GET /relations`. PAPI is not a second PIM editor — it only contains the last successful **papi-sync**. Draft or unpublished products, and unpublished Media Bank assets, do not appear. `GET /products/{id}` 404 on a known number is the published-absence proof.

When the org has never papi-synced (or has no publish for that `context`), product and category reads return **404** `{ message: "No publishes available for given context.", entityId: "<context>" }`. `GET /v1/syncs` is `[]`. That is catalog-empty, not a missing SKU. After a sync exists, probe unpublished numbers in a **separate** request — do not mix them into the published `by-numbers` batch if a miss 404s the whole POST.

## VLA 400s

| Body | Result |
|---|---|
| `mandatory` / `locked` / `definingAttributes` without `copy: true` | 400 |
| `copy` on SINGLE / VARIANT / BUNDLE **create** | 400 |
| `copy` on GROUP create or VLA PUT | Valid |

`forceVla` defaults to fill-empty-only. Passing `true` overwrites variant values — use deliberately.

## CLA

POST then PATCH. Flags on the PATCH DTO may be marked deprecated in OpenAPI; they still work. CLA does **not** write a value onto FAMILY.

CLA is catalog propagation, not a prerequisite for product attributes. A product can carry `definitionId` + `values` with no CLA on its catalog. CLA with propagate adds the definition to all products in the node; empty propagated fields still have no value until someone fills them.

CLA `mandatory` applies to **all product types** in that catalog node. A mixed FAMILY+GROUP category with mandatory dims makes FAMILY incomplete even when values live on GROUP. Use completeness `includedProductTypes` when types differ; keep CLA mandatory only for cross-type facts.

## Attribute payloads are IDs

`GET /products/{id}/attributes`: `definitionId` + `values[]` and/or `dictionary[]`. No human-readable names.

| `dataType` | Field | Resolve |
|---|---|---|
| scalars | `values[]` | Display as-is |
| `single_select`, `multi_select` | `values[]` = option ids | `definition.restrictions.enum.values[].{valueId,value}` |
| `dictionary` | `dictionary[]` | Dictionary value endpoint; shape `value.value[languageId]` |

Writes use those ids: `PUT …/select` (or payload `selectValueIds`) and `PUT …/dictionary`. Sending the label string does not select the option. Dictionary POST onto a product **replaces** the full set — send existing ids + new.

## Definition upsert

Reuse by `number`. Assign `groupId` with GET-then-PUT. A missing group in TEST leaves definitions **unassigned**, not deleted.

Do **not** change `dataType` on a live definition. Stored values stay the old shape (strings stay strings). A text→select move is a **new** number, then rewrite as option ids. Display **names are unique org-wide** — a replacement select cannot reuse “Room” while the old text def exists (pick a new name, archive the old def after CLAs/completeness/values move). After that archive, a loader **ensure** must iterate only the live list — never POST the retired numbers again. `PUT …/simple` succeeding for text is not a reason to type a closed list as `text`. See [model.md](model.md) § Choose `dataType` first.

## Validation query params

Product create often uses `?validation=NUMBER`. Catalog create often uses `?validation=NAME`. Definition update: `?validation=NONE` when re-putting an existing def. Wrong validation → 400 on duplicates or names.

## PBC-only

- `getAxiosInstance()` only — not `axios` or `fetch`.
- External URLs → `/api/plugin-api/<plugin-id>/...`.
- React **16.14.0**.
- Never change `metaInfo.json` `id`.
- Dashboard widget **grid size** is `contains.dashboard[].layout` (`w`/`h`/`minW`/`minH`, …) in `metaInfo.json`, not `createDashboardPlugin`. Omit `layout` and the PIM slot stays tiny; inner CSS cannot enlarge it. Template map widgets are `4×3`; do not default `12×10` or set `minW`/`minH` so high the user cannot shrink. Reload (and re-add/resize) after changing defaults.
- Native publish (green check) is **not** `GET /pim/products/{id}` `state`. That field is old-PAPI-only. Current orgs: `POST /pim/products/list/views/by-ids` with `views: [{ type: "STATUS" }]` (same column as the product grid Status). Fallback `GET /pim/products/{id}/overview`. Missing `state` on GET is not Draft.
- `console.error` → `providedEnv.utils.showSystemMessage`.

## Machine-only

- Client credentials are per **organisation**. Mixing tenants overwrites the wrong catalog.
- Token URL is IdP (`idp.test.bluestonepim.com`), not the API host.
- If token request with `scope` fails, retry without `scope`.
- Media Bank ingest (`POST /media-bank/upload`) needs **MEDIA / WRITE** on that org’s OAuth client. PIM product scopes are not enough. `403 Forbidden` on `/upload` means grant media on this client — do not reuse another tenant’s keys.
- Media Bank attribute writes share PIM `definitionId`s. Assign with `POST /media-bank/assets/{id}/attributes/{definitionId}` (409 = already assigned). Then `PUT .../simple` `{ value }`, `PUT .../select` `{ selectIds }`, `PUT .../dictionary` `{ dictionaryIds }`. A 400 `must not be null` on `selectIds` / `dictionaryIds` is the wrong body (`values` from some OpenAPI samples) — not proof that selects cannot live on DAM. GET attributes returns `selectValueIds` / `dictionaryValueIds`; the PUT field names differ.

## 429 Too Many Requests

MAPI rate-limits bursty list/create loops (`POST /catalogs/nodes/list`, `GET /relations`, CLA PATCH). Retry the same call after `Retry-After` (or exponential backoff). Do not re-list the full relation or catalog set once per definition — cache for the write run.

## Gateway 403 on some GET collections

`GET /pim/products` and a few other collection GETs can return API Gateway “Invalid key=value pair in Authorization header” even with a valid Bearer token. That is a dead/IAM-gated route, not a bad token. Use the documented **POST `/…/list`** counterparts (`/products/list/views/by-numbers`, `/definitions/list`, `/completeness-score/requirements/list`). Those succeed with the same token.

`POST /products/list` (NUMBER_IN filter) is **deprecated** — PIM returns that as an error. Resolve products by number with `POST /products/list/views/by-numbers` (`numbers`, `views: [{ type: "METADATA" }]`, `archiveState=active`). Details: `GET /products/{id}` or `POST /products/list/views/by-ids`. Catalogs, definitions, and relations still use `/catalogs/nodes/list`, `/definitions/list`, `/relations/list`.

## Column setups (`ui-settings`)

Live OpenAPI: `https://docs.api.test.bluestonepim.com/openapi/ui-settings.json`. Machine: `https://api.<env>.bluestonepim.com/ui-settings`.

- `GET /columnsSetups/all` returns a **bare array**, same MAPI token as PIM.
- Create is `POST /columnsSetups/all`. Required: `name`, `isPublic`, `service`, `entity`, `columns`, **`owner`** (PIM user email). Response **200** with the setup in the body (`id` included). `isDefault` is response-only — omit it on create or you will fight the org Default.
- `service` is `bluestone-pim`, `entity` is `PRODUCT` for the products grid.
- `columnType` is lowercase (`name`, `number`, `status`, `score`, `attribute`, `producttype`, …). For `producttype` the column **`id` is `type`**. For attributes, `id` is the definition id and `columnType` is `attribute`.
- Optional sort field is **`sortConfig`** `{ columnId, direction }`, not `sorting`.
- `GET /columnsSetups` (no `/all`) is a dead gateway route (403 “Invalid key=value pair”), same class as `GET /pim/products`.

## When OpenAPI disagrees with the UI

The path the PIM UI calls in DevTools is authoritative. Add a one-liner to this file after confirming.
