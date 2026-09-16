# Completeness, labels, assets, contexts

Light coverage. Endpoint lists live in [api-full.md](api-full.md) — open only the section you need.

## Completeness

Completeness is **schema + values**, not a separate product type.

### CLA mandatory vs completeness requirements

| Need | Use |
|---|---|
| This definition must be filled on **every product type** in the category | CLA `mandatory` (hard gate: red fields, can block publish/sync) |
| This definition must be filled on **some types only** (e.g. GROUP, not FAMILY) | Completeness requirement with `includedProductTypes` (score/workflow; does **not** stop publish/sync the way CLA mandatory does) |
| Same CLA mandatory but types must not share a node | Separate catalog trees per product type (more structure to maintain) |

Default: completeness requirements when mandatory-ness differs by type; CLA mandatory only when it is true for all types in that node.

Completeness can also include category scope (`…/categories/{id}/included`) and types via `PUT …/requirements/{id}/productType/{type}/included`. OpenAPI may omit `FAMILY` from the enum; **FAMILY works** (confirmed).

### Other levers

- **VLA `mandatory`** (with `copy: true`): every variant under the GROUP should have the value (inherited or own). Still GROUP→VARIANT only — it does not spare FAMILY from a CLA on a shared catalog.
- Empty FAMILY is correct when no collection-level definitions are CLA-mandatory.
- Scores and requirement APIs: completeness-score service (machine base `https://api.<env>.bluestonepim.com/completeness-score`). PBC prefix `/api/completeness-score/`. Create: `POST /requirements` with `requirementType` (e.g. `ATTRIBUTE_HAS_VALUE`), `params.definitionId`, `weight`, then attach product types and categories.
- A score is **per product**, in one context. `GET /completeness-score/scores/{entityId}/{context}` is the breakdown; `POST /scores/list` with `entityIds` batches the headline number (`pageSize` max 100). That list is the **last stored** score, not a live recompute. Publishing, filling an attribute, or attaching media in the native product UI does not push a new number into a plugin until something calls `recalculate/entity` (or a catalog-wide start) and the plugin re-lists. A parent’s score does **not** include related children’s requirements. Folder averages (`POST /scores/category/list`) are catalog-node rollups, not a graph “sum of parts.” If a plugin needs neighbourhood readiness, it must combine child scores in the UI and label that as derived.
- Native product publish (green check / CONNECTED) is **not** a custom “publish status” attribute. Do **not** score a shadow status field. Completeness `ATTRIBUTE_HAS_VALUE` on a status definition still only checks that the attribute is filled. A PBC that badges Draft/Published from that attribute will stay Draft after a native publish until the attribute itself is written.
- `MINIMUM_MEDIA_WITH_LABEL` needs `params.mediaLabelId` + `mediaCount`. Put it on the **product** (offer / SKU), not a proxy photo card. Use a **generic** DAM label for “a file exists”. Do not use a QC-approve label here or Reject/Pending files would make the product look incomplete. QC status is an **asset** attribute (`POST /media-bank/assets/{id}/attributes/{definitionId}` then `PUT .../simple`), not a product completeness requirement. Recalculate after attaching binaries (`POST /scores/calculation/start` with `{ context }`, fallback `POST /scores/recalculate/{context}`). After writing product attributes that completeness reads, `POST /scores/calculation/recalculate/entity` with `{ context, entityIds }` (chunks of 100) then re-list those ids.

Do not “fix” a sparse FAMILY by copying child or related-object attributes onto it — that lies in the model and inflates completeness with the wrong owner.

## Labels

Product labels are tags for filtering and workflows, not attributes.

- Product: `/products/{id}/labels` and batch `/products/labels/by-ids`.
- Media Bank has its own label API (`/media-bank/labels`, asset label attach).
- Search/query-builder can filter on `product_labels` and completeness score.

Use labels for process (e.g. “ready to publish”). Use attributes for product facts.

## Assets / Media Bank

1. Create or upload an asset in Media Bank (binary, or a resolvable URL depending on the endpoint). `POST /media-bank/upload` → S3 `actionUrl` → `PUT /upload/{id}` metadata → `POST /upload/{id}/done`.
2. Facts that belong to the **file** (QC state, reject reason, colour shown, dimensions) are **asset attributes**, not a proxy PIM product. Assign a definition with `POST /media-bank/assets/{id}/attributes/{definitionId}`, then `PUT .../simple` `{ value }`, `PUT .../select` `{ selectIds }`, or `PUT .../dictionary` `{ dictionaryIds }`. Same `definitionId` space as PIM. `POST` 400/409 if that definition cannot live on assets. Do not send `{ values }` on select/dictionary — Media Bank 400s `selectIds` / `dictionaryIds` must not be null, which looks like “selects are unsupported”.
3. One asset can hang off **several products**: `POST /pim/products/{id}/assets/{assetId}` per product. Rejected files stay unattached to sellable products so a product-asset pull cannot leak them. `PUT /media-bank/assets/{id}/published` `{ value: false }` is the DAM publish gate.
4. Category membership is a **catalog** association (`GET /pim/catalogs/nodes/{id}/assets`, `GET /pim/catalogs/nodes/assets/{id}`). Confirm the write verb in the Network tab before coding — Media Bank OpenAPI does not own product or category links. An empty catalog folder just to “hold photos” is unnecessary: attach the file to the sellable products. Do not mint a PIM SINGLE per photo, and do not recreate a retired Assets category after it has been archived.
5. Publish if the flow requires it.

A spreadsheet **filename** (`hero.jpg`) is not an asset. Do not POST it as a media id. Either upload the file, or prepend a known CDN base if the customer hosts files themselves.

Product attributes that store “image name” as text are **text attributes**, not Media Bank links — fine for migration staging, not a substitute for DAM. Do not mint a PIM SINGLE per photo just to hold QC.

## Contexts (locales)

- Product **name** and **description** are context-aware. Pass `context` on write/read.
- Attribute definitions may be `contextAware: true` — values then vary by locale.
- Do not model “English name” / “German name” as two attribute definitions when a context already exists.

## Bundles vs relations vs configurator

Three different things:

- **Relation:** “this SKU uses this colour SINGLE.” Graph, not a sellable pack.
- **BUNDLE** (product type): a sellable combination of existing products with quantities (`POST /products/{id}/bundles/{complementaryProductId}`). A kit / sales code. Not a configured quote, not “the SKU plus options.”
- **Bundle configurator** (v1-apps): choice **rules** attached to a main product or category. Each rule lists **linked** products (`id` + `active` only). Rule attributes (`required`, default, help, select) sit on the **step**, not on a candidate. Customer profile / group links are B2B audience scope, not assortment or channel availability.

A configured quote, BOM explosion, or “SKU + user-picked options” is usually GROUP + related option SINGLEs (or a downstream order), not a BUNDLE. Made-to-measure option types can be configurator rules; default / frozen / rank still live on the PIM relation.

A curated **configurable parent** (choice groups with a per-candidate flag) is not a BUNDLE. Treat the parent as a SINGLE (or GROUP if it has variants). Association facts belong on the pair they describe: **product-level** relation attributes when the fact is binary; a **junction SINGLE** when it is ternary (parent + choice group + SKU) or when two flags belong to different pairs. Cloning a shared choice product per parent so a binary link can hold the flag is the junction in disguise. The configurator can **project** that graph later (one rule per parent × group, parent as `main`, SKUs as `linked`) for editor UX or PAPI `preConfig` metadata. It cannot store per-candidate ordinality — `LinkedProduct` has no attributes. Do not share one rule across parents whose candidate sets or flags differ. Do not map availability scopes to configurator customer groups.

## Customer-facing export

Native product publish (green check / `CONNECTED`) is the PIM gate. Do **not** invent a “publish status” attribute for it, and do not score one. A **papi-sync** should copy only `CONNECTED` products (and published media). PAPI (`/v1`) is that last snapshot — it does not live-read PIM. If a Draft was synced in error, it stays in `/v1` until the next curated sync; a consumer UI must not hide it as a substitute for fixing the job.

Keep expected absences on an explicit “out” list (probe the number; a miss is the pass). Omitting the row looks like a miss. The customer-live storefront gate stays with the customer’s own platform when they have one. Do not treat PIM `CONNECTED` as shop go-live.

Attach rejected binaries to the asset record only — not to the sellable SKU or pack — so a product-asset pull does not leak QC failures.

## Environments

| | TEST | PROD |
|---|---|---|
| PIM API | `https://api.test.bluestonepim.com` | `https://api.bluestonepim.com` |
| IdP | `https://idp.test.bluestonepim.com` | `https://idp.bluestonepim.com` |
| App | `https://app.test.bluestonepim.com` | `https://app.bluestonepim.com` |

PBC apps never hardcode these. Machine clients select the host from config. Do not write TEST data with PROD keys or the reverse.
