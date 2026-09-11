# Recipes

Paths below are **PIM core relative** (`/products`, `/catalogs/...`).

- **PBC:** prefix `/api/pim` and use `getAxiosInstance()`.
- **Machine:** `https://api.<env>.bluestonepim.com/pim` + path, Bearer token. See [auth.md](auth.md).

Always send `context` + `context-fallback: true` on localised PIM core calls. New ids: read `resource-id` (see [gotchas.md](gotchas.md)).

## 1. Upsert a product by number

Lookup first (`POST /products/list/views/by-numbers` with `views: [{ type: "METADATA" }]` and `archiveState=active`). If found, PUT attributes. If not:

```
POST /products?validation=NUMBER
{
  "name": "Model name",
  "number": "SKU-OR-MODEL-NUMBER",
  "type": "SINGLE" | "FAMILY" | "GROUP",
  "description": "optional",
  "categories": ["catalogNodeId"],
  "attributes": [
    {
      "definitionId": "...",
      "values": ["42"],
      "copy": true,
      "definingAttributes": false,
      "locked": false,
      "mandatory": true
    }
  ]
}
```

`copy` / `mandatory` / `locked` / `definingAttributes` are valid on **GROUP create only**. Omit them on SINGLE / VARIANT / BUNDLE or the API returns 400.

On **409** or “already exists”, look up by number and reuse. Do not create a second product.

VARIANT children: create as `type: "SINGLE"`, then recipe 4.

## 1b. PUT a scalar attribute (text / decimal / date)

```
PUT /products/{id}/attributes/{definitionId}?forceVla=false
{ "value": "475", "values": ["475"] }
```

PBC prefix `/api/pim`. Send `context` + `context-fallback: true`. Do **not** send `selectValueIds` for scalars — that body is for selects. 404 → `POST /products/{id}/attributes` `{ definitionId }`, then PUT again.

## 2. Upsert an attribute definition

1. `GET /definitions` (or list/filter by `number`).
2. Missing → `POST /definitions` (body includes `name`, `number`, `dataType`, `groupId`, …).
3. Exists but unassigned / wrong group → `GET /definitions/{id}`, then `PUT /definitions/{id}?validation=NONE` with current fields plus the target `groupId`.

Deleted attribute **groups** do not delete definitions. Reuse by number; never create a duplicate number.

## 3. Attach CLA

Definitions must already exist.

```
POST /catalogs/nodes/{categoryId}/attributes/{definitionId}?forceCla=false
{}

PATCH /catalogs/nodes/{categoryId}/attributes/{definitionId}?forceCla=false
{ "copyAttribute": true, "locked": false, "mandatory": true }
```

409 / “already exists” on POST → treat as reuse, still PATCH flags. Skip if the definition is not in the environment yet.

List attached CLAs: `GET /catalogs/nodes/attributes`. OpenAPI may mark `copyAttribute` / `locked` / `mandatory` deprecated; they are still what the UI uses.

Set `"mandatory": true` only when **every** product type in that category must have the value. Otherwise attach the CLA with `mandatory: false` (schema / optional fields) and add a completeness requirement scoped by type (recipe 11).

## 4. Assign GROUP under FAMILY, VARIANT under GROUP

```
POST /products/groups/append/by-ids
{ "familyVariantId": "<familyId>", "productIds": ["<groupId>", ...] }

POST /products/variants/append/by-ids?forceVla=true
{ "variantGroupId": "<groupId>", "productIds": ["<singleId>", ...] }
```

Conflict / “already” → continue. Batch ids (e.g. 100).

## 5. Set VLA flags on a GROUP

```
PUT /products/{groupId}/variants/attributes/{definitionId}?forceVla=true|false
{
  "copy": true,
  "definingAttributes": false,
  "locked": false,
  "mandatory": true
}
```

`copy` must be `true` if any other flag is true. `forceVla=true` overwrites child values; `false` fills empty only.

If the attribute is not yet on the GROUP, POST it onto `/products/{groupId}/attributes` first (ignore conflict), then PUT VLA.

## 6. Relate two products

```
POST /products/{fromId}/connections/products
{ "relationId": "<relationDefinitionId>", "to": "<toProductId>" }
```

Conflict → already linked. Relation **definitions** are created with `POST /relations` (`name`, `direction`, optional `reverseName`, `quantityEnabled`). Core connections have no typed attributes.

Drop one pair:

```
DELETE /products/{fromId}/connections/products/{relationId}/{toId}
```

Omit `context` / `context-fallback` on connection **writes** (POST and DELETE). 404 means it is already gone. `DELETE …/{relationId}` with no `toId` removes **every** target of that relation type — do not use that when only one pick should go.

## 6c. Read a product neighbourhood

`GET /products/{id}` (with `context`) includes `relatedProducts: [{ relationId, relatedId, reverse }]`. `reverse: true` means the other product points at this one. That list is enough to walk a graph without a second round-trip per relation type.

Per-relation page (ids only):

```
GET /products/{id}/connections/products/{relationId}?direction=DIRECT|REVERSE&page=0&pageSize=1000
```

Response is `{ data: [{ productId }] }`. Then GET each product for name/number/attributes. GET of this path accepts `context`; POST of the write path rejects it (see [gotchas.md](gotchas.md)).

Products in a catalog node:

```
GET /catalogs/nodes/{id}/products?page=0&pageSize=100
```

Returns `{ productId, productName }`. Resolve relation/definition ids with `POST /relations/list` and `POST /definitions/list` (`NUMBER_IN`).

## 6b. Relation attributes (v1-apps)

Not PIM core. Base: `https://api.<env>.bluestonepim.com/relation` (PBC: `/api/relation`). Create the core connection first (recipe 6).

1. Assign the definition to the relation type:

```
POST /relationDefinitions/relations/{relationId}/attributes
{ "level": "product", "attributeDefinitionId": "<defId>" }
```

`level: "relation"` = one value for the type on this product. `level: "product"` = per related pair.

2. Set the per-pair value:

```
PUT /products/{fromId}/relations/{relationId}/relatedProducts/{toId}/attributes/{attrDefId}
{ "values": ["..."], "reverse": false }
```

Completeness `MINIMUM_RELATION_CONNECTIONS` scores the core edge, not the relation-attribute value.

Read is **not** documented. Do not assume GET of the PUT path works. A GET of that path on TEST MAPI returned API Gateway 403. Walk the core connection (`relatedProducts` / connections GET) first; treat actor/reason as unread until a relation-attribute GET is confirmed.

## 7. Create a catalog node

```
POST /catalogs/nodes?validation=NAME
{ "name": "Range name", "number": "CAT-001", "parentId": "<optional>" }
```

Reuse by number. `parentId` omitted → root.

## 8. Read attributes for display (MAPI)

MAPI `GET /products/{id}/attributes` is raw IDs. Resolve:

1. Product attributes + `GET /definitions` in parallel.
2. `single_select` / `multi_select` → labels on `definition.restrictions.enum.values` (no extra calls).
3. `dictionary` → `GET /definitions/dictionary/{definitionId}/values/{valueId}` per unique pair. Label at `data.value.value[languageId]` (no `name` field, often no extra `data` wrapper).

Do not call the dictionary endpoint for selects (404).

## 9. Write a dictionary value from a PBC

1. `POST /definitions/dictionary/{definitionId}/values` `{ value: "Label" }` → id in `resource-id`.
2. `POST /products/{id}/attributes/dictionary/{definitionId}/values` with **full** `valueIds` (replace, not append).

## 10. Media

Upload/create via Media Bank (`/media-bank/assets/...`), then attach to the product. A source **filename** with no CDN base and no binary is not attachable. See [completeness.md](completeness.md).

For PBC `getAxiosInstance()` examples of less common endpoints, open [api-core.md](api-core.md) only for that path.

## 11. Completeness requirement (type-scoped)

Machine base: `https://api.<env>.bluestonepim.com/completeness-score`. PBC: `/api/completeness-score/`.

```
POST /requirements
{
  "requirementType": "ATTRIBUTE_HAS_VALUE",
  "params": { "definitionId": "<defId>" },
  "weight": 1
}
```

Id in `resource-id`. Then:

```
PUT /requirements/{id}/productType/GROUP/included
PUT /requirements/{id}/categories/{categoryId}/included
```

`includedProductTypes` may be sent on create. OpenAPI often lists only SINGLE/BUNDLE/GROUP/VARIANT — **FAMILY is valid**. Completeness scores editors; it does not replace CLA mandatory as a publish/sync gate.

## 12. Product-grid column setup (UI settings)

Machine base: `https://api.<env>.bluestonepim.com/ui-settings`. PBC: `/api/ui-settings/`. Same MAPI client credentials as PIM.

```
GET /columnsSetups/all
POST /columnsSetups/all
{
  "name": "Room offers",
  "isPublic": true,
  "service": "bluestone-pim",
  "entity": "PRODUCT",
  "owner": "user@example.com",
  "columns": [
    { "id": "status", "columnType": "status" },
    { "id": "name", "columnType": "name" },
    { "id": "number", "columnType": "number" },
    { "id": "type", "columnType": "producttype" },
    { "id": "score", "columnType": "score" },
    { "id": "<definitionId>", "columnType": "attribute" }
  ],
  "sortConfig": { "columnId": "number", "direction": "ASC" }
}
```

Create returns the setup in the **body** (including `id`), not only `resource-id`. `owner` is a PIM **user email** — reuse the org Default’s owner, or set it from a known user. `isDefault` is not on the create body — do not POST a second Default. `producttype` uses `id: "type"`. Attribute columns use the definition id. Match existing setups by `name` + `service` + `entity`; `PUT /columnsSetups/all/{id}` if the column list changed. See [gotchas.md](gotchas.md).

## 13. Excel / CSV product import (UI)

Headers on row 1. PIM maps by **header text**, not column order:

| Header | Maps to |
|---|---|
| `Number` | Product number (lookup; update if it exists) |
| `Name` | Product name |
| exact attribute **name** | That definition |

Do not put relation or join-key columns in a file that should not overwrite them. Turn **off** “Allow creating new attribute definitions” when the names already exist — a typo otherwise creates a duplicate definition. Import sessions are the job history; core connections are not written by an attribute-only file.
