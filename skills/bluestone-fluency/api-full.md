> **Lookup only — last resort.** Open the relevant heading, not this file from the top. Many types are names-only; do not guess field names. Confirmed shapes and prefixes: [gotchas.md](gotchas.md), [auth.md](auth.md).

# Bluestone PIM API — Developer Reference

Complete API reference for PBC plugin developers. All calls use `getAxiosInstance()` from `@bluestone-ext/plugin-framework`.

**Base URL:** `https://api.test.bluestonepim.com`
**Auth:** Handled automatically by `getAxiosInstance()` — never add tokens manually.
**Locale header:** Include `context: en` (or the locale key) and `context-fallback: true` on most PIM core calls.

> **IMPORTANT — Response body shapes:**
> This file documents endpoint paths and response *type names* but many type schemas are not fully detailed.
> **Do not guess field names.** If an endpoint's response body shape is not shown in the
> "Confirmed Response Shapes" section at the bottom of this file, flag to the developer
> that the schema needs verification via browser DevTools → Network tab before shipping.

---

## Table of Contents

1. [PIM Core](#pim-core-api) — Products, Attributes, Categories, Relations, Variants, Bundles, Assets, Catalog
2. [Search](#search) — Product search and scroll API
3. [Media Bank](#media-bank) — Media asset management
4. [Tasks](#tasks) — Workflow tasks
5. [History](#history) — Change history and audit log
6. [Completeness Score](#completeness-score) — Product completeness
7. [Labels](#labels) — Product labels
8. [Metadata](#metadata) — Metadata management
9. [External Notifications](#external-notifications) — Webhook subscriptions
10. [Public API Sync](#public-api-sync) — Sync state management
11. [Page](#page) — Page configuration API
12. [Global Settings](#global-settings) — Organization settings
13. [UI Settings](#ui-settings) — User UI preferences
14. [IDP](#idp) — Identity provider / user directory

---


## PIM Core API

> Base URL: `https://api.test.bluestonepim.com`  
> Headers: `context: <locale>` (default `en`), `context-fallback: true`


### Products

#### POST /products
*Create new product.*

**Permission:** `[{'accessType': 'ADD', 'module': 'PRODUCTS'}]`  
**Params:** `validation` (query), `context` (header)  
**Body:** `ProductCreateRequest`  
```typescript
const {data} = await getAxiosInstance().post(`/products`, body);
```

#### PUT /products/archive/by-ids
*Archive products by IDs.*

**Permission:** `[{'accessType': 'ARCHIVE', 'module': 'PRODUCTS'}]`  
**Body:** `ProductArchiveStateRequest`  
```typescript
await getAxiosInstance().put(`/products/archive/by-ids`, body);
```

#### POST /products/copy
*Create a copy of product.*

**Permission:** `[{'accessType': 'COPY', 'module': 'PRODUCTS'}]`  
**Params:** `context` (header)  
**Body:** `CopyProductRequest`  
```typescript
const {data} = await getAxiosInstance().post(`/products/copy`, body);
```

#### POST /products/cursor/views/all
*Get products using cursor with details from given views.*

**Permission:** `[{'accessType': 'DETAILS', 'module': 'PRODUCTS'}]`  
**Params:** `archiveState` (query), `context` (header), `context-fallback` (header)  
**Body:** `CursorWithViewsRequestDto`  
**Response:** `ListableWithCursorProductViewDto`  
```typescript
const {data} = await getAxiosInstance().post(`/products/cursor/views/all`, body);
```

#### POST /products/cursor/views/by-types
*Show list of products filtered by type with details from given views.*

**Permission:** `[{'accessType': 'DETAILS', 'module': 'PRODUCTS'}]`  
**Params:** `archiveState` (query), `context` (header), `context-fallback` (header)  
**Body:** `ProductViewsByTypeRequest`  
**Response:** `ListableWithCursorProductViewDto`  
```typescript
const {data} = await getAxiosInstance().post(`/products/cursor/views/by-types`, body);
```

#### POST /products/list/views/by-assets
*Show list of products filtered by asset ids with details from given views.*

**Permission:** `[{'accessType': 'DETAILS', 'module': 'PRODUCTS'}]`  
**Params:** `archiveState` (query), `context` (header), `context-fallback` (header)  
**Body:** `AssetIdListViewsRequestDto`  
**Response:** `ListableProductViewDto`  
```typescript
const {data} = await getAxiosInstance().post(`/products/list/views/by-assets`, body);
```

#### POST /products/list/views/by-ids
*Show list of products filtered by ids with details from given views.*

**Permission:** `[{'accessType': 'DETAILS', 'module': 'PRODUCTS'}]`  
**Params:** `archiveState` (query), `context` (header), `context-fallback` (header)  
**Body:** `ProductIdListViewsRequestDto`  
**Response:** `ListableProductViewDto`  
```typescript
const {data} = await getAxiosInstance().post(`/products/list/views/by-ids`, body);
```

#### POST /products/list/views/by-numbers
*Show list of products filtered by numbers with details from given views.*

**Permission:** `[{'accessType': 'DETAILS', 'module': 'PRODUCTS'}]`  
**Params:** `archiveState` (query), `context` (header), `context-fallback` (header)  
**Body:** `ProductNumberListViewsRequestDto`  
**Response:** `ListableProductViewDto`  
```typescript
const {data} = await getAxiosInstance().post(`/products/list/views/by-numbers`, body);
```

#### POST /products/states/by-ids
*[DEPRECATED; EOL 2026-11-10] Change products state by ids.*

**Permission:** `[{'accessType': 'UPDATE', 'module': 'PRODUCTS'}]`  
**Params:** `context` (header)  
**Body:** `UpdateProductStateRequest`  
```typescript
const {data} = await getAxiosInstance().post(`/products/states/by-ids`, body);
```

#### PUT /products/unarchive/by-ids
*Unarchive products by ids.*

**Permission:** `[{'accessType': 'ARCHIVE', 'module': 'PRODUCTS'}]`  
**Body:** `ProductArchiveStateRequest`  
```typescript
await getAxiosInstance().put(`/products/unarchive/by-ids`, body);
```

#### GET /products/{id}
*Show product details.*

**Permission:** `[{'accessType': 'DETAILS', 'module': 'PRODUCTS'}]`  
**Params:** `id` (path, required), `archiveState` (query), `context` (header), `context-fallback` (header)  
**Response:** `ProductAll`  
```typescript
const {data} = await getAxiosInstance().get(`/products/{id}`);
```

#### PATCH /products/{id}
*Update product main details, supports partial update.*

**Permission:** `[{'accessType': 'UPDATE', 'module': 'PRODUCTS'}]`  
**Params:** `id` (path, required), `context` (header)  
**Body:** `ProductMetadataUpdateRequest`  
```typescript
await getAxiosInstance().patch(`/products/{id}`, body);
```

#### POST /products/{id}/copy
*Create a copy of product.*

**Permission:** `[{'accessType': 'COPY', 'module': 'PRODUCTS'}]`  
**Params:** `id` (path, required), `context` (header)  
```typescript
const {data} = await getAxiosInstance().post(`/products/{id}/copy`, body);
```

#### POST /products/{id}/get/views
*Show product filtered by id with view.*

**Permission:** `[{'accessType': 'DETAILS', 'module': 'PRODUCTS'}]`  
**Params:** `id` (path, required), `archiveState` (query), `context` (header), `context-fallback` (header)  
**Body:** `ProductIdViewsRequestDto`  
**Response:** `ProductViewDto`  
```typescript
const {data} = await getAxiosInstance().post(`/products/{id}/get/views`, body);
```

#### GET /products/{id}/overview
*Show product overview.*

**Permission:** `[{'accessType': 'DETAILS', 'module': 'PRODUCTS'}]`  
**Params:** `id` (path, required), `archiveState` (query)  
**Response:** `ProductOverviewDto`  
```typescript
const {data} = await getAxiosInstance().get(`/products/{id}/overview`);
```

#### POST /products/{id}/{action}
*[DEPRECATED; EOL 2026-11-10] Change products state.*

**Permission:** `[{'accessType': 'UPDATE', 'module': 'PRODUCTS'}]`  
**Params:** `id` (path, required), `action` (path, required), `context` (header)  
```typescript
const {data} = await getAxiosInstance().post(`/products/{id}/{action}`, body);
```


### Products attributes

#### POST /products/attributes/add/by-ids
*Add attribute values in given set of products.*

**Permission:** `[{'accessType': 'ADD', 'module': 'ATTRIBUTES'}]`  
**Params:** `failOnAssignedAttribute` (query), `context` (header), `context-fallback` (header)  
**Body:** `SaveProductsAttributesByIdsRequest`  
```typescript
const {data} = await getAxiosInstance().post(`/products/attributes/add/by-ids`, body);
```

#### POST /products/attributes/by-ids
*Upsert attribute values in given set of products.*

**Permission:** `[{'accessType': 'UPDATE', 'module': 'ATTRIBUTES'}, {'accessType': 'ADD', 'module': 'ATTRIBUTES'}]`  
**Params:** `forceVla` (query), `context` (header), `context-fallback` (header)  
**Body:** `SaveProductsAttributesByIdsRequest`  
```typescript
const {data} = await getAxiosInstance().post(`/products/attributes/by-ids`, body);
```

#### PUT /products/attributes/by-ids
*Update attribute values in given set of products.*

**Permission:** `[{'accessType': 'UPDATE', 'module': 'ATTRIBUTES'}]`  
**Params:** `forceVla` (query), `failOnMissingAttribute` (query), `context` (header), `context-fallback` (header)  
**Body:** `SaveProductsAttributesByIdsRequest`  
```typescript
await getAxiosInstance().put(`/products/attributes/by-ids`, body);
```

#### POST /products/attributes/dictionary/append/by-ids
*Append dictionary value to products.*

**Permission:** `[{'accessType': 'UPDATE', 'module': 'ATTRIBUTES'}]`  
**Params:** `forceVla` (query), `failOnMissingAttribute` (query), `context` (header)  
**Body:** `AppendDictionaryValueRequest`  
```typescript
const {data} = await getAxiosInstance().post(`/products/attributes/dictionary/append/by-ids`, body);
```

#### POST /products/attributes/dictionary/deduct/by-ids
*Deduct dictionary value from products.*

**Permission:** `[{'accessType': 'UPDATE', 'module': 'ATTRIBUTES'}]`  
**Params:** `forceVla` (query), `failOnMissingAttribute` (query), `context` (header)  
**Body:** `DeductDictionaryValueRequest`  
```typescript
const {data} = await getAxiosInstance().post(`/products/attributes/dictionary/deduct/by-ids`, body);
```

#### POST /products/attributes/select/append/by-ids
*Append select/multiselect values to products.*

**Permission:** `[{'accessType': 'UPDATE', 'module': 'ATTRIBUTES'}]`  
**Params:** `forceVla` (query), `failOnMissingAttribute` (query), `context` (header)  
**Body:** `AppendSelectValuesToProductsAttributeRequest`  
```typescript
const {data} = await getAxiosInstance().post(`/products/attributes/select/append/by-ids`, body);
```

#### POST /products/attributes/select/deduct/by-ids
*Deduct select/multiselect values from products.*

**Permission:** `[{'accessType': 'UPDATE', 'module': 'ATTRIBUTES'}]`  
**Params:** `forceVla` (query), `failOnMissingAttribute` (query), `context` (header)  
**Body:** `DeductSelectValuesFromProductsAttributeRequest`  
```typescript
const {data} = await getAxiosInstance().post(`/products/attributes/select/deduct/by-ids`, body);
```

#### GET /products/{id}/attributes
*Retrieve the attributes associated with a product.*

**Permission:** `[{'accessType': 'LIST', 'module': 'ATTRIBUTES'}]`  
**Params:** `id` (path, required), `archiveState` (query), `context` (header), `context-fallback` (header)  
**Response:** `ListableAttributeValueAll`  
```typescript
const {data} = await getAxiosInstance().get(`/products/{id}/attributes`);
```

#### POST /products/{id}/attributes
*Add an attribute to a product.*

**Permission:** `[{'accessType': 'ADD', 'module': 'ATTRIBUTES'}]`  
**Params:** `id` (path, required), `context` (header), `context-fallback` (header)  
**Body:** `CreateSimpleAttributeRequest`  
```typescript
const {data} = await getAxiosInstance().post(`/products/{id}/attributes`, body);
```

#### PUT /products/{id}/attributes
*Upsert many attributes on a product.*

**Permission:** `[{'accessType': 'UPDATE', 'module': 'ATTRIBUTES'}, {'accessType': 'ADD', 'module': 'ATTRIBUTES'}]`  
**Params:** `id` (path, required), `forceVla` (query), `context` (header), `context-fallback` (header)  
**Body:** `array`  
```typescript
await getAxiosInstance().put(`/products/{id}/attributes`, body);
```

#### POST /products/{id}/attributes/column
*Add column attribute to product.*

**Permission:** `[{'accessType': 'ADD', 'module': 'ATTRIBUTES'}]`  
**Params:** `id` (path, required), `context` (header), `context-fallback` (header)  
**Body:** `ColumnAttributeValueAddRequest`  
```typescript
const {data} = await getAxiosInstance().post(`/products/{id}/attributes/column`, body);
```

#### PUT /products/{id}/attributes/column/{definitionId}
*Update column attribute of product.*

**Permission:** `[{'accessType': 'UPDATE', 'module': 'ATTRIBUTES'}]`  
**Params:** `id` (path, required), `definitionId` (path, required), `forceVla` (query), `context` (header)  
**Body:** `ColumnAttributeValueDto`  
```typescript
await getAxiosInstance().put(`/products/{id}/attributes/column/{definitionId}`, body);
```

#### POST /products/{id}/attributes/dictionary
*Add dictionary attribute to product.*

**Permission:** `[{'accessType': 'ADD', 'module': 'ATTRIBUTES'}]`  
**Params:** `id` (path, required), `context` (header), `context-fallback` (header)  
**Body:** `DictionaryAttributeValueAddRequest`  
```typescript
const {data} = await getAxiosInstance().post(`/products/{id}/attributes/dictionary`, body);
```

#### POST /products/{id}/attributes/dictionary/{definitionId}/values
*Set values for dictionary attribute of product.*

**Permission:** `[{'accessType': 'UPDATE', 'module': 'ATTRIBUTES'}]`
**Params:** `id` (path, required), `definitionId` (path, required), `forceVla` (query, default false), `context` (header)
**Body:** `SetDictionaryValuesRequest` — confirmed shape: `{ valueIds: string[] }`
**⚠️ Replaces the full set** — send existing IDs + new ID together, not just the new one.
**Response:** 204 No Content
```typescript
// Confirmed from DevTools — body is { valueIds: string[] }, replaces full set
await getAxiosInstance().post(
    `/api/pim/products/${productId}/attributes/dictionary/${definitionId}/values`,
    { valueIds: [...existingValueIds, newValueId] },
    { headers, params: { forceVla: false } }
);
```

#### POST /definitions/dictionary/{id}/values
*Create a new value in a dictionary attribute definition.*

**Permission:** `ATTRIBUTE_DEFINITIONS`
**Params:** `id` (path, required), `context` (header, sets language for the label)
**Body:** `{ value: string (required), number?: string, metadata?: string }`
**Response:** 201 — new value ID returned in `Resource-Id` response header (not in body)
```typescript
const res = await getAxiosInstance().post(
    `/api/pim/definitions/dictionary/${definitionId}/values`,
    { value: labelText },
    { headers }
);
const newValueId = res.headers['resource-id']; // header key is lowercase
```

#### PATCH /definitions/dictionary/{id}/values/{valueId}
*Update the label or metadata of an existing dictionary value.*

**Permission:** `ATTRIBUTE_DEFINITIONS`
**Params:** `id` (path), `valueId` (path), `context` (header, sets language for the label)
**Body:** partial update — wrap each field in `{ value: ... }`:
```typescript
// { value: { value: "new label" }, number?: { value: "new-number" }, metadata?: { value: "hex" } }
await getAxiosInstance().patch(
    `/api/pim/definitions/dictionary/${definitionId}/values/${valueId}`,
    { value: { value: newLabel } },
    { headers }
);
// Response: 204 No Content
```

#### DELETE /definitions/dictionary/{id}/values/{valueId}
*Permanently delete a value from a dictionary attribute definition.*

**Permission:** `ATTRIBUTE_DEFINITIONS`
**Params:** `id` (path), `valueId` (path)
**Response:** 202 Accepted (async — deletion may not be immediate)
```typescript
await getAxiosInstance().delete(
    `/api/pim/definitions/dictionary/${definitionId}/values/${valueId}`
);
```

#### PUT /definitions/dictionary/{id}
*Replace a dictionary attribute definition (full update).*

**Permission:** `ATTRIBUTE_DEFINITIONS`
**Body:** `DictionaryAttributeDefinitionDto` — full definition object
**Response:** 204 No Content

#### PATCH /definitions/dictionary/{id}
*Partial update — selectedValuesLimit or dictionaryValuesMetadataType only.*

**Body:** `{ selectedValuesLimit?: { value: number|null }, dictionaryValuesMetadataType?: { value: string } }`
**Response:** 204 No Content

#### POST /products/{id}/attributes/matrix
*Add matrix attribute to product.*

**Permission:** `[{'accessType': 'ADD', 'module': 'ATTRIBUTES'}]`  
**Params:** `id` (path, required), `context` (header), `context-fallback` (header)  
**Body:** `MatrixAttributeValueAddRequest`  
```typescript
const {data} = await getAxiosInstance().post(`/products/{id}/attributes/matrix`, body);
```

#### PUT /products/{id}/attributes/matrix/{definitionId}
*Update matrix attribute of product.*

**Permission:** `[{'accessType': 'UPDATE', 'module': 'ATTRIBUTES'}]`  
**Params:** `id` (path, required), `definitionId` (path, required), `forceVla` (query), `context` (header)  
**Body:** `MatrixAttributeValueDto`  
```typescript
await getAxiosInstance().put(`/products/{id}/attributes/matrix/{definitionId}`, body);
```

#### DELETE /products/{id}/attributes/{definitionId}
*Delete product attribute.*

**Permission:** `[{'accessType': 'DELETE', 'module': 'ATTRIBUTES'}]`  
**Params:** `id` (path, required), `definitionId` (path, required)  
```typescript
await getAxiosInstance().delete(`/products/{id}/attributes/{definitionId}`);
```

#### PUT /products/{id}/attributes/{definitionId}
*Update product attribute.*

**Permission:** `[{'accessType': 'UPDATE', 'module': 'ATTRIBUTES'}]`  
**Params:** `id` (path, required), `definitionId` (path, required), `forceVla` (query), `context` (header)  
**Body:** `AttributeValueValues`  
```typescript
await getAxiosInstance().put(`/products/{id}/attributes/{definitionId}`, body);
```

#### GET /products/{id}/groupedAttributes
*Get grouped product attributes.*

**Permission:** `[{'accessType': 'LIST', 'module': 'ATTRIBUTES'}]`  
**Params:** `id` (path, required), `archiveState` (query), `context` (header), `context-fallback` (header)  
**Response:** `ListableGroupedAttributeValuesDto`  
```typescript
const {data} = await getAxiosInstance().get(`/products/{id}/groupedAttributes`);
```


### Products assets

#### GET /products/{id}/assets
*Retrieve the assets associated with a product.*

**Permission:** `[{'accessType': 'DETAILS', 'module': 'PRODUCTS'}]`  
**Params:** `id` (path, required), `archiveState` (query), `page` (query), `pageSize` (query)  
**Response:** `ListableString`  
```typescript
const {data} = await getAxiosInstance().get(`/products/{id}/assets`);
```

#### POST /products/{id}/assets
*Associate assets with a product.*

**Permission:** `[{'accessType': 'UPDATE', 'module': 'PRODUCTS'}]`  
**Params:** `id` (path, required)  
**Body:** `AssociateProductAssetsRequest`  
```typescript
const {data} = await getAxiosInstance().post(`/products/{id}/assets`, body);
```

#### DELETE /products/{id}/assets/{assetId}
*Disassociate an asset from a product.*

**Permission:** `[{'accessType': 'DELETE', 'module': 'PRODUCTS'}]`  
**Params:** `id` (path, required), `assetId` (path, required)  
```typescript
await getAxiosInstance().delete(`/products/{id}/assets/{assetId}`);
```

#### POST /products/{id}/assets/{assetId}
*Associate an asset with a product.*

**Permission:** `[{'accessType': 'UPDATE', 'module': 'PRODUCTS'}]`  
**Params:** `id` (path, required), `assetId` (path, required)  
```typescript
const {data} = await getAxiosInstance().post(`/products/{id}/assets/{assetId}`, body);
```


### Products categories

#### POST /products/categories/by-ids
*Add products as children of category.*

**Permission:** `[{'accessType': 'PRODUCT_ASSOCIATE', 'module': 'CATALOG'}]`  
**Body:** `AssignProductsToCategoryRequest`  
```typescript
const {data} = await getAxiosInstance().post(`/products/categories/by-ids`, body);
```

#### GET /products/{id}/categories
*List of category IDs the product resides in.*

**Permission:** `[{'accessType': 'DETAILS', 'module': 'PRODUCTS'}]`  
**Params:** `id` (path, required), `archiveState` (query), `page` (query), `pageSize` (query)  
**Response:** `ListableString`  
```typescript
const {data} = await getAxiosInstance().get(`/products/{id}/categories`);
```

#### POST /products/{id}/categories
*Add product to categories.*

**Permission:** `[{'accessType': 'PRODUCT_ASSOCIATE', 'module': 'CATALOG'}]`  
**Params:** `id` (path, required)  
**Body:** `CategoryReferenceRequest`  
```typescript
const {data} = await getAxiosInstance().post(`/products/{id}/categories`, body);
```

#### DELETE /products/{id}/categories/{categoryId}
*Remove product from category.*

**Permission:** `[{'accessType': 'PRODUCT_ASSOCIATE', 'module': 'CATALOG'}]`  
**Params:** `id` (path, required), `categoryId` (path, required)  
```typescript
await getAxiosInstance().delete(`/products/{id}/categories/{categoryId}`);
```


### Products relations

#### GET /products/{id}/connections
*Show all relations with directions for the product.*

**Permission:** `[{'accessType': 'DETAILS', 'module': 'PRODUCTS'}]`  
**Params:** `id` (path, required), `page` (query), `pageSize` (query)  
**Response:** `ListableProductConnectionInfoResponse`  
```typescript
const {data} = await getAxiosInstance().get(`/products/{id}/connections`);
```

#### POST /products/{id}/connections/products
*Create new relation between two products.*

**Permission:** `[{'accessType': 'ADD', 'module': 'CONNECTIONS'}]`  
**Params:** `id` (path, required)  
**Body:** `ProductConnectionRequest`  
```typescript
const {data} = await getAxiosInstance().post(`/products/{id}/connections/products`, body);
```

#### DELETE /products/{id}/connections/products/{relationId}
*Delete all product relations based on a specified product and relation.*

**Permission:** `[{'accessType': 'DELETE', 'module': 'CONNECTIONS'}]`  
**Params:** `id` (path, required), `relationId` (path, required)  
```typescript
await getAxiosInstance().delete(`/products/{id}/connections/products/{relationId}`);
```

#### GET /products/{id}/connections/products/{relationId}
*Retrieve related products for given product and relation.*

**Permission:** `[{'accessType': 'DETAILS', 'module': 'CONNECTIONS'}]`  
**Params:** `id` (path, required), `relationId` (path, required), `direction` (query, required), `page` (query)  
**Response:** `ListableProductConnectionProductResponse`  
```typescript
const {data} = await getAxiosInstance().get(`/products/{id}/connections/products/{relationId}`);
```

#### POST /products/{id}/connections/products/{relationId}
*Create new relations between multiple products.*

**Permission:** `[{'accessType': 'ADD', 'module': 'CONNECTIONS'}]`  
**Params:** `id` (path, required), `relationId` (path, required)  
**Body:** `array`  
```typescript
const {data} = await getAxiosInstance().post(`/products/{id}/connections/products/{relationId}`, body);
```

#### DELETE /products/{id}/connections/products/{relationId}/bulk
*Remove multiple product relations from a specified product and relation.*

**Permission:** `[{'accessType': 'DELETE', 'module': 'CONNECTIONS'}]`  
**Params:** `id` (path, required), `relationId` (path, required)  
```typescript
await getAxiosInstance().delete(`/products/{id}/connections/products/{relationId}/bulk`);
```

#### DELETE /products/{id}/connections/products/{relationId}/{connectedProductId}
*Delete relation between two products.*

**Permission:** `[{'accessType': 'DELETE', 'module': 'CONNECTIONS'}]`  
**Params:** `id` (path, required), `relationId` (path, required), `connectedProductId` (path, required)  
```typescript
await getAxiosInstance().delete(`/products/{id}/connections/products/{relationId}/{connectedProductId}`);
```


### Products labels

#### DELETE /products/labels/by-ids
*Remove product label from the set of products.*

**Permission:** `[{'accessType': 'DELETE', 'module': 'PRODUCTS'}]`  
```typescript
await getAxiosInstance().delete(`/products/labels/by-ids`);
```

#### POST /products/labels/by-ids
*Add product label to the set of products.*

**Permission:** `[{'accessType': 'UPDATE', 'module': 'PRODUCTS'}]`  
**Body:** `ModifyLabelInProductsRequestDto`  
```typescript
const {data} = await getAxiosInstance().post(`/products/labels/by-ids`, body);
```

#### DELETE /products/{id}/labels
*Remove labels from the product.*

**Permission:** `[{'accessType': 'DELETE', 'module': 'PRODUCTS'}]`  
**Params:** `id` (path, required)  
```typescript
await getAxiosInstance().delete(`/products/{id}/labels`);
```

#### GET /products/{id}/labels
*Retrieve product labels.*

**Permission:** `[{'accessType': 'DETAILS', 'module': 'PRODUCTS'}]`  
**Params:** `id` (path, required), `archiveState` (query), `page` (query), `pageSize` (query)  
**Response:** `ListableString`  
```typescript
const {data} = await getAxiosInstance().get(`/products/{id}/labels`);
```

#### POST /products/{id}/labels
*Add product labels to product.*

**Permission:** `[{'accessType': 'UPDATE', 'module': 'PRODUCTS'}]`  
**Params:** `id` (path, required)  
**Body:** `LabelReferenceRequest`  
```typescript
const {data} = await getAxiosInstance().post(`/products/{id}/labels`, body);
```

#### DELETE /products/{id}/labels/{labelId}
*Remove product label from product.*

**Permission:** `[{'accessType': 'DELETE', 'module': 'PRODUCTS'}]`  
**Params:** `id` (path, required), `labelId` (path, required)  
```typescript
await getAxiosInstance().delete(`/products/{id}/labels/{labelId}`);
```


### Products bundles

#### POST /products/{id}/bundles
*Create product bundle.*

**Permission:** `[{'accessType': 'UPDATE', 'module': 'PRODUCTS'}]`  
**Params:** `id` (path, required)  
```typescript
const {data} = await getAxiosInstance().post(`/products/{id}/bundles`, body);
```

#### POST /products/{id}/bundles/{complementaryProductId}
*Add product to bundle.*

**Permission:** `[{'accessType': 'UPDATE', 'module': 'PRODUCTS'}]`  
**Params:** `id` (path, required), `complementaryProductId` (path, required)  
**Body:** `ProductBundleRequest`  
```typescript
const {data} = await getAxiosInstance().post(`/products/{id}/bundles/{complementaryProductId}`, body);
```


### Products templates

#### GET /products/templates
*List all product templates.*

**Permission:** `[{'accessType': 'DETAILS', 'module': 'PRODUCTS'}]`  
**Params:** `page` (query), `pageSize` (query)  
**Response:** `ListableProductTemplateResponse`  
```typescript
const {data} = await getAxiosInstance().get(`/products/templates`);
```

#### GET /products/templates/{productId}
*Show details of product template.*

**Permission:** `[{'accessType': 'DETAILS', 'module': 'PRODUCTS'}]`  
**Params:** `productId` (path, required)  
**Response:** `ProductTemplateResponse`  
```typescript
const {data} = await getAxiosInstance().get(`/products/templates/{productId}`);
```


### Product variants

#### POST /products/variants/append/by-ids
*Assign multiple product variants to a variant group.*

**Permission:** `[{'accessType': 'UPDATE', 'module': 'PRODUCTS'}]`  
**Params:** `forceVla` (query)  
**Body:** `AddProductVariantsRequest`  
```typescript
const {data} = await getAxiosInstance().post(`/products/variants/append/by-ids`, body);
```

#### POST /products/{id}/variants
*Create variant group.*

**Permission:** `[{'accessType': 'ADD', 'module': 'PRODUCTS'}]`  
**Params:** `id` (path, required)  
```typescript
const {data} = await getAxiosInstance().post(`/products/{id}/variants`, body);
```

#### GET /products/{id}/variants/attributes/{definitionId}
*Retrieve variant level attribute (VLA).*

**Permission:** `[{'accessType': 'DETAILS', 'module': 'ATTRIBUTES'}]`  
**Params:** `id` (path, required), `definitionId` (path, required)  
**Response:** `ProductVariantAttributeDto`  
```typescript
const {data} = await getAxiosInstance().get(`/products/{id}/variants/attributes/{definitionId}`);
```

#### PUT /products/{id}/variants/attributes/{definitionId}
*Update variant level attribute (VLA).*

**Permission:** `[{'accessType': 'UPDATE', 'module': 'ATTRIBUTES'}]`  
**Params:** `id` (path, required), `definitionId` (path, required), `forceVla` (query)  
**Body:** `ProductVariantAttributeDto`  
```typescript
await getAxiosInstance().put(`/products/{id}/variants/attributes/{definitionId}`, body);
```

#### PUT /products/{id}/variants/{variantProductId}
*Assign product variant.*

**Permission:** `[{'accessType': 'UPDATE', 'module': 'PRODUCTS'}]`  
**Params:** `id` (path, required), `variantProductId` (path, required), `forceVla` (query)  
```typescript
await getAxiosInstance().put(`/products/{id}/variants/{variantProductId}`, body);
```

#### PUT /products/{id}/variants/{variantProductId}/order
*Update product variant order.*

**Permission:** `[{'accessType': 'UPDATE', 'module': 'PRODUCTS'}]`  
**Params:** `id` (path, required), `variantProductId` (path, required), `targetPosition` (query, required)  
```typescript
await getAxiosInstance().put(`/products/{id}/variants/{variantProductId}/order`, body);
```


### Catalog nodes

#### GET /catalogs/nodes/attributeDefinition/{attributeDefinitionId}
*List all catalog nodes/categories that use the attribute definition in a Category Level Attribute (CLA).*

**Permission:** `[{'accessType': 'DETAILS', 'module': 'CATALOG'}]`  
**Params:** `attributeDefinitionId` (path, required), `page` (query), `pageSize` (query), `archiveState` (query)  
**Response:** `ListableCategoryBasicResponse`  
```typescript
const {data} = await getAxiosInstance().get(`/catalogs/nodes/attributeDefinition/{attributeDefinitionId}`);
```

#### GET /catalogs/nodes/{id}
*Describe catalog node/category.*

**Permission:** `[{'accessType': 'DETAILS', 'module': 'CATALOG'}]`  
**Params:** `id` (path, required), `archiveState` (query), `context` (header), `context-fallback` (header)  
**Response:** `CategoryBasicResponse`  
```typescript
const {data} = await getAxiosInstance().get(`/catalogs/nodes/{id}`);
```

#### GET /catalogs/nodes/{id}/children
*List catalog node/category child nodes/categories.*

**Permission:** `[{'accessType': 'DETAILS', 'module': 'CATALOG'}]`  
**Params:** `id` (path, required), `archiveState` (query), `page` (query), `pageSize` (query)  
**Response:** `ListableCategoryBasicResponse`  
```typescript
const {data} = await getAxiosInstance().get(`/catalogs/nodes/{id}/children`);
```

#### GET /catalogs/nodes/{id}/path
*List catalog node/category path to root.*

**Permission:** `[{'accessType': 'DETAILS', 'module': 'CATALOG'}]`  
**Params:** `id` (path, required), `archiveState` (query)  
**Response:** `ListableString`  
```typescript
const {data} = await getAxiosInstance().get(`/catalogs/nodes/{id}/path`);
```

#### GET /catalogs/nodes/{id}/products
*List catalog node/category products.*

**Permission:** `[{'accessType': 'DETAILS', 'module': 'CATALOG'}]`  
**Params:** `id` (path, required), `page` (query), `pageSize` (query), `archiveState` (query)  
**Response:** `ListableCategoryProductResponse`  
```typescript
const {data} = await getAxiosInstance().get(`/catalogs/nodes/{id}/products`);
```


### Catalogs

#### GET /catalogs
*List catalogs.*

**Permission:** `[{'accessType': 'LIST', 'module': 'CATALOG'}]`  
**Params:** `page` (query), `pageSize` (query), `archiveState` (query), `context` (header)  
**Response:** `ListableCategoryBasicResponse`  
```typescript
const {data} = await getAxiosInstance().get(`/catalogs`);
```

#### GET /catalogs/{id}/nodes
*List all nodes/categories from catalog.*

**Permission:** `[{'accessType': 'DETAILS', 'module': 'CATALOG'}]`  
**Params:** `id` (path, required), `archiveState` (query), `context` (header), `context-fallback` (header)  
**Response:** `CategoryWithChildrenResponse`  
```typescript
const {data} = await getAxiosInstance().get(`/catalogs/{id}/nodes`);
```


### Catalog node assets

#### GET /catalogs/nodes/assets/{id}
*List categories associated with asset.*

**Permission:** `[{'accessType': 'LIST', 'module': 'CATALOG'}]`  
**Params:** `id` (path, required), `page` (query), `pageSize` (query)  
**Response:** `ListableCategoryBasicResponse`  
```typescript
const {data} = await getAxiosInstance().get(`/catalogs/nodes/assets/{id}`);
```

#### GET /catalogs/nodes/{id}/assets
*List assets associated with category.*

**Permission:** `[{'accessType': 'DETAILS', 'module': 'CATALOG'}]`  
**Params:** `id` (path, required)  
**Response:** `ListableString`  
```typescript
const {data} = await getAxiosInstance().get(`/catalogs/nodes/{id}/assets`);
```


### Attribute definitions

#### GET /definitions
*List all attribute definitions.*

**Permission:** `[{'accessType': 'LIST', 'module': 'ATTRIBUTE_DEFINITIONS'}]`  
**Params:** `page` (query), `pageSize` (query), `excludeToBeRemoved` (query), `context` (header)  
**Response:** `ListableAttributeDefinitionResponse`  
```typescript
const {data} = await getAxiosInstance().get(`/definitions`);
```

#### GET /definitions/dictionary/{id}/values/{valueId}
*Show details of dictionary attribute value.*

**Permission:** `[{'accessType': 'DETAILS', 'module': 'ATTRIBUTE_DEFINITIONS'}]`  
**Params:** `id` (path, required), `valueId` (path, required), `context` (header), `context-fallback` (header)  
**Response:** `DictionaryAttributeResponse`  
```typescript
const {data} = await getAxiosInstance().get(`/definitions/dictionary/{id}/values/{valueId}`);
```

#### GET /definitions/simple
*List simple attribute definitions.*

**Permission:** `[{'accessType': 'LIST', 'module': 'ATTRIBUTE_DEFINITIONS'}]`  
**Params:** `page` (query), `pageSize` (query), `excludeToBeRemoved` (query), `context` (header)  
**Response:** `ListableAttributeDefinitionResponse`  
```typescript
const {data} = await getAxiosInstance().get(`/definitions/simple`);
```

#### GET /definitions/{id}
*Show details of attribute definition.*

**Permission:** `[{'accessType': 'DETAILS', 'module': 'ATTRIBUTE_DEFINITIONS'}]`  
**Params:** `id` (path, required), `context` (header), `context-fallback` (header)  
**Response:** `AttributeDefinitionResponse`  
```typescript
const {data} = await getAxiosInstance().get(`/definitions/{id}`);
```

#### GET /definitions/{id}/products
*List all products that use the simple attribute definition.*

**Permission:** `[{'accessType': 'LIST', 'module': 'ATTRIBUTE_DEFINITIONS'}]`  
**Params:** `id` (path, required), `page` (query), `pageSize` (query), `archiveState` (query)  
**Response:** `ListableProductWithAttributeValuesResponse`  
```typescript
const {data} = await getAxiosInstance().get(`/definitions/{id}/products`);
```


### Attribute groups

#### GET /attributeGroups
*List all attribute groups.*

**Permission:** `[{'accessType': 'LIST', 'module': 'ATTRIBUTE_DEFINITIONS'}]`  
**Params:** `page` (query), `pageSize` (query), `context` (header), `context-fallback` (header)  
**Response:** `ListableAttributeGroupResponse`  
```typescript
const {data} = await getAxiosInstance().get(`/attributeGroups`);
```

#### GET /attributeGroups/other/definitions
*List definitions that are not in any group.*

**Permission:** `[{'accessType': 'LIST', 'module': 'ATTRIBUTE_DEFINITIONS'}]`  
**Params:** `page` (query), `pageSize` (query), `context` (header), `context-fallback` (header)  
**Response:** `ListableAttributeDefinitionResponse`  
```typescript
const {data} = await getAxiosInstance().get(`/attributeGroups/other/definitions`);
```

#### GET /attributeGroups/rootGroup
*Get attribute root group.*

**Permission:** `[{'accessType': 'DETAILS', 'module': 'ATTRIBUTE_DEFINITIONS'}]`  
**Params:** `context` (header), `context-fallback` (header)  
**Response:** `AttributeGroupResponse`  
```typescript
const {data} = await getAxiosInstance().get(`/attributeGroups/rootGroup`);
```

#### GET /attributeGroups/{id}/definitions
*List definitions in attribute group.*

**Permission:** `[{'accessType': 'DETAILS', 'module': 'ATTRIBUTE_DEFINITIONS'}]`  
**Params:** `id` (path, required), `page` (query), `pageSize` (query), `context` (header)  
**Response:** `ListableAttributeDefinitionResponse`  
```typescript
const {data} = await getAxiosInstance().get(`/attributeGroups/{id}/definitions`);
```


### Category attributes

#### GET /catalogs/nodes/{id}/category-attributes
*List all Catalog or Category attributes.*

**Permission:** `[{'accessType': 'DETAILS', 'module': 'CATALOG'}]`  
**Params:** `id` (path, required), `context` (header), `context-fallback` (header)  
**Response:** `ListableLocalCategoryAttributeDto`  
```typescript
const {data} = await getAxiosInstance().get(`/catalogs/nodes/{id}/category-attributes`);
```


### Category level attributes

#### GET /catalogs/nodes/attributes
*List all Category Level Attributes (CLA).*

**Permission:** `[{'accessType': 'LIST', 'module': 'ATTRIBUTES'}]`  
**Params:** `page` (query), `pageSize` (query), `archiveState` (query), `value` (query)  
**Response:** `ListableCategoryAttributeBaseMetadataResponse`  
```typescript
const {data} = await getAxiosInstance().get(`/catalogs/nodes/attributes`);
```

#### GET /catalogs/nodes/{id}/attributes
*List only Category Level Attributes (CLA) attached to given catalog node/category.*

**Permission:** `[{'accessType': 'LIST', 'module': 'ATTRIBUTES'}]`  
**Params:** `id` (path, required), `archiveState` (query), `context` (header), `context-fallback` (header)  
**Response:** `ListableCategoryAttributeMetadataResponse`  
```typescript
const {data} = await getAxiosInstance().get(`/catalogs/nodes/{id}/attributes`);
```


### Relations

#### GET /relations
*Show all relation definitions.*

**Permission:** `[{'accessType': 'LIST', 'module': 'RELATIONS'}]`  
**Params:** `page` (query), `pageSize` (query), `context` (header), `context-fallback` (header)  
**Response:** `ListableRelationResponse`  
```typescript
const {data} = await getAxiosInstance().get(`/relations`);
```

#### GET /relations/{id}
*Show relation definition details.*

**Permission:** `[{'accessType': 'DETAILS', 'module': 'RELATIONS'}]`  
**Params:** `id` (path, required), `context` (header), `context-fallback` (header)  
**Response:** `RelationResponse`  
```typescript
const {data} = await getAxiosInstance().get(`/relations/{id}`);
```

#### GET /relations/{id}/products/connections
*Show all product relations using relation definition.*

**Permission:** `[{'accessType': 'DETAILS', 'module': 'RELATIONS'}]`  
**Params:** `id` (path, required), `page` (query), `pageSize` (query), `context` (header)  
**Response:** `ListableProductConnectionResponse`  
```typescript
const {data} = await getAxiosInstance().get(`/relations/{id}/products/connections`);
```


---

## Search

Base URL: `https://api.test.bluestonepim.com/search`

---

### POST /search/products/search

**Tags:** Search
**Permission:** `module=PRODUCTS, accessType=DETAILS`
**Key params:**
- `archiveState` (query, optional, enum: `ACTIVE` | `ARCHIVED`) — filter by archive state, default active
- `context` (header, optional, string, default `"en"`) — localization context
- `context-fallback` (header, optional, boolean, default `true`) — fall back to default locale if value missing in context

**Request body:** `PimProductSearchRequest`
- `page` (int32, required, 0–10000) — zero-based page index
- `pageSize` (int32, required, 1–100) — results per page
- `column` (string, optional, enum: `PRODUCT_NAME` | `PRODUCT_NUMBER` | `PRODUCT_TYPE` | `ATTRIBUTE` | `LAST_UPDATE` | `CREATE_DATE`) — sort column
- `sortDirection` (string, optional, enum: `ASC` | `DESC`)
- `attributeIdToSortBy` (string, optional) — required when `column=ATTRIBUTE`
- `typeFilter` (TypeFilter, optional) — filter by a single product type and group IDs
- `typesFilter` (TypesFilter, optional) — filter by multiple product types
- `baseFilters` (BaseFilter[], optional) — name/number/description/ID text filters
- `attributeFilters` (AttributeFilter[], optional) — attribute value filters
- `categoryFilters` (CategoryFilter[], optional) — category membership filters
- `labelFilters` (LabelFilter[], optional) — label filters
- `publishStateFilter` (PublishStateFilter, optional) — publication status filter
- `validationStatusFilter` (ValidationStatusFilter, optional) — validation status filter
- `assetFilters` (AssetFilter[], optional) — asset count/assignment filters
- `variantFilters` (VariantFilter[], optional) — variant count filters
- `bundleFilters` (BundleFilter[], optional) — bundle count filters
- `relationsFilter` (RelationsFilter, optional) — relation filters
- `productCreateDateFilter` (CreateDateFilter[], optional) — absolute create date range
- `productCreateDateRelativeFilter` (CreateDateRelativeFilter[], optional) — relative create date
- `productLastUpdateFilter` (LastUpdateFilter[], optional) — absolute last-update range
- `productLastUpdateRelativeFilter` (LastUpdateRelativeFilter[], optional) — relative last-update
- `scoreFilter` (ScoreFilter, optional) — completeness score range
- `scoreRequirementResultFilters` (ScoreRequirementResultFilter[], optional) — failed requirements
- `outOfSyncFilter` (OutOfSyncFilter, optional) — out-of-sync state
- `lastSyncStateFilter` (LastSyncStateFilter, optional) — last sync result

**Response:** `ProductSearchResponse` — `data` (array of `{ id: string }`)

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().post(
  `/api/search/products/search`,
  {
    page: 0,
    pageSize: 50,
    column: "PRODUCT_NAME",
    sortDirection: "ASC",
    attributeFilters: [
      {
        attributeDefinitionId: "attr-uuid",
        type: "EQUALS",
        values: ["Blue"],
      },
    ],
    publishStateFilter: { type: "IN", publishStates: ["CONNECTED"] },
  },
  { params: { archiveState: "ACTIVE" }, headers: { context: "en" } }
);
// data.data is { id: string }[]
```

---

### POST /search/products/scroll/search

**Tags:** Search
**Permission:** `module=PRODUCTS, accessType=DETAILS`
**Key params:**
- `archiveState` (query, optional, enum: `ACTIVE` | `ARCHIVED`)
- `context` (header, optional, string, default `"en"`)
- `context-fallback` (header, optional, boolean, default `true`)

**Request body:** `PimProductSearchScrollRequest` (two phases)

Initial request:
- `pageSize` (int32, required, 1–100)
- Same filter fields as `/products/search`
- `scrollId` omitted

Continuation request:
- `pageSize` (int32, required)
- `scrollId` (string, required) — token from previous response

Note: scroll context expires after 10 seconds of inactivity.

**Response:** `ProductSearchScrollResponse` — `data` (`{ id: string }[]`), `scrollId` (string, use in next page call, null when exhausted)

**PBC usage:**
```typescript
// Initial call
const { data: first } = await getAxiosInstance().post(
  `/api/search/products/scroll/search`,
  {
    pageSize: 100,
    publishStateFilter: { type: "IN", publishStates: ["CONNECTED"] },
  },
  { params: { archiveState: "ACTIVE" } }
);
let scrollId = first.scrollId;

// Subsequent pages
while (scrollId) {
  const { data: page } = await getAxiosInstance().post(
    `/api/search/products/scroll/search`,
    { pageSize: 100, scrollId }
  );
  // process page.data
  scrollId = page.scrollId ?? null;
}
```

---

### POST /search/products/count

**Tags:** Search
**Permission:** `module=PRODUCTS, accessType=DETAILS`
**Key params:**
- `archiveState` (query, optional, enum: `ACTIVE` | `ARCHIVED`)
- `context` (header, optional, string, default `"en"`)
- `context-fallback` (header, optional, boolean, default `true`)

**Request body:** `PimProductCountRequest` — same filter fields as `/products/search` (no pagination or sort fields)

**Response:** `ProductCountResponse` — `count` (int64)

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().post(
  `/api/search/products/count`,
  {
    labelFilters: [{ type: "SOME", labelsIds: ["label-uuid"] }],
    validationStatusFilter: { type: "IN", validationStatus: ["INVALID"] },
  },
  { params: { archiveState: "ACTIVE" } }
);
console.log(data.count);
```

---

### POST /search/assets/search

**Tags:** Search
**Permission:** `module=MEDIA, accessType=READ`
**Key params:**
- `context` (header, optional, string, default `"en"`)
- `context-fallback` (header, optional, boolean, default `true`)

**Request body:** `MediabankAssetSearchRequest`
- `page` (int32, required, 0–10000)
- `resultsPerPage` (int32, required, 1–100)
- `sortField` (string, optional, enum: `NAME` | `FILE_SIZE` | `CREATED_AT`)
- `sortOrder` (string, optional, enum: `ASC` | `DESC`)
- `quickSearch` (string, optional) — full-text search across asset name/description
- `asset` (MediabankAssetsParams, optional):
  - `uploader` (string) — filter by uploader user ID
  - `internal` (boolean) — internal/external asset flag
  - `published` (boolean)
  - `idsParams`, `numbersParams`, `nameParams`, `descriptionParams` — text match params
  - `productsParams` — filter assets assigned to specific products
  - `categoriesParams` — filter by category assignment
  - `labelsParams` — filter by asset labels
  - `fileParams` — file-level filters
  - `created`, `updated` — absolute date range filters
  - `createdRelative`, `updatedRelative` — relative date filters
  - `attributeFilters` (AttributeFilter[]) — asset attribute filters
- `mediaInfo` (MediabankMediaInfoParams, optional):
  - `mediaType` (enum: `IMAGE` | `VIDEO` | `AUDIO` | `TEXT` | `DOCUMENT` | `OTHER`)
  - `mediaTypes` (array of the above)
  - `dimensionsCondition` (enum: `ICON` | `SMALL` | `MEDIUM` | `LARGE` | `XL`)
  - `fileMinimumSize`, `fileMaximumSize` — file size bounds
  - `fileExtension` — e.g. `"jpg"`, `"pdf"`

**Response:** `AssetSearchResponse` — `data` (`{ id: string }[]`)

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().post(
  `/api/search/assets/search`,
  {
    page: 0,
    resultsPerPage: 50,
    sortField: "CREATED_AT",
    sortOrder: "DESC",
    quickSearch: "product hero",
    mediaInfo: { mediaType: "IMAGE", fileExtension: "jpg" },
  },
  { headers: { context: "en" } }
);
// data.data is { id: string }[]
```

---

### POST /search/assets/cursor

**Tags:** Search
**Permission:** `module=MEDIA, accessType=READ`
**Key params:**
- `context` (header, optional, string, default `"en"`)
- `context-fallback` (header, optional, boolean, default `true`)

**Request body:** `MediabankAssetSearchCursorRequest`
- `count` (int32, required, max 100) — page size
- `cursor` (string, optional) — opaque cursor from previous response; omit for first page
- `quickSearch` (string, optional)
- `asset` (MediabankAssetsParams, optional) — see `/assets/search`
- `mediaInfo` (MediabankMediaInfoParams, optional) — see `/assets/search`

**Response:** `AssetSearchCursorResponse` — `data` (`{ id: string }[]`), `cursor` (string | null — null means no more pages)

**PBC usage:**
```typescript
let cursor: string | undefined;
do {
  const { data: page } = await getAxiosInstance().post(
    `/api/search/assets/cursor`,
    {
      count: 100,
      cursor,
      mediaInfo: { mediaType: "IMAGE" },
    }
  );
  // process page.data
  cursor = page.cursor ?? undefined;
} while (cursor);
```

---

### POST /search/assets/count

**Tags:** Search
**Permission:** `module=MEDIA, accessType=READ`
**Key params:**
- `context` (header, optional, string, default `"en"`)
- `context-fallback` (header, optional, boolean, default `true`)

**Request body:** `MediabankAssetCountRequest`
- `quickSearch` (string, optional)
- `asset` (MediabankAssetsParams, optional)
- `mediaInfo` (MediabankMediaInfoParams, optional)

**Response:** `AssetCountResponse` — `count` (int64)

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().post(
  `/api/search/assets/count`,
  {
    mediaInfo: { mediaType: "VIDEO" },
  }
);
console.log(data.count);
```

---

### GET /search/find

**Tags:** Full-text search
**Permission:** `module=SEARCH, accessType=READ`
**Key params:**
- `query` (query, optional, string) — full-text search string
- `context` (query, optional, string) — localization context
- `searchableFields` (query, optional, string[]) — restrict which fields are searched
- `archiveState` (query, optional, enum: `ACTIVE` | `ARCHIVED`, default `ACTIVE`)
- `highlight` (query, optional, boolean) — return highlighted fragments
- `fragmentSize` (query, optional, int32, 0–1000, default 20) — highlight fragment length in chars
- `fuzziness` (query, optional, enum: `ZERO` | `ONE` | `TWO` | `AUTO`, default `AUTO`) — edit-distance tolerance
- `page` (query, optional, int32, default 0)
- `pageSize` (query, optional, int32, 1–1000, default 10)

**Request body:** none

**Response:** `FindResponseList` — `data` (string), `total` (int64)

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().get(`/api/search/find`, {
  params: {
    query: "winter jacket",
    context: "en",
    highlight: true,
    fragmentSize: 50,
    fuzziness: "AUTO",
    page: 0,
    pageSize: 20,
    archiveState: "ACTIVE",
  },
});
// data.data contains highlighted result fragments; data.total is the hit count
```

---

---

## Query Builder

Base URL: `https://api.test.bluestonepim.com/query-builder`

The query-builder service accepts a structured `MainQueryGroup` / `QueryGroup` tree in place of flat filter arrays. It also exposes endpoints for persisted (saved) queries and view validation.

---

### Query Structure Reference

#### MainQueryGroup

The top-level query object sent to `/query-builder/products/search`, `/count`, and `/scroll/search`.

```
MainQueryGroup {
  matchRule:       MatchRule          // required — "all" | "any"
  children:        QueryGroupChild[]  // required — one or more filter nodes
  not?:            boolean            // negate the entire group
  contextFallback?: boolean           // set false to disable locale fallback
}
```

#### QueryGroup (nested group)

```
QueryGroup {
  matchRule:  MatchRule         // required
  children:   QueryGroupChild[] // required
  not?:       boolean
}
```

#### QueryGroupWithAssociation (association-scoped group)

Extends `QueryGroup` with one additional field:

```
association: "variant_group" | "variant" | "bundle" | "product_in_bundle"
```

Use this when you want to evaluate conditions scoped to associated entities (e.g. "has a variant where color=Red").

#### QueryGroupChild

A union — each child is one of:

| Type | Description |
|---|---|
| `QueryGroup` | Nested logical group |
| `QueryGroupWithAssociation` | Nested group scoped to an association |
| `QueryTerm` | A single filter condition (leaf node) |

#### MatchRule

```
"all"  // AND — all children must match
"any"  // OR  — at least one child must match
```

---

### QueryTerm Types (Leaf Filters)

Each `QueryTerm` has a `termType` that identifies the field and a `filterType` that identifies the operation.

#### ID

| filterType | Description |
|---|---|
| `is` | exact ID match |
| `in` | ID in a set |

#### Name / Number / Description

| filterType | Description |
|---|---|
| `is` | exact match |
| `contains` | substring |
| `starts_with` | prefix |
| `ends_with` | suffix |
| `not_contains` | exclude substring |
| `defined` / `not_defined` | existence check |

#### Product Type — `termType: "product_type"`

```
filterType: "in"
value: ProductType[]  // "SINGLE" | "GROUP" | "VARIANT" | "BUNDLE"
```

#### Created Date — `termType: "created_date"`

```
filterType: "date_between"
from?: number  // epoch ms
to?:   number  // epoch ms

// or relative:
filterType: "days_ago" | "days_from_now"
value: number
```

#### Updated Date — `termType: "updated_date"`

Same filterType options as created date.

#### Category — `termType: "category"`

| filterType | Description |
|---|---|
| `have_some` | in specific category |
| `have_some_with_children` | in category or any descendant |
| `have_all` | in all listed categories |
| `have_all_with_children` | in all listed categories or their descendants |
| `have_none` | not in any of the listed categories |

#### Product Labels — `termType: "product_labels"`

| filterType | Description |
|---|---|
| `have_all` | has all listed labels |
| `have_some` | has at least one listed label |
| `have_none` | has none of the listed labels |

#### Assigned Assets — `termType: "assigned_assets"`

| filterType | Description |
|---|---|
| `greater_than` | asset count > threshold |
| `smaller_than` | asset count < threshold |
| `have_some` | has at least one asset |
| `have_none` | has no assets |
| `have_some_of` | assigned to specific asset IDs |
| `have_all_of` | assigned to all listed asset IDs |

#### Variants — `termType: "variant"`

| filterType | Description |
|---|---|
| `greater_than` | variant count > threshold |
| `smaller_than` | variant count < threshold |
| `have_some` | has variants |
| `have_none` | has no variants |

#### Relations — `termType: "relation"`

| filterType | Description |
|---|---|
| `is` | related via a specific relation |
| `is_with_products` | related to specific product IDs via a relation |
| `have_none` | has no relations |

Fields: `relationId` (string), `productIds` (string[], max 100), `reverse` (boolean).

#### Validation Status — `termType: "validation_status"`

```
filterType: "is"
value: "VALID" | "INVALID"
```

#### Publish State — `termType: "publish_state"`

```
filterType: "in"
value: ProductStatus[]
```

#### Out of Sync — `termType: "papi_out_of_sync"`

```
filterType: "is"
value: boolean
```

#### Last Sync State — `termType: "last_sync_state"`

```
filterType: "is"
value: "SUCCESSFUL" | "FAILED"
```

#### Last Modified By — `termType: "last_modified_by"`

```
filterType: "is"              // with user identifier value
filterType: "is_current_user" // no value needed
```

#### Completeness Score — `termType: "completeness_score"`

```
filterType: "between"
context: string
from: number  // 0–100
to:   number
```

#### Completeness Requirements — `termType: "completeness_requirements"`

```
filterType: "not_meet_all"
context: string
value: string[]  // requirement IDs
```

---

### Attribute QueryTerm Types

All attribute terms share `termType: "attribute"` and require `attributeId` and `context` fields. The `filterType` and additional fields vary by attribute data type.

#### Text Attribute

| filterType | Extra fields |
|---|---|
| `contains` | `value: string` |
| `not_contains` | `value: string` |
| `is` | `value: string` |
| `in` | `values: string[]` |
| `starts_with` | `value: string` |
| `ends_with` | `value: string` |
| `assigned` | — (has any value) |
| `not_assigned` | — (has no value) |

#### Multiline / Formatted Text Attribute

Same filterTypes as Text plus `defined` / `not_defined`.

#### Integer / Decimal Attribute

| filterType | Extra fields |
|---|---|
| `greater_than` | `value: number` |
| `smaller_than` | `value: number` |
| `between` | `from: number`, `to: number` |
| `equals` | `value: number` |
| `in` | `values: number[]` |
| `have_some` | — |
| `assigned` | — |
| `not_assigned` | — |

#### Boolean Attribute

| filterType | Description |
|---|---|
| `equals` | `value: boolean` |
| `assigned` | has a value |
| `not_assigned` | has no value |

#### Date Attribute

| filterType | Extra fields |
|---|---|
| `date_between` | `from: number (ms)`, `to: number (ms)` |
| `equals` | `value: number (ms)` |
| `assigned` | — |
| `not_assigned` | — |

#### DateTime / Time Attribute

Same filterTypes as Date.

#### Dictionary / SingleSelect / MultiSelect Attribute

| filterType | Extra fields |
|---|---|
| `is` | `value: string` (option ID) |
| `have_some` | `values: string[]` |
| `have_all` | `values: string[]` |
| `assigned` | — |
| `not_assigned` | — |

#### Matrix / Column Attribute

```
filterType: "assigned"   // has any value in the matrix
```

#### Pattern Attribute

```
filterType: "assigned" | "not_assigned"
```

---

### Enum Reference

#### AttributeFilterTypes (used in flat `AttributeFilter` on search.json endpoints)

```
CONTAINS | NOT_CONTAINS | EQUALS | NOT_EQUALS
EMPTY | NOT_EMPTY
BETWEEN | NOT_BETWEEN
GREATER_THAN | GREATER_OR_EQUALS_TO | SMALLER_THAN | SMALLER_OR_EQUALS_TO
DOES_HAVE_SOME | DOESNT_HAVE_ANY | DOES_HAVE_ALL | DOESNT_HAVE_ALL
ASSOCIATED | NOT_ASSOCIATED
IN
DATE_RELATIVE
DO_NOT_FILTER
```

#### AssetFilterTypes

```
HAVE_NONE | HAVE_ANY | HAVE_MORE_THAN | HAVE_LESS_THAN
ASSIGNED_TO_ANY | ASSIGNED_TO_ALL
DO_NOT_FILTER
```

#### CategoryFiltersTypes

```
IN_ANY_CHILD | IN_CATEGORY | WITHOUT_CATEGORY
```

#### LabelFilterType (LabelFilterTypes)

```
ALL | SOME | NOT | EMPTY | DO_NOT_FILTER
```

#### AssetLabelsFilterTypes

```
NOT_HAVE_LABEL | HAVE_LABEL | DO_NOT_FILTER
```

#### VariantFilterTypes

```
HAVE_NONE | HAVE_ANY | HAVE_MORE_THAN | HAVE_LESS_THAN | DO_NOT_FILTER
```

#### BundleFilterTypes

```
HAVE_NONE | HAVE_ANY | HAVE_MORE_THAN | HAVE_LESS_THAN | DO_NOT_FILTER
```

#### RelationsFilterTypes

```
EMPTY | HAVE | NOT | DO_NOT_FILTER | HAVE_WITH_PRODUCTS
```

#### ProductStatus (PublishState values)

```
PLAYGROUND_ONLY | TO_BE_PUBLISHED | CONNECTED | NOT_CONNECTED
TO_BE_UNPUBLISHED | TO_BE_ARCHIVED | ARCHIVED
```

#### ValidationStatusState

```
VALID | INVALID
```

#### ProductType

```
SINGLE | GROUP | VARIANT | BUNDLE
```

#### BaseFiltersTypes

```
NAME_IS | NAME_CONTAINS | NAME_NOT_CONTAINS | NAME_STARTS_WITH | NAME_ENDS_WITH | NAME_EMPTY | NAME_NOT_EMPTY
NUMBER_IS | NUMBER_CONTAINS | NUMBER_NOT_CONTAINS | NUMBER_STARTS_WITH | NUMBER_ENDS_WITH | NUMBER_IN | NUMBER_NOT_IN
DESCRIPTION_IS | DESCRIPTION_CONTAINS | DESCRIPTION_NOT_CONTAINS | DESCRIPTION_STARTS_WITH | DESCRIPTION_ENDS_WITH | DESCRIPTION_EMPTY | DESCRIPTION_NOT_EMPTY
ID_IN
LAST_MODIFIED_BY_IS | LAST_MODIFIED_BY_CURRENT_USER
```

#### SortDirectionTypes

```
ASC | DESC
```

#### PersistedProductQueryTypes

```
product_category_assign | product_relation_assign
```

#### RelativeAttributeUnitTypes

```
DAYS_AGO | DAYS_FROM_NOW
```

---

### Sorting (query-builder endpoints)

#### SortingBasicFields

```typescript
{
  termType: "name" | "number" | "type" | "created_date" | "updated_date"
  direction: "asc" | "desc"
  context: string
}
```

#### SortingAttribute

```typescript
{
  termType: "attribute"
  attributeId: string
  attributeType: string  // attribute data type (e.g. "text", "integer")
  context: string
  direction: "asc" | "desc"
}
```

---

### Paging

```typescript
// Offset paging (search + count)
{ page: number; pageSize: number }

// Scroll initial
{ pageSize: number }

// Scroll continuation
{ scrollId: string }
```

---

### Query Builder Endpoints

#### POST /query-builder/products/search

**Tags:** Products
**Key params:**
- `failOnAssociationQueryLimit` (query, boolean, default `true`) — return error instead of silently capping when association query limit is exceeded
- `archiveState` (query, optional, enum: `ACTIVE` | `ARCHIVED`)
- `context-fallback` (header, optional, boolean)

**Request body:** `ProductsSearchRequest`
- `query` (MainQueryGroup, required)
- `sorting` (SortingBody, optional)
- `paging` (Paging, optional)

**Response:** `ProductsSearchResponse` — `data` (`{ id: string }[]`), `errors` (`ProductSearchQueryError[]`)

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().post(
  `/api/query-builder/products/search`,
  {
    query: {
      matchRule: "all",
      children: [
        {
          matchRule: "all",
          children: [
            {
              termType: "attribute",
              filterType: "equals",
              attributeId: "color-attr-uuid",
              context: "en",
              value: "Blue",
            },
          ],
        },
      ],
    },
    sorting: { termType: "name", direction: "asc", context: "en" },
    paging: { page: 0, pageSize: 50 },
  },
  { params: { archiveState: "ACTIVE" } }
);
// data.data is { id: string }[]
```

---

#### POST /query-builder/products/count

**Tags:** Products
**Key params:**
- `failOnAssociationQueryLimit` (query, boolean, default `true`)
- `archiveState` (query, optional, enum: `ACTIVE` | `ARCHIVED`)
- `context-fallback` (header, optional, boolean)

**Request body:** `ProductsSearchRequest` — same as `/products/search` (paging/sorting ignored for count)

**Response:** `ProductsCountResponse` — `count` (number)

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().post(
  `/api/query-builder/products/count`,
  {
    query: {
      matchRule: "any",
      children: [
        { termType: "validation_status", filterType: "is", value: "INVALID" },
      ],
    },
  },
  { params: { archiveState: "ACTIVE" } }
);
console.log(data.count);
```

---

#### POST /query-builder/products/scroll/search

**Tags:** Products
**Key params:**
- `archiveState` (query, optional, enum: `ACTIVE` | `ARCHIVED`)
- `context-fallback` (header, optional, boolean)

**Request body:** `ProductsScrollSearchRequest` (union)
- Initial: `{ query: MainQueryGroup, sorting?: SortingBody, paging: { pageSize: number } }`
- Continuation: `{ paging: { scrollId: string } }`

**Response:** `ProductsScrollSearchResponse` — `data` (`{ id: string }[]`), `scrollId` (string)

**PBC usage:**
```typescript
// Initial
const { data: first } = await getAxiosInstance().post(
  `/api/query-builder/products/scroll/search`,
  {
    query: { matchRule: "all", children: [] },
    paging: { pageSize: 100 },
  }
);
let scrollId = first.scrollId;

// Pages
while (scrollId) {
  const { data: page } = await getAxiosInstance().post(
    `/api/query-builder/products/scroll/search`,
    { paging: { scrollId } }
  );
  // process page.data
  scrollId = page.scrollId ?? null;
}
```

---

#### POST /query-builder/savedViews/getValidView

**Tags:** savedView
**Key params:** none

**Request body:** `ProductFiltersOrQuery` — either a flat `PureProductFilters` object or a `MainQueryGroup`

**Response:** `GetValidFilterResponse`
- `filter` (ProductFiltersOrQuery) — corrected/normalized filter
- `isOriginalValid` (boolean) — `false` if the input had to be corrected

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().post(
  `/api/query-builder/savedViews/getValidView`,
  {
    matchRule: "all",
    children: [
      { termType: "product_type", filterType: "in", value: ["SINGLE"] },
    ],
  }
);
if (!data.isOriginalValid) {
  console.warn("Query was auto-corrected", data.filter);
}
```

---

#### POST /query-builder/products/persistedQueries

**Tags:** Products
**Key params:** none

**Request body:** `PersistedProductQueryInsertRequest`
- `queryType` (enum: `product_category_assign` | `product_relation_assign`, required)
- `query` (MainStaticQueryGroup, required) — static (non-dynamic) query tree
- `queryId` (string, optional) — supply to use a specific ID, otherwise auto-assigned
- `useOnlyNewIndex` (boolean, optional)

**Response:** `PersistedProductQueryInsertResponse` — `status` (`{ statusType, message }`), `data` (`{ id: string }`)

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().post(
  `/api/query-builder/products/persistedQueries`,
  {
    queryType: "product_category_assign",
    query: {
      matchRule: "all",
      children: [
        { termType: "product_type", filterType: "in", value: ["SINGLE"] },
      ],
    },
  }
);
const persistedId = data.data.id;
```

---

#### PUT /query-builder/products/persistedQueries

**Tags:** Products
**Key params:** none

**Request body:** `PersistedProductQueryUpdateRequest`
- `queryType` (enum, required)
- `queryId` (string, required)
- `query` (MainStaticQueryGroup, required)

**Response:** `PersistedProductQueryResponseBase` — `status` (`{ statusType, message }`)

**PBC usage:**
```typescript
await getAxiosInstance().put(`/api/query-builder/products/persistedQueries`, {
  queryType: "product_category_assign",
  queryId: "existing-query-uuid",
  query: {
    matchRule: "all",
    children: [
      { termType: "product_labels", filterType: "have_some", values: ["label-uuid"] },
    ],
  },
});
```

---

#### DELETE /query-builder/products/persistedQueries

**Tags:** Products
**Key params:** none

**Request body:** `PersistedProductQueryDeleteRequest`
- `queryType` (enum, required)
- `queryId` (string, required)

**Response:** `PersistedProductQueryResponseBase` — `status` (`{ statusType, message }`)

**PBC usage:**
```typescript
await getAxiosInstance().delete(`/api/query-builder/products/persistedQueries`, {
  data: {
    queryType: "product_category_assign",
    queryId: "query-uuid-to-remove",
  },
});
```

---

#### POST /query-builder/products/persistedQueries/list

**Tags:** Products
**Key params:** none

**Request body:** `PersistedProductQueryListRequest`
- `queryType` (enum, required)
- `paging` (`{ page?: number, pageSize?: number }`, optional)

**Response:** `PersistedProductQueryListResponse` — `status`, `data` (`{ id: string, query: MainStaticQueryGroup }[]`)

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().post(
  `/api/query-builder/products/persistedQueries/list`,
  {
    queryType: "product_relation_assign",
    paging: { page: 0, pageSize: 20 },
  }
);
// data.data is { id, query }[]
```

---

#### POST /query-builder/products/persistedQueries/search

**Tags:** Products
**Key params:** none

**Request body:** `PersistedProductQuerySearchRequest`
- `queryType` (enum, required)
- `productId` (string, required) — find which persisted queries match this product
- `paging` (optional)

**Response:** `PersistedProductQuerySearchResponse` — `status`, `data` (`{ id: string }[]`)

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().post(
  `/api/query-builder/products/persistedQueries/search`,
  {
    queryType: "product_category_assign",
    productId: "product-uuid",
  }
);
// data.data lists IDs of persisted queries that match the product
```

---

### Complete Worked Example

Filter **active, single products** where:
- The `color` text attribute **equals** `"Blue"` (in `en` context)
- The product was **created between** 1 Jan 2024 and 31 Dec 2024
- The product is **connected** (published)

```typescript
const { data } = await getAxiosInstance().post(
  `/api/query-builder/products/search`,
  {
    query: {
      matchRule: "all",          // AND all top-level conditions
      contextFallback: true,
      children: [
        // Condition 1: product type = SINGLE
        {
          termType: "product_type",
          filterType: "in",
          value: ["SINGLE"],
        },
        // Condition 2: publish state = CONNECTED
        {
          termType: "publish_state",
          filterType: "in",
          value: ["CONNECTED"],
        },
        // Condition 3: color attribute equals "Blue"
        {
          termType: "attribute",
          filterType: "equals",
          attributeId: "color-attr-uuid",   // replace with real attribute definition ID
          context: "en",
          value: "Blue",
        },
        // Condition 4: created date between 2024-01-01 and 2024-12-31
        {
          termType: "created_date",
          filterType: "date_between",
          from: 1704067200000,  // 2024-01-01T00:00:00Z in epoch ms
          to:   1735689599000,  // 2024-12-31T23:59:59Z in epoch ms
        },
      ],
    },
    sorting: {
      termType: "name",
      direction: "asc",
      context: "en",
    },
    paging: {
      page: 0,
      pageSize: 50,
    },
  },
  {
    params: { archiveState: "ACTIVE" },
    headers: { "context-fallback": true },
  }
);

const productIds = data.data.map((p: { id: string }) => p.id);
```

---

## Media Bank

> Base path prefix: `/api/media-bank/...` on `api.test.bluestonepim.com`

---

### GET /labels

**Tags:** Labels
**Permission:** MEDIA / READ
**Key params:**
- `labelType` (query, string, optional) — filter by `CUSTOM` or `TAG`
- `mediaType` (query, string, optional) — `IMAGE`, `VIDEO`, `AUDIO`, `TEXT`, `DOCUMENT`, `OTHER`
- `page` (query, integer, optional, default: 0)
- `pageSize` (query, integer, optional, max: 1000)

**Response:** `DescribeMediaLabelResponse` array — label id, name, type, associated mediaType

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().get('/api/media-bank/labels', {
  params: { labelType: 'CUSTOM', mediaType: 'IMAGE', page: 0, pageSize: 100 },
});
```

---

### POST /labels

**Tags:** Labels
**Permission:** MEDIA / WRITE
**Request body:**
- `name` (string, required) — label name
- `mediaType` (string, optional) — `IMAGE`, `VIDEO`, `AUDIO`, `TEXT`, `DOCUMENT`, `OTHER`

**Response:** `DescribeMediaLabelResponse` — created label id, name, type

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().post('/api/media-bank/labels', {
  name: 'Hero Shot',
  mediaType: 'IMAGE',
});
```

---

### POST /labels/type/{type}

**Tags:** Labels
**Permission:** MEDIA_LABELS / WRITE
**Key params:**
- `type` (path, string, required) — `CUSTOM` or `TAG`

**Request body:**
- `name` (string, required)
- `mediaType` (string, optional)

**Response:** `DescribeMediaLabelResponse` — created label

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().post('/api/media-bank/labels/type/TAG', {
  name: 'approved',
});
```

---

### GET /labels/{id}

**Tags:** Labels
**Permission:** MEDIA / READ
**Key params:**
- `id` (path, string, required) — label identifier

**Response:** `DescribeMediaLabelResponse` — id, name, type, mediaType

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().get(`/api/media-bank/labels/${labelId}`);
```

---

### PUT /labels/{id}

**Tags:** Labels
**Permission:** MEDIA / WRITE
**Key params:**
- `id` (path, string, required)

**Request body:**
- `name` (string, required)
- `mediaType` (string, optional)

**Response:** `DescribeMediaLabelResponse` — updated label

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().put(`/api/media-bank/labels/${labelId}`, {
  name: 'Updated Label Name',
});
```

---

### DELETE /labels/{id}

**Tags:** Labels
**Permission:** MEDIA / WRITE
**Key params:**
- `id` (path, string, required)

**Response:** 204 No Content

**PBC usage:**
```typescript
await getAxiosInstance().delete(`/api/media-bank/labels/${labelId}`);
```

---

### PUT /labels/type/{type}/{id}

**Tags:** Labels
**Permission:** MEDIA_LABELS / WRITE
**Key params:**
- `type` (path, string, required) — `CUSTOM` or `TAG`
- `id` (path, string, required)

**Request body:**
- `name` (string, required)
- `mediaType` (string, optional)

**Response:** `DescribeMediaLabelResponse` — updated label

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().put(`/api/media-bank/labels/type/CUSTOM/${labelId}`, {
  name: 'Renamed Custom Label',
});
```

---

### DELETE /labels/type/{type}/{id}

**Tags:** Labels
**Permission:** MEDIA_LABELS / WRITE
**Key params:**
- `type` (path, string, required) — `CUSTOM` or `TAG`
- `id` (path, string, required)

**Response:** 204 No Content

**PBC usage:**
```typescript
await getAxiosInstance().delete(`/api/media-bank/labels/type/TAG/${labelId}`);
```

---

### POST /labels/list

**Tags:** Labels
**Permission:** MEDIA / READ
**Request body:**
- `labelTypes` (array of strings, optional) — `CUSTOM`, `TAG`
- `mediaTypes` (array of strings, optional)
- `page` (integer, optional)
- `pageSize` (integer, optional, max: 1000)

**Response:** `ListMediaLabelResponse` — paginated list of `DescribeMediaLabelResponse`

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().post('/api/media-bank/labels/list', {
  labelTypes: ['CUSTOM', 'TAG'],
  mediaTypes: ['IMAGE'],
  page: 0,
  pageSize: 200,
});
```

---

### GET /upload

**Tags:** Upload
**Permission:** MEDIA / READ
**Key params:**
- `context` (header, string, optional) — language/context identifier
- `context-fallback` (header, boolean, optional) — fall back to default context if value missing

**Response:** `DescribeUploadAssetResponse` array — pending upload assets

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().get('/api/media-bank/upload', {
  headers: { context: 'en_US' },
});
```

---

### POST /upload

**Tags:** Upload
**Permission:** MEDIA / WRITE
**Key params:**
- `context` (header, string, optional)

**Request body:**
- `fileName` (string, required) — original file name including extension
- `contentType` (string, optional) — MIME type

**Response:** `UploadAssetResponse` — pre-signed upload URL; response headers `Location` (asset URL) and `Resource-Id` (new asset id)

**PBC usage:**
```typescript
const { data, headers } = await getAxiosInstance().post('/api/media-bank/upload', {
  fileName: 'product-shot.jpg',
  contentType: 'image/jpeg',
});
const assetId = headers['resource-id'];
// Use data.uploadUrl to PUT the binary file directly to storage
```

---

### POST /upload/{id}

**Tags:** Upload
**Permission:** MEDIA / WRITE
**Key params:**
- `id` (path, string, required) — existing asset id to create a new version for
- `context` (header, string, optional)

**Request body:**
- `fileName` (string, required)
- `contentType` (string, optional)

**Response:** `UploadAssetResponse` — pre-signed URL for the new version; headers `Location`, `Resource-Id`

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().post(`/api/media-bank/upload/${assetId}`, {
  fileName: 'product-shot-v2.jpg',
  contentType: 'image/jpeg',
});
```

---

### POST /upload/{id}/done

**Tags:** Upload
**Permission:** MEDIA / WRITE
**Key params:**
- `id` (path, string, required) — upload asset id returned from POST /upload
- `context` (header, string, optional)

**Response:** `DescribeUploadAssetResponse` — finalized asset record

**PBC usage:**
```typescript
// Call after binary upload to the pre-signed URL is complete
const { data } = await getAxiosInstance().post(`/api/media-bank/upload/${assetId}/done`);
```

---

### PUT /upload/{id}

**Tags:** Upload
**Permission:** MEDIA / WRITE
**Key params:**
- `id` (path, string, required)

**Request body:**
- `name` (string, optional) — display name
- `number` (string, optional) — asset number/SKU reference
- `description` (string, optional)
- `labels` (array of strings, optional) — label ids
- `internal` (boolean, optional) — mark asset as internal-only

**Response:** `DescribeUploadAssetResponse` — updated asset metadata

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().put(`/api/media-bank/upload/${assetId}`, {
  name: 'Front View Hero Shot',
  labels: ['labelId1', 'labelId2'],
});
```

---

### DELETE /upload/{id}

**Tags:** Upload
**Permission:** MEDIA / WRITE
**Key params:**
- `id` (path, string, required)
- `context` (header, string, optional)

**Response:** 204 — upload cancelled (asset never finalized)

**PBC usage:**
```typescript
await getAxiosInstance().delete(`/api/media-bank/upload/${assetId}`);
```

---

### GET /assets/{id}

**Tags:** Assets
**Permission:** MEDIA / READ
> **Deprecated** — EOL 2026-07-30. Use `POST /assets/views/by-ids` instead.

**Key params:**
- `id` (path, string, required)
- `context` (header, string, optional)
- `context-fallback` (header, boolean, optional)

**Response:** `DescribeWorkingAssetResponse` — full asset record including id, name, number, description, labels, mediaType, published, versions

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().get(`/api/media-bank/assets/${assetId}`, {
  headers: { context: 'en_US' },
});
```

---

### DELETE /assets/{id}

**Tags:** Assets
**Permission:** MEDIA / WRITE
**Key params:**
- `id` (path, string, required)

**Response:** 204 — asset marked as deleted

**PBC usage:**
```typescript
await getAxiosInstance().delete(`/api/media-bank/assets/${assetId}`);
```

---

### DELETE /assets/delete

**Tags:** Assets
**Permission:** none specified (inherits OAuth2)
**Key params:**
- `ids` (query, array of strings, required) — list of asset IDs to delete

**Response:** Array of `BatchOperationApiResponse` — per-id success/failure results

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().delete('/api/media-bank/assets/delete', {
  params: { ids: ['assetId1', 'assetId2'] },
});
```

---

### GET /assets/{id}/versions

**Tags:** Assets
**Permission:** MEDIA / READ
**Key params:**
- `id` (path, string, required)
- `page` (query, integer, optional, default: 0)
- `pageSize` (query, integer, optional, default: 10, max: 1000)
- `context` (header, string, optional)

**Response:** `ListableDescribeMediaVersionAssetResponse` — paginated version history for the asset

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().get(`/api/media-bank/assets/${assetId}/versions`, {
  params: { page: 0, pageSize: 20 },
});
```

---

### POST /assets/{id}/versions/{versionId}/revert

**Tags:** Assets
**Permission:** MEDIA / WRITE
**Key params:**
- `id` (path, string, required) — asset id
- `versionId` (path, string, required) — target version id
- `context` (header, string, optional)

**Response:** 204 — asset reverted to specified version

**PBC usage:**
```typescript
await getAxiosInstance().post(`/api/media-bank/assets/${assetId}/versions/${versionId}/revert`);
```

---

### POST /assets/{id}/duplicate

**Tags:** Assets
**Permission:** MEDIA / WRITE
**Key params:**
- `id` (path, string, required)

**Response:** `DescribeWorkingAssetResponse` — newly created duplicate asset

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().post(`/api/media-bank/assets/${assetId}/duplicate`);
```

---

### POST /assets/download

**Tags:** Assets
**Permission:** MEDIA / READ
**Key params:**
- `context` (header, string, optional)
- `context-fallback` (header, boolean, optional)

**Request body:**
- `DownloadAssetsRequest` — asset ids and optional format/size parameters

**Response:** `DownloadAssetsResponse` — map of asset id to pre-signed download URL

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().post('/api/media-bank/assets/download', {
  assetIds: ['assetId1', 'assetId2'],
});
// data contains { assetId1: 'https://...', assetId2: 'https://...' }
```

---

### PUT /assets/{id}/generate-fixed-link

**Tags:** Assets
**Permission:** MEDIA / WRITE
**Key params:**
- `id` (path, string, required)
- `context` (header, string, optional)
- `context-fallback` (header, boolean, optional)

**Response:** `RedirectionGenerationResult` — stable public URL for the asset

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().put(`/api/media-bank/assets/${assetId}/generate-fixed-link`);
```

---

### POST /assets/{id}/views

**Tags:** Assets
**Permission:** MEDIA / READ
**Key params:**
- `id` (path, string, required)
- `context` (header, string, optional)
- `context-fallback` (header, boolean, optional)

**Request body:** `WorkingAssetViewsRequest` — list of view/rendition names to resolve

**Response:** `WorkingAssetViewResponse` — resolved URLs per requested view

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().post(`/api/media-bank/assets/${assetId}/views`, {
  views: ['thumbnail', 'preview'],
}, { headers: { context: 'en_US' } });
```

---

### POST /assets/views/by-ids

**Tags:** Assets
**Permission:** MEDIA / READ
**Key params:**
- `context` (header, string, optional)
- `context-fallback` (header, boolean, optional)

**Request body:** `WorkingAssetsViewsByIdsRequest` — array of asset ids and requested view names

**Response:** `WorkingAssetViewListResponse` — per-asset resolved view URLs

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().post('/api/media-bank/assets/views/by-ids', {
  assetIds: ['assetId1', 'assetId2'],
  views: ['thumbnail', 'original'],
}, { headers: { context: 'en_US' } });
```

---

### POST /assets/search/by-ids

**Tags:** Assets
**Permission:** MEDIA / READ
> **Deprecated** — EOL 2026-07-30. Use `POST /assets/views/by-ids` instead.

**Key params:**
- `context` (header, string, optional)
- `context-fallback` (header, boolean, optional)

**Request body:** `DescribeWorkingAssetsByIdsSearchRequest` — array of asset ids

**Response:** `DescribeWorkingAssetListResponse` — list of full asset records

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().post('/api/media-bank/assets/search/by-ids', {
  ids: ['assetId1', 'assetId2'],
});
```

---

### POST /assets/search/by-numbers

**Tags:** Assets
**Permission:** MEDIA / READ
**Key params:**
- `context` (header, string, optional)
- `context-fallback` (header, boolean, optional)

**Request body:** `WorkingAssetsByNumbersRequest` — array of asset numbers (business key)

**Response:** `WorkingAssetPaginatedResponse` — matching assets

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().post('/api/media-bank/assets/search/by-numbers', {
  numbers: ['SKU-001', 'SKU-002'],
}, { headers: { context: 'en_US' } });
```

---

### POST /assets/cursor/all

**Tags:** Assets
**Permission:** MEDIA / READ
**Key params:**
- `context` (header, string, optional)
- `context-fallback` (header, boolean, optional)

**Request body:** `WorkingAssetCursorRequest` — optional cursor, page size, filters

**Response:** `WorkingAssetCursorResponse` — page of assets plus `nextCursor` for subsequent pages

**PBC usage:**
```typescript
let cursor: string | undefined;
do {
  const { data } = await getAxiosInstance().post('/api/media-bank/assets/cursor/all', {
    cursor,
    count: 100,
  });
  // process data.items
  cursor = data.nextCursor;
} while (cursor);
```

---

### PUT /assets/{id}/labels

**Tags:** Assets
**Permission:** MEDIA / WRITE
**Key params:**
- `id` (path, string, required)

**Request body:** `UpdateAssetsLabelsAttributeRequest` — full replacement set of label ids

**Response:** `DescribeWorkingAssetResponse` — updated asset

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().put(`/api/media-bank/assets/${assetId}/labels`, {
  labelIds: ['labelId1', 'labelId2'],
});
```

---

### POST /assets/{id}/labels

**Tags:** Assets
**Permission:** MEDIA / WRITE
**Key params:**
- `id` (path, string, required)

**Request body:** `UpdateAssetsLabelsAttributeRequest` — label ids to add (additive)

**Response:** `DescribeWorkingAssetResponse` — updated asset

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().post(`/api/media-bank/assets/${assetId}/labels`, {
  labelIds: ['labelId3'],
});
```

---

### DELETE /assets/{id}/labels/{labelId}

**Tags:** Assets
**Permission:** MEDIA / WRITE
**Key params:**
- `id` (path, string, required)
- `labelId` (path, string, required)

**Response:** `DescribeWorkingAssetResponse` — updated asset

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().delete(`/api/media-bank/assets/${assetId}/labels/${labelId}`);
```

---

### POST /assets/labels/by-ids

**Tags:** Assets
**Permission:** MEDIA / WRITE
**Request body:** `BulkAssetsLabelUpdateRequest` — `assetIds` array, `labelIds` array

**Response:** 204 No Content

**PBC usage:**
```typescript
await getAxiosInstance().post('/api/media-bank/assets/labels/by-ids', {
  assetIds: ['assetId1', 'assetId2'],
  labelIds: ['labelId1'],
});
```

---

### DELETE /assets/labels/by-ids

**Tags:** Assets
**Permission:** MEDIA / WRITE
**Request body:** `BulkAssetsLabelUpdateRequest` — `assetIds` array, `labelIds` array to remove

**Response:** 204 No Content

**PBC usage:**
```typescript
await getAxiosInstance().delete('/api/media-bank/assets/labels/by-ids', {
  data: { assetIds: ['assetId1'], labelIds: ['labelId1'] },
});
```

---

### PUT /assets/published

**Tags:** Assets
**Permission:** MEDIA / WRITE
**Key params:**
- `context` (header, string, optional)
- `context-fallback` (header, boolean, optional)

**Request body:** `BulkAssetPublishedUpdateRequest` — `assetIds` array, `published` boolean

**Response:** `BulkAssetPublishedUpdateResponse` — per-asset result

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().put('/api/media-bank/assets/published', {
  assetIds: ['assetId1', 'assetId2'],
  published: true,
});
```

---

### PUT /assets/{id}/published

**Tags:** Assets
**Permission:** MEDIA / WRITE
**Key params:**
- `id` (path, string, required)
- `context` (header, string, optional)
- `context-fallback` (header, boolean, optional)

**Request body:** `UpdateAssetAttributeRequestBoolean` — `value` (boolean)

**Response:** `DescribeWorkingAssetResponse` — updated asset

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().put(`/api/media-bank/assets/${assetId}/published`, {
  value: true,
});
```

---

### PUT /assets/{id}/name

**Tags:** Assets
**Permission:** MEDIA / WRITE
**Key params:**
- `id` (path, string, required)
- `context` (header, string, optional)

**Request body:** `UpdateAssetAttributeRequestString` — `value` (string)

**Response:** `DescribeWorkingAssetResponse`

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().put(`/api/media-bank/assets/${assetId}/name`, {
  value: 'New Asset Name',
});
```

---

### PUT /assets/{id}/number

**Tags:** Assets
**Permission:** MEDIA / WRITE
**Key params:**
- `id` (path, string, required)
- `context` (header, string, optional)

**Request body:** `UpdateAssetAttributeRequestString` — `value` (string)

**Response:** `DescribeWorkingAssetResponse`

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().put(`/api/media-bank/assets/${assetId}/number`, {
  value: 'ASSET-2024-001',
});
```

---

### PUT /assets/{id}/description

**Tags:** Assets
**Permission:** MEDIA / WRITE
**Key params:**
- `id` (path, string, required)
- `context` (header, string, optional)

**Request body:** `UpdateAssetAttributeRequestString` — `value` (string)

**Response:** `DescribeWorkingAssetResponse`

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().put(`/api/media-bank/assets/${assetId}/description`, {
  value: 'Front-facing product image on white background.',
});
```

---

### PUT /assets/{id}/internal

**Tags:** Assets
**Permission:** MEDIA_INTERNAL / WRITE
**Key params:**
- `id` (path, string, required)

**Request body:** `UpdateAssetAttributeRequestBoolean` — `value` (boolean)

**Response:** `DescribeWorkingAssetResponse`

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().put(`/api/media-bank/assets/${assetId}/internal`, {
  value: true,
});
```

---

### PUT /assets/{id}/alpha-channel

**Tags:** Assets
**Permission:** MEDIA / WRITE
**Key params:**
- `id` (path, string, required)

**Request body:** `UpdateAssetAttributeRequestBoolean` — `value` (boolean)

**Response:** `DescribeWorkingAssetResponse`

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().put(`/api/media-bank/assets/${assetId}/alpha-channel`, {
  value: true,
});
```

---

### PUT /assets/{id}/clipping-path

**Tags:** Assets
**Permission:** MEDIA / WRITE
**Key params:**
- `id` (path, string, required)

**Request body:** `UpdateAssetAttributeRequestBoolean` — `value` (boolean)

**Response:** `DescribeWorkingAssetResponse`

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().put(`/api/media-bank/assets/${assetId}/clipping-path`, {
  value: true,
});
```

---

### GET /assets/{id}/attributes

**Tags:** Asset attributes
**Permission:** MEDIA / READ
**Key params:**
- `id` (path, string, required)
- `page` (query, integer, optional, default: 0)
- `pageSize` (query, integer, optional, default: 100, max: 1000)
- `context` (header, string, optional)
- `context-fallback` (header, boolean, optional)

**Response:** `ListableAssetAttributesResponse` — paginated list of attribute values (id, definitionId, value, type)

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().get(`/api/media-bank/assets/${assetId}/attributes`, {
  params: { page: 0, pageSize: 100 },
  headers: { context: 'en_US' },
});
```

---

### POST /assets/{id}/attributes/{definitionId}

**Tags:** Asset attributes
**Permission:** MEDIA / WRITE
**Key params:**
- `id` (path, string, required) — asset id
- `definitionId` (path, string, required) — attribute definition id

**Response:** 204 — attribute assigned to asset

**PBC usage:**
```typescript
await getAxiosInstance().post(`/api/media-bank/assets/${assetId}/attributes/${definitionId}`);
```

---

### DELETE /assets/{id}/attributes/{definitionId}

**Tags:** Asset attributes
**Permission:** MEDIA / WRITE
**Key params:**
- `id` (path, string, required)
- `definitionId` (path, string, required)

**Response:** 204 — attribute unassigned

**PBC usage:**
```typescript
await getAxiosInstance().delete(`/api/media-bank/assets/${assetId}/attributes/${definitionId}`);
```

---

### PUT /assets/{id}/attributes/{definitionId}/simple

**Tags:** Asset attributes
**Permission:** MEDIA / WRITE
**Key params:**
- `id` (path, string, required)
- `definitionId` (path, string, required)
- `context` (header, string, optional)

**Request body:** `UpdateSimpleAssetAttributeValueRequest` — `value` (string)

**Response:** 204

**PBC usage:**
```typescript
await getAxiosInstance().put(
  `/api/media-bank/assets/${assetId}/attributes/${definitionId}/simple`,
  { value: 'some text value' },
  { headers: { context: 'en_US' } },
);
```

---

### PUT /assets/{id}/attributes/{definitionId}/select

**Tags:** Asset attributes
**Permission:** MEDIA / WRITE
**Key params:**
- `id` (path, string, required)
- `definitionId` (path, string, required)
- `context` (header, string, optional)

**Request body:** `UpdateSelectAssetAttributeValuesRequest` — `values` (array of option ids)

**Response:** 204

**PBC usage:**
```typescript
await getAxiosInstance().put(
  `/api/media-bank/assets/${assetId}/attributes/${definitionId}/select`,
  { values: ['optionId1', 'optionId2'] },
);
```

---

### PUT /assets/{id}/attributes/{definitionId}/dictionary

**Tags:** Asset attributes
**Permission:** MEDIA / WRITE
**Key params:**
- `id` (path, string, required)
- `definitionId` (path, string, required)
- `context` (header, string, optional)

**Request body:** `UpdateDictionaryAssetAttributeValuesRequest` — `values` (array of dictionary entry ids)

**Response:** 204

**PBC usage:**
```typescript
await getAxiosInstance().put(
  `/api/media-bank/assets/${assetId}/attributes/${definitionId}/dictionary`,
  { values: ['dictEntryId1'] },
  { headers: { context: 'en_US' } },
);
```

---

### POST /assets/attributes/add/by-ids

**Tags:** Asset attributes
**Permission:** MEDIA / WRITE
**Request body:**
- `assetIds` (array of strings, required)
- `attributeDefinitionId` (string, required)

**Response:** 204 — attribute definition assigned to all specified assets

**PBC usage:**
```typescript
await getAxiosInstance().post('/api/media-bank/assets/attributes/add/by-ids', {
  assetIds: ['assetId1', 'assetId2'],
  attributeDefinitionId: 'defId1',
});
```

---

### PUT /assets/attributes/by-ids

**Tags:** Asset attributes
**Permission:** MEDIA / WRITE
**Request body:** `SaveAttributeToAssetsByIdsRequest` — `assetIds`, `attributeDefinitionId`, `value`

**Response:** 204 — attribute upserted on all specified assets

**PBC usage:**
```typescript
await getAxiosInstance().put('/api/media-bank/assets/attributes/by-ids', {
  assetIds: ['assetId1', 'assetId2'],
  attributeDefinitionId: 'defId1',
  value: 'bulk value',
});
```

---

### POST /assets/attributes/by-ids

**Tags:** Asset attributes
**Permission:** MEDIA / WRITE
**Key params:**
- `failOnMissingAttribute` (query, boolean, optional, default: true)

**Request body:** `SaveAttributeToAssetsByIdsRequest` — `assetIds`, `attributeDefinitionId`, `value`

**Response:** 204 — attribute updated on all specified assets

**PBC usage:**
```typescript
await getAxiosInstance().post('/api/media-bank/assets/attributes/by-ids', {
  assetIds: ['assetId1'],
  attributeDefinitionId: 'defId1',
  value: 'updated value',
}, { params: { failOnMissingAttribute: false } });
```

---

### POST /assets/attributes/dictionary/append/by-ids

**Tags:** Asset attributes
**Permission:** MEDIA / WRITE
**Key params:**
- `failOnMissingAttribute` (query, boolean, optional, default: true)
- `context` (header, string, optional)

**Request body:** `DictionaryAssetAttributeValuesRequest` — `assetIds`, `attributeDefinitionId`, `values` (array)

**Response:** 204 — dictionary values appended

**PBC usage:**
```typescript
await getAxiosInstance().post('/api/media-bank/assets/attributes/dictionary/append/by-ids', {
  assetIds: ['assetId1'],
  attributeDefinitionId: 'defId1',
  values: ['dictEntryId1'],
});
```

---

### POST /assets/attributes/dictionary/deduct/by-ids

**Tags:** Asset attributes
**Permission:** MEDIA / WRITE
**Key params:**
- `failOnMissingAttribute` (query, boolean, optional, default: true)
- `context` (header, string, optional)

**Request body:** `DictionaryAssetAttributeValuesRequest` — `assetIds`, `attributeDefinitionId`, `values` (array)

**Response:** 204 — dictionary values removed

**PBC usage:**
```typescript
await getAxiosInstance().post('/api/media-bank/assets/attributes/dictionary/deduct/by-ids', {
  assetIds: ['assetId1'],
  attributeDefinitionId: 'defId1',
  values: ['dictEntryId1'],
});
```

---

### POST /assets/attributes/select/append/by-ids

**Tags:** Asset attributes
**Permission:** MEDIA / WRITE
**Key params:**
- `failOnMissingAttribute` (query, boolean, optional, default: true)
- `context` (header, string, optional)

**Request body:** `AppendSelectValuesToAssetsAttributeParams` — `assetIds`, `attributeDefinitionId`, `values` (option ids)

**Response:** 204

**PBC usage:**
```typescript
await getAxiosInstance().post('/api/media-bank/assets/attributes/select/append/by-ids', {
  assetIds: ['assetId1'],
  attributeDefinitionId: 'defId1',
  values: ['optionId1'],
});
```

---

### POST /assets/attributes/select/deduct/by-ids

**Tags:** Asset attributes
**Permission:** MEDIA / WRITE
**Key params:**
- `failOnMissingAttribute` (query, boolean, optional, default: true)
- `context` (header, string, optional)

**Request body:** `DeductSelectValuesToAssetsAttributeParams` — `assetIds`, `attributeDefinitionId`, `values` (option ids to remove)

**Response:** 204

**PBC usage:**
```typescript
await getAxiosInstance().post('/api/media-bank/assets/attributes/select/deduct/by-ids', {
  assetIds: ['assetId1'],
  attributeDefinitionId: 'defId1',
  values: ['optionId1'],
});
```

---

### DELETE /assets/attributes

**Tags:** Asset attributes
**Permission:** MEDIA / WRITE
**Request body:** `DeleteProductsAttributesBulkRequest` — `assetIds` array, `attributeDefinitionIds` array

**Response:** 204 — attributes unassigned from all specified assets

**PBC usage:**
```typescript
await getAxiosInstance().delete('/api/media-bank/assets/attributes', {
  data: {
    assetIds: ['assetId1'],
    attributeDefinitionIds: ['defId1', 'defId2'],
  },
});
```

---

## Tasks

> Base path prefix: `/api/tasks/...` on `api.test.bluestonepim.com`

**Async pattern note:** The Tasks API is a workflow/collaboration API, not a background-job processor. Operations that return 202 Accepted (assign, update, complete, revoke) are fire-and-forget acknowledgements — no task-id polling is required. `POST /tasks` returns a `Resource-Id` response header containing the newly created task id; use that id for all subsequent calls.

---

### GET /tasks

**Tags:** Tasks
**Permission:** none specified (OAuth2 required)
**Key params:**
- `productId` (query, string, optional) — filter tasks associated with a product
- `context` (query, string, optional) — task context/workspace
- `sortField` (query, string, optional, default: `TITLE`) — `TITLE` or `DUE_DATE`
- `sortDirection` (query, string, optional, default: `ASC`) — `ASC` or `DESC`
- `page` (query, integer, optional, default: 0)
- `pageSize` (query, integer, optional, default: 1000)

**Response:** `ListablePresentableGridTask` — `data` array of task grid rows (id, title, dueDate, assignee, status, context)

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().get('/api/tasks/tasks', {
  params: { productId: 'prod-123', sortField: 'DUE_DATE', sortDirection: 'ASC' },
});
```

---

### POST /tasks

**Tags:** Tasks
**Permission:** none specified (OAuth2 required)
**Request body:**
- `title` (string, required, minLength: 1)
- `description` (string, optional)
- `assignee` (string, required, minLength: 1) — username or user group name
- `assigneeType` (string, optional) — `user` or `userGroup`
- `dueDate` (string, optional) — ISO date string
- `context` (string, optional) — workspace/context identifier
- `isPrivate` (boolean, optional)
- `assigneeReminderTime` (integer, optional, min: 1) — hours before due date
- `creatorReminderTime` (integer, optional, min: 1) — hours before due date

**Response:** 201; response header `Resource-Id` contains the new task id

**PBC usage:**
```typescript
const response = await getAxiosInstance().post('/api/tasks/tasks', {
  title: 'Review product copy',
  assignee: 'jane.doe',
  assigneeType: 'user',
  dueDate: '2026-04-01',
  context: 'en_US',
});
const newTaskId = response.headers['resource-id'];
```

---

### GET /tasks/{taskId}

**Tags:** Tasks
**Permission:** none specified (OAuth2 required)
**Key params:**
- `taskId` (path, string, required)

**Response:** `PresentableTaskResponse` — full task detail (id, title, description, assignee, assigneeType, dueDate, status, context, isPrivate, products, comments, reminders)

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().get(`/api/tasks/tasks/${taskId}`);
```

---

### PUT /tasks/{taskId}

**Tags:** Tasks
**Permission:** none specified (OAuth2 required)
**Key params:**
- `taskId` (path, string, required)

**Request body:**
- `title` (string, optional)
- `description` (string, optional)
- `dueDate` (string, optional) — ISO date string
- `context` (string, optional)

**Response:** 202 Accepted

**PBC usage:**
```typescript
await getAxiosInstance().put(`/api/tasks/tasks/${taskId}`, {
  title: 'Updated task title',
  dueDate: '2026-04-15',
});
```

---

### GET /tasks/archived

**Tags:** Tasks
**Permission:** none specified (OAuth2 required)
**Key params:**
- `page` (query, integer, optional, default: 0)
- `pageSize` (query, integer, optional, default: 1000)

**Response:** `ListablePresentableGridTask` — paginated list of archived tasks

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().get('/api/tasks/tasks/archived', {
  params: { page: 0, pageSize: 50 },
});
```

---

### GET /tasks/folders

**Tags:** Tasks
**Permission:** none specified (OAuth2 required)
**Key params:** none

**Response:** `PresentableFoldersList` — list of available task folders/workspaces

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().get('/api/tasks/tasks/folders');
```

---

### GET /tasks/users/{userId}/{taskType}

**Tags:** Tasks
**Permission:** none specified (OAuth2 required)
**Key params:**
- `userId` (path, string, required)
- `taskType` (path, enum, required) — `all`, `archived`, `assigned`, or `created`
- `page` (query, integer, optional, default: 0)
- `pageSize` (query, integer, optional, default: 1000)

**Response:** `ListablePresentableGridTask` — filtered task list for the given user

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().get(`/api/tasks/tasks/users/${userId}/assigned`, {
  params: { page: 0, pageSize: 100 },
});
```

---

### GET /tasks/userGroups/{userGroupId}/{taskType}

**Tags:** Tasks
**Permission:** none specified (OAuth2 required)
**Key params:**
- `userGroupId` (path, string, required)
- `taskType` (path, enum, required) — `all`, `archived`, `assigned`, or `created`
- `page` (query, integer, optional, default: 0)
- `pageSize` (query, integer, optional, default: 1000)

**Response:** `ListablePresentableGridTask` — filtered task list for the given user group

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().get(
  `/api/tasks/tasks/userGroups/${userGroupId}/all`,
  { params: { pageSize: 100 } },
);
```

---

### PUT /tasks/{taskId}/assign

**Tags:** Tasks
**Permission:** none specified (OAuth2 required)
**Key params:**
- `taskId` (path, string, required)

**Request body:**
- `assignee` (string, optional) — username or user group name
- `assigneeType` (string, optional) — `user` or `userGroup`

**Response:** 202 Accepted

**PBC usage:**
```typescript
await getAxiosInstance().put(`/api/tasks/tasks/${taskId}/assign`, {
  assignee: 'john.smith',
  assigneeType: 'user',
});
```

---

### POST /tasks/{taskId}/products

**Tags:** Tasks
**Permission:** none specified (OAuth2 required)
**Key params:**
- `taskId` (path, string, required)

**Request body:**
- `productIds` (array of strings, required)

**Response:** 202 Accepted

**PBC usage:**
```typescript
await getAxiosInstance().post(`/api/tasks/tasks/${taskId}/products`, {
  productIds: ['prod-123', 'prod-456'],
});
```

---

### PUT /tasks/{taskId}/products/{productId}/complete

**Tags:** Tasks
**Permission:** none specified (OAuth2 required)
**Key params:**
- `taskId` (path, string, required)
- `productId` (path, string, required)

**Response:** 202 Accepted

**PBC usage:**
```typescript
await getAxiosInstance().put(`/api/tasks/tasks/${taskId}/products/${productId}/complete`);
```

---

### PUT /tasks/{taskId}/products/{productId}/revoke

**Tags:** Tasks
**Permission:** none specified (OAuth2 required)
**Key params:**
- `taskId` (path, string, required)
- `productId` (path, string, required)

**Response:** 201

**PBC usage:**
```typescript
await getAxiosInstance().put(`/api/tasks/tasks/${taskId}/products/${productId}/revoke`);
```

---

### DELETE /tasks/{taskId}/products/{productId}

**Tags:** Tasks
**Permission:** none specified (OAuth2 required)
**Key params:**
- `taskId` (path, string, required)
- `productId` (path, string, required)

**Response:** 201

**PBC usage:**
```typescript
await getAxiosInstance().delete(`/api/tasks/tasks/${taskId}/products/${productId}`);
```

---

### GET /userTasks

**Tags:** User tasks
**Permission:** none specified (OAuth2 required)
**Key params:**
- `page` (query, integer, optional, default: 0)
- `pageSize` (query, integer, optional, default: 1000)

**Response:** `ListablePresentableGridTask` — all tasks for the authenticated user

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().get('/api/tasks/userTasks', {
  params: { page: 0, pageSize: 50 },
});
```

---

### GET /userTasks/assigned

**Tags:** User tasks
**Permission:** none specified (OAuth2 required)
**Key params:**
- `page` (query, integer, optional, default: 0)
- `pageSize` (query, integer, optional, default: 1000)

**Response:** `ListablePresentableGridTask` — tasks assigned to the authenticated user

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().get('/api/tasks/userTasks/assigned');
```

---

### GET /userTasks/{taskId}

**Tags:** User tasks
**Permission:** none specified (OAuth2 required)
**Key params:**
- `taskId` (path, string, required)

**Response:** `PresentableTask` — full task detail from the current user's perspective

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().get(`/api/tasks/userTasks/${taskId}`);
```

---

### PUT /userTasks/{taskId}/complete

**Tags:** User tasks
**Permission:** none specified (OAuth2 required)
**Key params:**
- `taskId` (path, string, required)

**Response:** 200 OK

**PBC usage:**
```typescript
await getAxiosInstance().put(`/api/tasks/userTasks/${taskId}/complete`);
```

---

### PUT /userTasks/{taskId}/revoke

**Tags:** User tasks
**Permission:** none specified (OAuth2 required)
**Key params:**
- `taskId` (path, string, required)

**Response:** 202 Accepted — marks the task incomplete again

**PBC usage:**
```typescript
await getAxiosInstance().put(`/api/tasks/userTasks/${taskId}/revoke`);
```

---

### PUT /userTasks/{taskId}/important

**Tags:** User tasks
**Permission:** none specified (OAuth2 required)
**Key params:**
- `taskId` (path, string, required)

**Response:** 200 OK

**PBC usage:**
```typescript
await getAxiosInstance().put(`/api/tasks/userTasks/${taskId}/important`);
```

---

### PUT /userTasks/{taskId}/unimportant

**Tags:** User tasks
**Permission:** none specified (OAuth2 required)
**Key params:**
- `taskId` (path, string, required)

**Response:** 200 OK

**PBC usage:**
```typescript
await getAxiosInstance().put(`/api/tasks/userTasks/${taskId}/unimportant`);
```

---

### GET /comments/{taskId}

**Tags:** Task comments
**Permission:** none specified (OAuth2 required)
**Key params:**
- `taskId` (path, string, required)

**Response:** `TaskComment` array — id, message, userId, createdAt, replies

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().get(`/api/tasks/comments/${taskId}`);
```

---

### POST /comments/{taskId}

**Tags:** Task comments
**Permission:** none specified (OAuth2 required)
**Key params:**
- `taskId` (path, string, required)

**Request body:**
- `message` (string) — comment text

**Response:** `AddCommentResponse` — id, message, userId, createdAt

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().post(`/api/tasks/comments/${taskId}`, {
  message: 'Please update the product title to match the style guide.',
});
```

---

### POST /comments/{taskId}/{commentId}

**Tags:** Task comments
**Permission:** TASKS / UPDATE
**Key params:**
- `taskId` (path, string, required)
- `commentId` (path, string, required) — parent comment id

**Request body:**
- `message` (string) — reply text

**Response:** `AddCommentReplyResponse` — id, parentId, message, userId, createdAt

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().post(
  `/api/tasks/comments/${taskId}/${commentId}`,
  { message: 'Done, title updated.' },
);
```

---

### GET /tasks/reminders/task/{taskId}

**Tags:** Tasks reminders
**Permission:** none specified (OAuth2 required)
**Key params:**
- `taskId` (path, string, required)

**Response:** `TaskReminderListResponse` — array of reminders (id, taskId, remindBeforeTime in hours)

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().get(`/api/tasks/tasks/reminders/task/${taskId}`);
```

---

### POST /tasks/reminders/task/{taskId}

**Tags:** Tasks reminders
**Permission:** none specified (OAuth2 required)
**Key params:**
- `taskId` (path, string, required)

**Request body:**
- `remindBeforeTime` (integer, required, min: 1) — hours before due date to send reminder; max 5 reminders per task

**Response:** 201 Created

**PBC usage:**
```typescript
await getAxiosInstance().post(`/api/tasks/tasks/reminders/task/${taskId}`, {
  remindBeforeTime: 24,
});
```

---

### PUT /tasks/reminders/{reminderId}

**Tags:** Tasks reminders
**Permission:** none specified (OAuth2 required)
**Key params:**
- `reminderId` (path, string, required)

**Request body:**
- `remindBeforeTime` (integer, required, min: 1)

**Response:** 202 Accepted

**PBC usage:**
```typescript
await getAxiosInstance().put(`/api/tasks/tasks/reminders/${reminderId}`, {
  remindBeforeTime: 48,
});
```

---

### DELETE /tasks/reminders/{reminderId}

**Tags:** Tasks reminders
**Permission:** none specified (OAuth2 required)
**Key params:**
- `reminderId` (path, string, required)

**Response:** 204 No Content

**PBC usage:**
```typescript
await getAxiosInstance().delete(`/api/tasks/tasks/reminders/${reminderId}`);
```

---

## History

> Base path prefix: `/api/history/...` on `api.test.bluestonepim.com`

**Entity types:** The `entityType` path parameter accepts the entity domain string (e.g., `PRODUCT`, `CATEGORY`, `ATTRIBUTE`, `ASSET`, `TASK`, `USER`). The exact set of supported values is defined by the Bluestone PIM platform and may vary by tenant configuration.

**Event types available via `eventType` filter:** Common values include `CREATED`, `UPDATED`, `DELETED`, `PUBLISHED`, `UNPUBLISHED`, `ASSIGNED`, `UNASSIGNED`. Filter by `field` to narrow to a specific attribute change.

**Common filter params shared across all History endpoints:**
- `eventType` (string) — event category (e.g., `CREATED`, `UPDATED`)
- `field` (string) — specific field name that changed
- `origin` (string) — system origin of the change
- `userId` (string) — author of the change
- `fromTime` / `toTime` (integer, epoch ms) — time range filter
- `eventId` (string) — specific event id
- `metadataEntityId` (string) — filter by related entity id in metadata
- `context` (string) — language/context of the change

---

### GET /history

**Tags:** History
**Permission:** HISTORY / READ
**Key params:**
- `page` (query, integer, optional, min: 0)
- `pageSize` (query, integer, optional, min: 1, max: 1000)
- plus all common filter params above

**Response:** `ListableHistoryEntryDto` — `data` array of `HistoryEntryDto` (id, entityType, eventType, entityIds, date, userId, origin, metadata)

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().get('/api/history/history', {
  params: {
    eventType: 'UPDATED',
    userId: 'user-abc',
    fromTime: Date.now() - 86400000, // last 24 hours
    pageSize: 100,
  },
});
```

---

### GET /history/{entityType}

**Tags:** History
**Permission:** HISTORY / READ
**Key params:**
- `entityType` (path, string, required) — e.g., `PRODUCT`, `ASSET`, `CATEGORY`
- `page` (query, integer, optional)
- `pageSize` (query, integer, optional, max: 1000)
- plus all common filter params

**Response:** `ListableHistoryEntryDto` — filtered history entries for that entity type

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().get('/api/history/history/PRODUCT', {
  params: { eventType: 'UPDATED', pageSize: 200 },
});
```

---

### GET /history/{entityType}/{id}

**Tags:** History
**Permission:** HISTORY / READ
**Key params:**
- `entityType` (path, string, required)
- `id` (path, string, required) — specific entity id
- `page` (query, integer, optional)
- `pageSize` (query, integer, optional, max: 1000)
- plus all common filter params

**Response:** `ListableHistoryEntryDto` — all history events for a single entity instance; each entry includes `metadata` array (field, value, oldValue, type, entity)

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().get(`/api/history/history/PRODUCT/${productId}`, {
  params: { pageSize: 50 },
});
// data.data[0].metadata contains [{ field: 'name', value: 'New Name', oldValue: 'Old Name' }]
```

---

### GET /history/{entityType}/{id}/count

**Tags:** History
**Permission:** HISTORY / READ
**Key params:**
- `entityType` (path, string, required)
- `id` (path, string, required)
- plus all common filter params (no pagination)

**Response:** `HistoryEntryCountResponse` — `{ count: number }`

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().get(`/api/history/history/ASSET/${assetId}/count`, {
  params: { eventType: 'UPDATED' },
});
console.log(data.count); // total number of UPDATED events for this asset
```

---

### GET /history/{entityType}/scroll

**Tags:** History
**Permission:** HISTORY / READ

> Use this endpoint (not the deprecated `/cursor` endpoint) for iterating over large result sets via Point-In-Time (PIT) search. Each response returns a new `scrollId`; pass it back in the next request. Continue until no `scrollId` is returned or the response `data` array is empty.

**Key params:**
- `entityType` (path, string, required)
- `scrollId` (query, string, optional) — omit on first request; pass value from previous response on subsequent requests
- `count` (query, integer, optional, min: 1, max: 100) — items per page
- plus all common filter params

**Response:** `ListableWithCursorHistoryEntryDto` — `{ nextScrollId: string, data: HistoryEntryDto[] }`

**PBC usage:**
```typescript
let scrollId: string | undefined;
do {
  const { data } = await getAxiosInstance().get('/api/history/history/PRODUCT/scroll', {
    params: { count: 100, eventType: 'UPDATED', scrollId },
  });
  // process data.data
  scrollId = data.nextCursor; // use nextCursor as the scrollId for next call
} while (scrollId);
```

---

### GET /history/search/{entityType}

**Tags:** History
**Permission:** HISTORY / READ
**Key params:**
- `entityType` (path, string, required)
- `query` (query, string, optional, minLength: 2) — full-text search term
- `context` (query, string, optional)
- `page` (query, integer, optional)
- `pageSize` (query, integer, optional, max: 1000)

**Response:** `ListableHistoryEntryDto` — history entries matching the search query for that entity type

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().get('/api/history/history/search/PRODUCT', {
  params: { query: 'price change', pageSize: 50 },
});
```

---

### GET /history/search/{entityType}/{id}

**Tags:** History
**Permission:** HISTORY / READ
**Key params:**
- `entityType` (path, string, required)
- `id` (path, string, required) — specific entity id
- `query` (query, string, optional, minLength: 2)
- `context` (query, string, optional)
- `page` (query, integer, optional)
- `pageSize` (query, integer, optional, max: 1000)

**Response:** `ListableHistoryEntryDto` — history entries for a single entity matching the search query

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().get(
  `/api/history/history/search/PRODUCT/${productId}`,
  { params: { query: 'description', pageSize: 25 } },
);
```

---

## Completeness Score

### GET /api/completeness-score/scores/{entityId}/{context}
**Tags:** Scores
**Permission:** `COMPLETENESS_SCORE` / READ
**Key params:**
- `entityId` (path, string, required) — PIM entity ID of the product
- `context` (path, string, required) — language/context ID (e.g. `en`, `Lx`)

**Response:** `ScoreDetailedResponse` — per-requirement breakdown of completeness for the entity in the given context

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().get(
  `/api/completeness-score/scores/${productId}/${context}`
);
```

---

### POST /api/completeness-score/scores/list
**Tags:** Scores
**Permission:** `COMPLETENESS_SCORE` / READ
**Key params:** none (all in body)
**Request body:**
- `page` (integer, required) — zero-based page index
- `pageSize` (integer, required, max 100) — items per page
- `entityIds` (array\<string\>, optional) — filter to specific entity IDs
- `contexts` (array\<string\>, optional) — filter to specific contexts

**Response:** `ScoreListResponse` — `data[]` each with `entityId`, `score` (number), `context`, `date` (epoch ms), `validationStatus` (`VALID`|`INVALID`), `entityLastUpdate`

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().post(
  `/api/completeness-score/scores/list`,
  {
    page: 0,
    pageSize: 50,
    entityIds: [productId],
    contexts: ['en'],
  }
);
```

---

### POST /api/completeness-score/scores/category/list
**Tags:** Scores
**Permission:** `COMPLETENESS_SCORE` / READ
**Key params:** none (all in body)
**Request body:**
- `page` (integer, required)
- `pageSize` (integer, required, max 100)
- `includeUnassigned` (boolean, required) — include score for unassigned products
- `categoryIds` (array\<string\>, optional) — limit to specific categories
- `contexts` (array\<string\>, optional) — limit to specific contexts

**Response:** `CategoryScoreListResponse` — `data[]` each with `categoryId`, `score`, `context`

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().post(
  `/api/completeness-score/scores/category/list`,
  {
    page: 0,
    pageSize: 50,
    includeUnassigned: true,
    categoryIds: [categoryId],
    contexts: ['en'],
  }
);
```

---

### GET /api/completeness-score/scores/status/{context}
**Tags:** Scores
**Permission:** `COMPLETENESS_SCORE` / READ
**Key params:**
- `context` (path, string, required) — context to check calculation status for

**Response:** `ScoreCompletenessStateResponse` — current calculation status for the context

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().get(
  `/api/completeness-score/scores/status/${context}`
);
```

---

### GET /api/completeness-score/validations/{entityId}/{context}
**Tags:** Validation
**Permission:** `COMPLETENESS_SCORE` / READ
**Key params:**
- `entityId` (path, string, required) — product entity ID
- `context` (path, string, required) — context ID

**Response:** `ProductValidationIssuesListResponse` — `data[]` each with `validationType` (enum: `INVALID_ATTRIBUTE_VALUE`, `MISSING_CATEGORY_ATTRIBUTE`, `MISSING_CATEGORY_VALUE`, `INVALID_CATEGORY_VALUE`, `MISSING_COMPOUND_ATTRIBUTE`, `MISSING_VARIANT_ATTRIBUTE`, `MISSING_VARIANT_VALUE`, `INVALID_VARIANT_VALUE`, `DICTIONARY_VALUE_NOT_EXIST_IN_FILTER`) and `validationDetails`

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().get(
  `/api/completeness-score/validations/${productId}/${context}`
);
```

---

### POST /api/completeness-score/validations/by-ids
**Tags:** Validation
**Permission:** `COMPLETENESS_SCORE` / READ
**Key params:** none (all in body)
**Request body:**
- `entityIds` (array\<string\>, required, 1–100) — product entity IDs to validate
- `context` (string, required) — context ID

**Response:** `BulkValidationListResponse` — `data[]` each with `entityId` and `validations[]` (array of `ProductValidationIssuesResponse`). Only entities that exist are included.

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().post(
  `/api/completeness-score/validations/by-ids`,
  {
    entityIds: [productId1, productId2],
    context: 'en',
  }
);
```

---

### POST /api/completeness-score/validations/product/attribute
**Tags:** Validation
**Permission:** `COMPLETENESS_SCORE` / READ
**Key params:** none (all in body)
**Request body:**
- `productId` (string, required) — product entity ID
- `context` (string, required) — context ID
- `attribute` (object, required) — `{ definitionId: string, value?: string, selectValuesIds?: string[], dictionaryValuesIds?: string[] }`

**Response:** `AttributeValueValidationIssuesListResponse` — `data[]` each with `validationType` and `validationDetails`

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().post(
  `/api/completeness-score/validations/product/attribute`,
  {
    productId,
    context: 'en',
    attribute: { definitionId, value: '42' },
  }
);
```

---

### POST /api/completeness-score/validations/definition/value
**Tags:** Validation
**Permission:** `COMPLETENESS_SCORE` / READ
**Key params:** none (all in body)
**Request body:**
- `context` (string, required) — context ID
- `attribute` (object, required) — `{ definitionId: string, value?: string }`

**Response:** `DefinitionValueValidationIssuesListResponse` — `data[]` each with `validationDetails`

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().post(
  `/api/completeness-score/validations/definition/value`,
  {
    context: 'en',
    attribute: { definitionId, value: 'some value' },
  }
);
```

---

### POST /api/completeness-score/requirements/list
**Tags:** Requirements
**Permission:** `COMPLETENESS_SCORE` / READ
**Key params:** none (all in body)
**Request body:** `RequirementListRequest` — pagination and filter fields (consult spec for full filter options)

**Response:** `RequirementListResponse` — `data[]` of requirement definitions including `requirementType`, `params`, `weight`, `includedProductTypes`, `includedCategories`, `excludedCategories`

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().post(
  `/api/completeness-score/requirements/list`,
  { page: 0, pageSize: 50 }
);
```

---

### POST /api/completeness-score/requirements/count
**Tags:** Requirements
**Permission:** `COMPLETENESS_SCORE` / READ
**Key params:** none (all in body)
**Request body:** `RequirementCountRequest` — filter criteria

**Response:** `RequirementCountResponse` — total count of matching requirements

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().post(
  `/api/completeness-score/requirements/count`,
  {}
);
```

---

### GET /api/completeness-score/requirements/entity/{entityId}/{context}
**Tags:** Requirements
**Permission:** `COMPLETENESS_SCORE` / READ
**Key params:**
- `entityId` (path, string, required) — product entity ID
- `context` (path, string, required) — context ID

**Response:** `ScoreDetailedResponse` — requirements applicable to the specific entity in the given context

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().get(
  `/api/completeness-score/requirements/entity/${productId}/${context}`
);
```

---

### POST /api/completeness-score/requirements
**Tags:** Requirements
**Permission:** `COMPLETENESS_SCORE` / WRITE
**Key params:** none (all in body)
**Request body:**
- `requirementType` (string enum, required) — one of: `ATTRIBUTE_HAS_VALUE`, `MINIMUM_MEDIA_WITH_LABEL`, `PERCENTAGE_OF_ATTRIBUTES_IN_GROUP_HAVE_VALUE`, `IMAGE_WITH_LABEL_HAS_MINIMUM_RESOLUTION`, `PERCENTAGE_OF_ATTRIBUTES_HAVE_VALUE`, `MINIMUM_RELATION_CONNECTIONS`, `IMAGE_WITH_LABEL_HAS_MINIMUM_DENSITY`, `IMAGE_WITH_LABEL_HAS_DEEP_ETCH`, `PRODUCT_ADDED_TO_CATEGORY`
- `params` (object, required) — type-specific params object (see `RequirementParams` mapping in spec)
- `weight` (number, optional, min 1)
- `includedProductTypes` (array\<string\>, optional) — `SINGLE`, `BUNDLE`, `GROUP`, `VARIANT`

**Response:** 201 Created (no body)

**PBC usage:**
```typescript
await getAxiosInstance().post(
  `/api/completeness-score/requirements`,
  {
    requirementType: 'ATTRIBUTE_HAS_VALUE',
    params: { definitionId: 'attr-def-id' },
    weight: 1,
  }
);
```

---

### PUT /api/completeness-score/requirements/{requirementId}
**Tags:** Requirements
**Permission:** `COMPLETENESS_SCORE` / WRITE
**Key params:**
- `requirementId` (path, string, required)

**Request body:**
- `requirementType` (string enum, optional)
- `params` (object, optional) — type-specific params
- `weight` (number, optional, min 1)

**Response:** 200 OK (no body)

**PBC usage:**
```typescript
await getAxiosInstance().put(
  `/api/completeness-score/requirements/${requirementId}`,
  { weight: 2 }
);
```

---

### DELETE /api/completeness-score/requirements/{requirementId}
**Tags:** Requirements
**Permission:** `COMPLETENESS_SCORE` / WRITE
**Key params:**
- `requirementId` (path, string, required)

**Response:** 204 No Content

**PBC usage:**
```typescript
await getAxiosInstance().delete(
  `/api/completeness-score/requirements/${requirementId}`
);
```

---

### PUT /api/completeness-score/requirements/{requirementId}/productType/{productType}/included
**Tags:** Requirements
**Permission:** `COMPLETENESS_SCORE` / WRITE
**Key params:**
- `requirementId` (path, string, required)
- `productType` (path, enum required) — `SINGLE` | `BUNDLE` | `GROUP` | `VARIANT`

**Response:** 200 OK

**PBC usage:**
```typescript
await getAxiosInstance().put(
  `/api/completeness-score/requirements/${requirementId}/productType/SINGLE/included`
);
```

---

### DELETE /api/completeness-score/requirements/{requirementId}/productType/{productType}/included
**Tags:** Requirements
**Permission:** `COMPLETENESS_SCORE` / WRITE
**Key params:**
- `requirementId` (path, string, required)
- `productType` (path, enum required) — `SINGLE` | `BUNDLE` | `GROUP` | `VARIANT`

**Response:** 200 OK

**PBC usage:**
```typescript
await getAxiosInstance().delete(
  `/api/completeness-score/requirements/${requirementId}/productType/SINGLE/included`
);
```

---

### PUT /api/completeness-score/requirements/{requirementId}/categories/{categoryId}/included
**Tags:** Requirements
**Permission:** `COMPLETENESS_SCORE` / WRITE
**Key params:**
- `requirementId` (path, string, required)
- `categoryId` (path, string, required)

**Response:** 200 OK

**PBC usage:**
```typescript
await getAxiosInstance().put(
  `/api/completeness-score/requirements/${requirementId}/categories/${categoryId}/included`
);
```

---

### DELETE /api/completeness-score/requirements/{requirementId}/categories/{categoryId}/included
**Tags:** Requirements
**Permission:** `COMPLETENESS_SCORE` / WRITE
**Key params:**
- `requirementId` (path, string, required)
- `categoryId` (path, string, required)

**Response:** 200 OK

**PBC usage:**
```typescript
await getAxiosInstance().delete(
  `/api/completeness-score/requirements/${requirementId}/categories/${categoryId}/included`
);
```

---

### PUT /api/completeness-score/requirements/{requirementId}/categories/{categoryId}/excluded
**Tags:** Requirements
**Permission:** `COMPLETENESS_SCORE` / WRITE
**Key params:**
- `requirementId` (path, string, required)
- `categoryId` (path, string, required)

**Response:** 200 OK

**PBC usage:**
```typescript
await getAxiosInstance().put(
  `/api/completeness-score/requirements/${requirementId}/categories/${categoryId}/excluded`
);
```

---

### DELETE /api/completeness-score/requirements/{requirementId}/categories/{categoryId}/excluded
**Tags:** Requirements
**Permission:** `COMPLETENESS_SCORE` / WRITE
**Key params:**
- `requirementId` (path, string, required)
- `categoryId` (path, string, required)

**Response:** 200 OK

**PBC usage:**
```typescript
await getAxiosInstance().delete(
  `/api/completeness-score/requirements/${requirementId}/categories/${categoryId}/excluded`
);
```

---

### POST /api/completeness-score/scores/calculation/recalculate/entity
**Tags:** Scores
**Permission:** `COMPLETENESS_SCORE` / WRITE
**Key params:** none (all in body)
**Request body:**
- `context` (string, required) — context ID
- `entityIds` (array\<string\>, required, 1–100) — entity IDs to recalculate (`entityId` single-value field is deprecated)

**Response:** 200 OK (no body)

**PBC usage:**
```typescript
await getAxiosInstance().post(
  `/api/completeness-score/scores/calculation/recalculate/entity`,
  { context: 'en', entityIds: [productId] }
);
```

---

### POST /api/completeness-score/scores/calculation/start
**Tags:** Scores
**Permission:** `COMPLETENESS_SCORE` / WRITE
**Key params:** none (all in body)
**Request body:**
- `context` (string, required) — context to start full recalculation for

**Response:** `CalculationResponse` — `{ message: string }`; HTTP 202 Accepted

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().post(
  `/api/completeness-score/scores/calculation/start`,
  { context: 'en' }
);
```

---

### POST /api/completeness-score/scores/calculation/stop
**Tags:** Scores
**Permission:** `COMPLETENESS_SCORE` / WRITE
**Key params:** none (all in body)
**Request body:**
- `context` (string, required) — context whose ongoing calculation to stop

**Response:** `CalculationResponse` — `{ message: string }`; HTTP 202 Accepted

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().post(
  `/api/completeness-score/scores/calculation/stop`,
  { context: 'en' }
);
```

---

## Labels

### GET /api/labels/labels
**Tags:** Labels
**Permission:** `LABELS` / LIST
**Key params:**
- `page` (query, integer, default 0) — zero-based page index
- `pageSize` (query, integer, default 1000, max 1000) — items per page
- `context` (header, optional) — language/context ID; defaults to `en`
- `context-fallback` (header, optional, boolean, default `true`) — whether to fall back to default translation if context-specific one is missing

**Response:** `ListableLabelResponse` — `data[]` each with `id` (string), `name` (string)

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().get(`/api/labels/labels`, {
  params: { page: 0, pageSize: 100 },
  headers: { context: 'en' },
});
```

---

### POST /api/labels/labels
**Tags:** Labels
**Permission:** `LABELS` / ADD
**Key params:**
- `context` (header, optional) — context for the label name

**Request body:**
- `name` (string, required, minLength 1) — label name

**Response:** 201 Created (no body; use `Location` header or list to retrieve the new ID)

**PBC usage:**
```typescript
await getAxiosInstance().post(
  `/api/labels/labels`,
  { name: 'Featured' },
  { headers: { context: 'en' } }
);
```

---

### GET /api/labels/labels/{id}
**Tags:** Labels
**Permission:** `LABELS` / DETAILS
**Key params:**
- `id` (path, string, required) — label ID
- `context` (header, optional) — context ID; defaults to `en`
- `context-fallback` (header, optional, boolean, default `true`)

**Response:** `LabelResponse` — `{ id: string, name: string }`

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().get(`/api/labels/labels/${labelId}`, {
  headers: { context: 'en' },
});
```

---

### PUT /api/labels/labels/{id}
**Tags:** Labels
**Permission:** `LABELS` / ADD
**Key params:**
- `id` (path, string, required) — label ID to update
- `context` (header, optional) — context in which to update the name

**Request body:**
- `name` (string, required, minLength 1)

**Response:** 204 No Content

**PBC usage:**
```typescript
await getAxiosInstance().put(
  `/api/labels/labels/${labelId}`,
  { name: 'Renamed Label' },
  { headers: { context: 'en' } }
);
```

---

### DELETE /api/labels/labels/{id}
**Tags:** Labels
**Permission:** `LABELS` / DELETE
**Key params:**
- `id` (path, string, required) — label ID to delete

**Response:** 204 No Content

**PBC usage:**
```typescript
await getAxiosInstance().delete(`/api/labels/labels/${labelId}`);
```

---

### POST /api/labels/labels/by-ids
**Tags:** Labels
**Permission:** `LABELS` / LIST
**Key params:**
- `context` (header, optional) — context ID; defaults to `en`
- `context-fallback` (header, optional, boolean, default `true`)

**Request body:**
- `ids` (array\<string\>, required, 0–100) — label IDs to retrieve

**Response:** `ListableLabelResponse` — `data[]` each with `id` and `name`

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().post(
  `/api/labels/labels/by-ids`,
  { ids: [labelId1, labelId2] },
  { headers: { context: 'en' } }
);
```

---

## Metadata

### GET /api/metadata/provider
**Tags:** Provider
**Permission:** `METADATA_PROVIDER` / READ
**Key params:** none

**Response:** `ListProviderResponse` — `data[]` each with `id`, `name`, `number`, `entityType` (`PRODUCT`|`CATEGORY`)

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().get(`/api/metadata/provider`);
```

---

### POST /api/metadata/provider/by-numbers
**Tags:** Provider
**Permission:** `METADATA_PROVIDER` / READ
**Key params:** none (all in body)
**Request body:**
- `numbers` (array\<string\>, required, 0–100) — provider numbers to look up

**Response:** `ListProviderResponse` — `data[]` each with `id`, `name`, `number`, `entityType`

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().post(
  `/api/metadata/provider/by-numbers`,
  { numbers: ['my-plugin-provider'] }
);
```

---

### POST /api/metadata/metadata/{providerId}/by-entities
**Tags:** Metadata
**Permission:** `METADATA` / READ
**Key params:**
- `providerId` (path, string, required) — provider ID (obtain from `/api/metadata/provider`)
- `context-fallback` (header, boolean, optional, default `true`)

**Request body:**
- `entitiesIds` (array\<string\>, required, 0–100) — entity (product/category) IDs
- `contextId` (string, required) — context ID

**Response:** `GetMetadataForEntitiesResponse` — `data[]` each with `entityId`, `providerName`, `metadata` (arbitrary JSON), `contextId`

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().post(
  `/api/metadata/metadata/${providerId}/by-entities`,
  { entitiesIds: [productId], contextId: 'en' }
);
```

---

### POST /api/metadata/metadata/{providerId}
**Tags:** Metadata
**Permission:** `METADATA` / WRITE
**Key params:**
- `providerId` (path, string, required)

**Request body:**
- `data` (array, required, 0–10) — upsert entries, each: `{ entityId: string, contextId?: string, value: any }`

**Response:** 204 No Content

**PBC usage:**
```typescript
await getAxiosInstance().post(`/api/metadata/metadata/${providerId}`, {
  data: [
    {
      entityId: productId,
      contextId: 'en',
      value: { myKey: 'myValue' },
    },
  ],
});
```

---

### DELETE /api/metadata/metadata/{providerId}
**Tags:** Metadata
**Permission:** `METADATA` / WRITE
**Key params:**
- `providerId` (path, string, required)

**Request body:**
- `entityIds` (array, required, 0–100) — entity IDs whose metadata to delete for this provider

**Response:** 204 No Content

**PBC usage:**
```typescript
await getAxiosInstance().delete(`/api/metadata/metadata/${providerId}`, {
  data: { entityIds: [productId] },
});
```

---

### DELETE /api/metadata/metadata/{providerId}/by-ids
**Tags:** Metadata
**Permission:** `METADATA` / WRITE
**Key params:**
- `providerId` (path, string, required)

**Request body:**
- `ids` (array, required, 1–100) — entries to delete, each: `{ entityId: string, contextId: string }`

**Response:** 204 No Content

**PBC usage:**
```typescript
await getAxiosInstance().delete(`/api/metadata/metadata/${providerId}/by-ids`, {
  data: {
    ids: [{ entityId: productId, contextId: 'en' }],
  },
});
```

---

### POST /api/metadata/metadata/{providerId}/cursor/views/all
**Tags:** Metadata
**Permission:** `METADATA` / READ
**Key params:**
- `providerId` (path, string, required)

**Request body:**
- `count` (integer, required, 1–100) — page size
- `cursor` (string, optional) — cursor ID from previous response for pagination
- `views` (array, optional) — `[{ type: "METADATA" }]`

**Response:** `MetadataWithCursorAndViewsByProviderIdResponse` — `data[]` each with `entityId`, `contextId`, `metadata`; `nextCursor` (string, optional — absent when last page)

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().post(
  `/api/metadata/metadata/${providerId}/cursor/views/all`,
  {
    count: 100,
    cursor: previousNextCursor, // omit on first page
    views: [{ type: 'METADATA' }],
  }
);
// data.nextCursor is undefined when there are no more pages
```

---

### GET /api/metadata/async/task/{taskId}
**Tags:** Task
**Permission:** none specified (authenticated)
**Key params:**
- `taskId` (path, string, required) — async task ID returned by a long-running operation

**Response:** `TaskStatusResponse` — `{ status: 'WAITING'|'EXECUTING'|'ERROR'|'COMPLETED'|'TO_CANCEL'|'CANCELLED'|'EXPIRED', createdAt, updatedAt, createdBy? }`

**PBC usage:**
```typescript
const { data } = await getAxiosInstance().get(
  `/api/metadata/async/task/${taskId}`
);
// Poll until data.status === 'COMPLETED' or terminal error state
```

---

## External Notifications

**Base path:** `/api/notification-external/...` on `api.test.bluestonepim.com`

**Available webhook event types:**

| Category | Event Types |
|---|---|
| Product changes | `PRODUCT_WATCH_ASSET`, `PRODUCT_WATCH_ATTRIBUTE`, `PRODUCT_WATCH_ATTRIBUTE_UPDATE_VALUE`, `PRODUCT_WATCH_ATTRIBUTE_ASSOCIATION`, `PRODUCT_WATCH_ATTRIBUTE_DISASSOCIATION`, `PRODUCT_WATCH_BUNDLE`, `PRODUCT_WATCH_BUNDLE_QUANTITY`, `PRODUCT_WATCH_CATEGORY`, `PRODUCT_WATCH_LABEL`, `PRODUCT_WATCH_METADATA_NAME`, `PRODUCT_WATCH_METADATA_NUMBER`, `PRODUCT_WATCH_METADATA_DESCRIPTION`, `PRODUCT_WATCH_RELATION`, `PRODUCT_WATCH_STATE`, `PRODUCT_WATCH_VARIANT`, `PRODUCT_CREATED`, `PRODUCT_SYNC_DONE` |
| Category changes | `CATEGORY_WATCH_METADATA_NAME`, `CATEGORY_WATCH_METADATA_NUMBER`, `CATEGORY_WATCH_METADATA_DESCRIPTION`, `CATEGORY_WATCH_ARCHIVE_STATE`, `CATEGORY_WATCH_MOVE`, `CATEGORY_WATCH_ORDER`, `CATEGORY_WATCH_ATTRIBUTE`, `CATEGORY_LOCAL_WATCH_ATTRIBUTE`, `CATEGORY_WATCH_ASSET`, `CATEGORY_CREATED`, `CATEGORY_REMOVED` |
| Asset changes | `ASSET_CREATED`, `ASSET_WATCH_METADATA_NAME`, `ASSET_WATCH_METADATA_NUMBER`, `ASSET_WATCH_METADATA_DESCRIPTION`, `ASSET_WATCH_ATTRIBUTE_UPDATE_VALUE`, `ASSET_WATCH_ATTRIBUTE_ASSOCIATION`, `ASSET_WATCH_ATTRIBUTE_DISASSOCIATION` |
| User/task (email/in-app only) | `TASK_IS_ASSIGNED_TO_USER`, `TASK_METADATA_IS_UPDATED`, `NEW_COMMENT_ASSIGNED_TO_TASK`, `TASK_DUE_DATE_UPDATED`, `TASK_SCOPE_IS_UPDATED`, `TASK_REPORTED_BY_ME_IS_CLOSED`, `PRODUCT_WATCH`, `TASK_DEADLINE_REACHED`, `TASK_DEADLINE_REMINDER` |

---

### POST /notification-external/webhooks
**Tags:** Webhooks
**Permission:** `ADMIN`, `NOTIFICATIONS` module
**Key params:** none
**Request body:**
- `url` (string, required) — HTTPS endpoint Bluestone will POST events to
- `secret` (string, required) — shared secret for HMAC signature verification
- `active` (boolean, required) — enable/disable delivery immediately
**Response:** `201 Created` — `Location` / `Resource-Id` headers contain the new webhook ID
```typescript
const { headers } = await getAxiosInstance().post(
  '/api/notification-external/webhooks',
  { url: 'https://my-service.example.com/hooks/bluestone', secret: 'my-secret', active: true }
);
const webhookId = headers['resource-id'];
```

---

### POST /notification-external/webhooks/list
**Tags:** Webhooks
**Permission:** `ADMIN`, `NOTIFICATIONS` module
**Key params:** none
**Request body:**
- `page` (integer ≥ 0, required)
- `pageSize` (integer 1–100, required)
**Response:** `WebhookListResponse` — `data` array of `{ id, url, secret, active, retryAttempts }`
```typescript
const { data } = await getAxiosInstance().post(
  '/api/notification-external/webhooks/list',
  { page: 0, pageSize: 20 }
);
// data.data → WebhookResponse[]
```

---

### GET /notification-external/webhooks/{webhookId}
**Tags:** Webhooks
**Permission:** `ADMIN`, `NOTIFICATIONS` module
**Key params:**
- `webhookId` (path, string, required) — webhook identifier
**Response:** `WebhookResponse` — `{ id, url, secret, active, retryAttempts }`
```typescript
const { data } = await getAxiosInstance().get(
  `/api/notification-external/webhooks/${webhookId}`
);
```

---

### PUT /notification-external/webhooks/{webhookId}
**Tags:** Webhooks
**Permission:** `ADMIN`, `NOTIFICATIONS` module
**Key params:**
- `webhookId` (path, string, required)
**Request body:** (all optional)
- `url` (string)
- `secret` (string)
- `active` (boolean)
**Response:** `200 OK`
```typescript
await getAxiosInstance().put(
  `/api/notification-external/webhooks/${webhookId}`,
  { active: false }
);
```

---

### DELETE /notification-external/webhooks/{webhookId}
**Tags:** Webhooks
**Permission:** `ADMIN`, `NOTIFICATIONS` module
**Key params:**
- `webhookId` (path, string, required)
**Response:** `200 OK`
```typescript
await getAxiosInstance().delete(
  `/api/notification-external/webhooks/${webhookId}`
);
```

---

### GET /notification-external/subscriptions/webhook/{webhookId}/events
**Tags:** Subscriptions webhook
**Permission:** `ADMIN`, `NOTIFICATIONS` module
**Key params:**
- `webhookId` (path, string, required)
**Response:** `SubscriptionResponse` — `{ eventTypes: string[] }`
```typescript
const { data } = await getAxiosInstance().get(
  `/api/notification-external/subscriptions/webhook/${webhookId}/events`
);
// data.eventTypes → currently subscribed event type strings
```

---

### PUT /notification-external/subscriptions/webhook/{webhookId}/events
**Tags:** Subscriptions webhook
**Permission:** `ADMIN`, `NOTIFICATIONS` module
**Key params:**
- `webhookId` (path, string, required)
**Request body:**
- `eventTypes` (array of strings, minItems: 1, required) — see event type table above
**Response:** `200 OK`
```typescript
await getAxiosInstance().put(
  `/api/notification-external/subscriptions/webhook/${webhookId}/events`,
  { eventTypes: ['PRODUCT_CREATED', 'PRODUCT_WATCH_ATTRIBUTE_UPDATE_VALUE', 'PRODUCT_SYNC_DONE'] }
);
```

---

### DELETE /notification-external/subscriptions/webhook/{webhookId}/events
**Tags:** Subscriptions webhook
**Permission:** `ADMIN`, `NOTIFICATIONS` module
**Key params:**
- `webhookId` (path, string, required)
**Request body:**
- `eventTypes` (array of strings, minItems: 1, required)
**Response:** `204 No Content`
```typescript
await getAxiosInstance().delete(
  `/api/notification-external/subscriptions/webhook/${webhookId}/events`,
  { data: { eventTypes: ['PRODUCT_CREATED'] } }
);
```

---

### GET /notification-external/subscriptions/webhook/{webhookId}/apps
**Tags:** Subscriptions apps queue webhook
**Permission:** `ADMIN`, `NOTIFICATIONS` module
**Key params:**
- `webhookId` (path, string, required)
**Response:** `WebhookAppsQueueResponse` — `{ enabled: boolean }`
```typescript
const { data } = await getAxiosInstance().get(
  `/api/notification-external/subscriptions/webhook/${webhookId}/apps`
);
// data.enabled → whether apps queue is active for this webhook
```

---

### PUT /notification-external/subscriptions/webhook/{webhookId}/apps
**Tags:** Subscriptions apps queue webhook
**Permission:** `ADMIN`, `NOTIFICATIONS` module
**Key params:**
- `webhookId` (path, string, required)
**Request body:**
- `enabled` (boolean, required)
**Response:** `204 No Content`
```typescript
await getAxiosInstance().put(
  `/api/notification-external/subscriptions/webhook/${webhookId}/apps`,
  { enabled: true }
);
```

---

### POST /notification-external/messages/webhook
**Tags:** Webhook messages
**Permission:** `ADMIN`, `NOTIFICATIONS` module
**Key params:**
- `url` (query, string, required) — target URL to test
- `secret` (query, string, required) — secret to use for the test call
**Response:** `WebhookCall` — `{ request, response, timestamp }`
```typescript
const { data } = await getAxiosInstance().post(
  '/api/notification-external/messages/webhook',
  null,
  { params: { url: 'https://my-service.example.com/hooks/bluestone', secret: 'my-secret' } }
);
// data.response → what the target endpoint returned
```

---

### POST /notification-external/messages/webhook/list
**Tags:** Webhook messages
**Permission:** `ADMIN`, `NOTIFICATIONS` module
**Key params:** none
**Request body:**
- `webhookId` (string, required)
- `page` (integer ≥ 0, required)
- `pageSize` (integer 1–100, required)
- `status` (enum: `TO_BE_SENT` | `SENT` | `ERROR` | `WEBHOOK_INACTIVE` | `IN_PROGRESS`, optional)
**Response:** array of `WebhookMessageResponse` — `{ id, content, createdAt, eventType, status, webhookId }`
```typescript
const { data } = await getAxiosInstance().post(
  '/api/notification-external/messages/webhook/list',
  { webhookId, page: 0, pageSize: 50, status: 'ERROR' }
);
// data → WebhookMessageResponse[] — useful for debugging failed deliveries
```

---

### POST /notification-external/messages/in-app/list
**Tags:** Messages in-app
**Permission:** `ADMIN`, `NOTIFICATIONS` module
**Key params:** none
**Request body:**
- `fromCreationDate` (integer int64, optional)
- `toCreationDate` (integer int64, optional)
- `ids` (array of strings, optional)
- `page` (integer ≥ 0)
- `pageSize` (integer 1–100)
- `status` (enum: `READ` | `NOT_READ`, optional)
**Response:** `InAppMessageListResponse` — `data` array
```typescript
const { data } = await getAxiosInstance().post(
  '/api/notification-external/messages/in-app/list',
  { page: 0, pageSize: 25, status: 'NOT_READ' }
);
```

---

### POST /notification-external/messages/user/in-app/list
**Tags:** Messages in-app
**Permission:** `READ`, `NOTIFICATIONS` module
**Key params:** none
**Request body:**
- `fromCreationDate` (integer int64, optional)
- `toCreationDate` (integer int64, optional)
- `ids` (array of strings, optional)
- `page` (integer ≥ 0)
- `pageSize` (integer 1–100)
- `status` (enum: `READ` | `NOT_READ`, optional)
**Response:** `InAppUserMessageListResponse` — `data` array of `{ id, content, contentType, createDate, status }`
```typescript
const { data } = await getAxiosInstance().post(
  '/api/notification-external/messages/user/in-app/list',
  { page: 0, pageSize: 25 }
);
```

---

### PUT /notification-external/messages/user/in-app/{messageId}/read
**Tags:** Messages in-app
**Permission:** `WRITE`, `NOTIFICATIONS` module
**Key params:**
- `messageId` (path, string, required)
**Response:** `200 OK`
```typescript
await getAxiosInstance().put(
  `/api/notification-external/messages/user/in-app/${messageId}/read`
);
```

---

### PUT /notification-external/messages/user/in-app/{messageId}/unread
**Tags:** Messages in-app
**Permission:** `WRITE`, `NOTIFICATIONS` module
**Key params:**
- `messageId` (path, string, required)
**Response:** `200 OK`
```typescript
await getAxiosInstance().put(
  `/api/notification-external/messages/user/in-app/${messageId}/unread`
);
```

---

### PUT /notification-external/messages/user/in-app/readall
**Tags:** Messages in-app
**Permission:** `WRITE`, `NOTIFICATIONS` module
**Key params:** none
**Response:** `200 OK`
```typescript
await getAxiosInstance().put(
  '/api/notification-external/messages/user/in-app/readall'
);
```

---

### POST /notification-external/messages/email/list
**Tags:** Email messages
**Permission:** `ADMIN`, `NOTIFICATIONS` module
**Key params:** none
**Request body:**
- `fromCreationDate` (integer int64, optional)
- `toCreationDate` (integer int64, optional)
- `ids` (array of strings, optional)
- `page` (integer ≥ 0)
- `pageSize` (integer 1–100)
**Response:** `EmailMessageListResponse` — `data` array
```typescript
const { data } = await getAxiosInstance().post(
  '/api/notification-external/messages/email/list',
  { page: 0, pageSize: 25 }
);
```

---

### GET /notification-external/messages/email/{messageId}
**Tags:** Email messages
**Permission:** `ADMIN`, `NOTIFICATIONS` module
**Key params:**
- `messageId` (path, string, required)
**Response:** `EmailMessageResponse` — `{ id, subject, content, contentType, createDate, deliveryStatus, recipient[] }`
```typescript
const { data } = await getAxiosInstance().get(
  `/api/notification-external/messages/email/${messageId}`
);
```

---

### GET /notification-external/subscriptions/user/email/events
**Tags:** Subscriptions email
**Permission:** `READ`, `NOTIFICATIONS` module
**Key params:** none
**Response:** `SubscriptionResponse` — `{ eventTypes: string[] }`
```typescript
const { data } = await getAxiosInstance().get(
  '/api/notification-external/subscriptions/user/email/events'
);
```

---

### PUT /notification-external/subscriptions/user/email/events
**Tags:** Subscriptions email
**Permission:** `WRITE`, `NOTIFICATIONS` module
**Key params:** none
**Request body:**
- `eventTypes` (array of strings, minItems: 1, required) — valid values: `TASK_IS_ASSIGNED_TO_USER`, `TASK_METADATA_IS_UPDATED`, `NEW_COMMENT_ASSIGNED_TO_TASK`, `TASK_DUE_DATE_UPDATED`, `TASK_SCOPE_IS_UPDATED`, `TASK_REPORTED_BY_ME_IS_CLOSED`, `PRODUCT_WATCH`, `TASK_DEADLINE_REACHED`, `TASK_DEADLINE_REMINDER`
**Response:** `200 OK`
```typescript
await getAxiosInstance().put(
  '/api/notification-external/subscriptions/user/email/events',
  { eventTypes: ['PRODUCT_WATCH', 'TASK_IS_ASSIGNED_TO_USER'] }
);
```

---

### DELETE /notification-external/subscriptions/user/email/events
**Tags:** Subscriptions email
**Permission:** `WRITE`, `NOTIFICATIONS` module
**Key params:** none
**Request body:**
- `eventTypes` (array of strings, minItems: 1, required)
**Response:** `204 No Content`
```typescript
await getAxiosInstance().delete(
  '/api/notification-external/subscriptions/user/email/events',
  { data: { eventTypes: ['PRODUCT_WATCH'] } }
);
```

---

### GET /notification-external/subscriptions/user/email/aggregation/entity/{entityType}
**Tags:** Subscriptions email
**Permission:** `READ`, `NOTIFICATIONS` module
**Key params:**
- `entityType` (path, enum: `TASK` | `PRODUCT` | `CATEGORY` | `ASSET`, required)
**Response:** `FrequencyConfigResponse` — `{ aggregated, aggregationFrequency, immediately }`
```typescript
const { data } = await getAxiosInstance().get(
  '/api/notification-external/subscriptions/user/email/aggregation/entity/PRODUCT'
);
```

---

### PUT /notification-external/subscriptions/user/email/aggregation/entity/{entityType}
**Tags:** Subscriptions email
**Permission:** `WRITE`, `NOTIFICATIONS` module
**Key params:**
- `entityType` (path, enum: `TASK` | `PRODUCT` | `CATEGORY` | `ASSET`, required)
**Request body:**
- `immediately` (boolean, required)
- `aggregated` (boolean, required)
- `aggregationFrequency` (object, required):
  - `frequencyType` (enum: `HOURLY` | `DAILY` | `WEEKLY`, required)
  - `hour` (integer 0–23, required)
  - `dayOfWeek` (enum: `MONDAY`–`SUNDAY`, required)
  - `timeZoneId` (string, required) — IANA tz identifier e.g. `Europe/Warsaw`
**Response:** `202 Accepted`
```typescript
await getAxiosInstance().put(
  '/api/notification-external/subscriptions/user/email/aggregation/entity/PRODUCT',
  {
    immediately: false,
    aggregated: true,
    aggregationFrequency: {
      frequencyType: 'DAILY',
      hour: 8,
      dayOfWeek: 'MONDAY',
      timeZoneId: 'Europe/Warsaw'
    }
  }
);
```

---

### GET /notification-external/subscriptions/user/in-app/events
**Tags:** Subscriptions in-app
**Permission:** `READ`, `NOTIFICATIONS` module
**Key params:** none
**Response:** `SubscriptionResponse` — `{ eventTypes: string[] }`
```typescript
const { data } = await getAxiosInstance().get(
  '/api/notification-external/subscriptions/user/in-app/events'
);
```

---

### PUT /notification-external/subscriptions/user/in-app/events
**Tags:** Subscriptions in-app
**Permission:** `WRITE`, `NOTIFICATIONS` module
**Key params:** none
**Request body:**
- `eventTypes` (array of strings, minItems: 1, required)
**Response:** `200 OK`
```typescript
await getAxiosInstance().put(
  '/api/notification-external/subscriptions/user/in-app/events',
  { eventTypes: ['PRODUCT_WATCH', 'TASK_IS_ASSIGNED_TO_USER'] }
);
```

---

### DELETE /notification-external/subscriptions/user/in-app/events
**Tags:** Subscriptions in-app
**Permission:** `WRITE`, `NOTIFICATIONS` module
**Key params:** none
**Request body:**
- `eventTypes` (array of strings, minItems: 1, required)
**Response:** `204 No Content`
```typescript
await getAxiosInstance().delete(
  '/api/notification-external/subscriptions/user/in-app/events',
  { data: { eventTypes: ['PRODUCT_WATCH'] } }
);
```

---

### GET /notification-external/subscriptions/user/watch/event/{eventType}
**Tags:** Subscriptions watch
**Permission:** `READ`, `NOTIFICATIONS` module
**Key params:**
- `eventType` (path, enum: `PRODUCT_WATCH`, required)
**Response:** `EntityWatchResponse` — `{ entityIds: string[] }`
```typescript
const { data } = await getAxiosInstance().get(
  '/api/notification-external/subscriptions/user/watch/event/PRODUCT_WATCH'
);
// data.entityIds → product IDs the current user is watching
```

---

### PUT /notification-external/subscriptions/user/watch/event/{eventType}
**Tags:** Subscriptions watch
**Permission:** `WRITE`, `NOTIFICATIONS` module
**Key params:**
- `eventType` (path, enum: `PRODUCT_WATCH`, required)
**Request body:**
- `entityIds` (array of strings, required) — product IDs to start watching
**Response:** `200 OK`
```typescript
await getAxiosInstance().put(
  '/api/notification-external/subscriptions/user/watch/event/PRODUCT_WATCH',
  { entityIds: ['prod-id-1', 'prod-id-2'] }
);
```

---

### DELETE /notification-external/subscriptions/user/watch/event/{eventType}
**Tags:** Subscriptions watch
**Permission:** `WRITE`, `NOTIFICATIONS` module
**Key params:**
- `eventType` (path, enum: `PRODUCT_WATCH`, required)
**Request body:**
- `entityIds` (array of strings, required) — product IDs to stop watching
**Response:** `204 No Content`
```typescript
await getAxiosInstance().delete(
  '/api/notification-external/subscriptions/user/watch/event/PRODUCT_WATCH',
  { data: { entityIds: ['prod-id-1'] } }
);
```

---

### GET /notification-external/subscriptions/user
**Tags:** User configuration
**Permission:** `READ`, `NOTIFICATIONS` module
**Key params:** none
**Response:** `UserConfigurationResponse` — `{ userEmail: string }`
```typescript
const { data } = await getAxiosInstance().get(
  '/api/notification-external/subscriptions/user'
);
```

---

### PATCH /notification-external/subscriptions/user
**Tags:** User configuration
**Permission:** `WRITE`, `NOTIFICATIONS` module
**Key params:** none
**Request body:**
- `userEmail` (PropertyUpdateString — nullable string) — override notification email address
**Response:** `202 Accepted`
```typescript
await getAxiosInstance().patch(
  '/api/notification-external/subscriptions/user',
  { userEmail: { value: 'developer@example.com' } }
);
```

---

## Public API Sync

**Base path:** `/api/papi-sync/...` on `api.test.bluestonepim.com`

**Sync overview:** A sync session represents a full or partial publication of products to the Public API (PAPI). The typical flow is: (1) create a session targeting one or more contexts, (2) add up to 100 products per call, (3) close the session when done. Monitor progress and issues via the report endpoints. Covers products, their related entities (attributes, relations, categories), and assets.

---

### POST /papi-sync/session
**Tags:** Session sync
**Permission:** `PRODUCTS` — `PUBLISH`
**Key params:** none
**Request body:**
- `contexts` (array of strings, required) — context IDs to publish to (e.g. `["en", "de"]`)
**Response:** `201 Created` — `Location` and `Resource-Id` headers contain the session ID
```typescript
const { headers } = await getAxiosInstance().post(
  '/api/papi-sync/session',
  { contexts: ['en'] }
);
const sessionId = headers['resource-id'];
```

---

### GET /papi-sync/session/{id}
**Tags:** Session sync
**Permission:** `PRODUCTS` — `PUBLISH`
**Key params:**
- `id` (path, string, required) — session identifier
**Response:** `SyncSessionResponse` — `{ id, contexts, status, errorReports }`
- `status` indicates current state (use to poll until terminal state)
```typescript
const { data } = await getAxiosInstance().get(
  `/api/papi-sync/session/${sessionId}`
);
// data.status → session lifecycle state
```

---

### POST /papi-sync/session/{id}/products
**Tags:** Session sync
**Permission:** `PRODUCTS` — `PUBLISH`
**Key params:**
- `id` (path, string, required)
**Request body:**
- `products` (array of `ProductSyncRequest`, max 100, required):
  - `productId` (string, required)
  - `lastUpdate` (integer int64, optional) — timestamp for conflict detection
**Response:** `204 No Content`
```typescript
await getAxiosInstance().post(
  `/api/papi-sync/session/${sessionId}/products`,
  {
    products: [
      { productId: 'prod-id-1', lastUpdate: Date.now() },
      { productId: 'prod-id-2' }
    ]
  }
);
```

---

### PUT /papi-sync/session/{id}/done
**Tags:** Session sync
**Permission:** `PRODUCTS` — `PUBLISH`
**Key params:**
- `id` (path, string, required)
**Response:** `200 OK` — triggers final processing and closes the session
```typescript
await getAxiosInstance().put(`/api/papi-sync/session/${sessionId}/done`);
```

---

### PUT /papi-sync/session/{id}/cancel
**Tags:** Session sync
**Permission:** `PRODUCTS` — `PUBLISH`
**Key params:**
- `id` (path, string, required)
**Response:** `200 OK` — aborts the session without publishing
```typescript
await getAxiosInstance().put(`/api/papi-sync/session/${sessionId}/cancel`);
```

---

### POST /papi-sync/product/state/list/by-ids
**Tags:** Products
**Permission:** `PRODUCTS` — `LIST`
**Key params:** none
**Request body:**
- `productIds` (array of strings, max 100, required)
- `contextId` (string, minLength: 1, required)
**Response:** `ProductStateSearchListResponse` — array of `{ productId, papiPublishState, context, papiOutOfSync, lastSyncState }`
```typescript
const { data } = await getAxiosInstance().post(
  '/api/papi-sync/product/state/list/by-ids',
  { productIds: ['prod-id-1', 'prod-id-2'], contextId: 'en' }
);
// data elements: papiPublishState (PUBLISHED/UNPUBLISHED), papiOutOfSync (bool)
```

---

### POST /papi-sync/product/state
**Tags:** Products
**Permission:** `PRODUCTS` — `PUBLISH`
**Key params:** none
**Request body:**
- `productIds` (array of strings, max 100, required)
- `context` (string, minLength: 1, required)
- `action` (enum: `PUBLISH` | `UNPUBLISH`, required)
**Response:** `200 OK`
```typescript
await getAxiosInstance().post('/api/papi-sync/product/state', {
  productIds: ['prod-id-1', 'prod-id-2'],
  context: 'en',
  action: 'PUBLISH'
});
```

---

### POST /papi-sync/category/state/list/by-ids
**Tags:** Categories
**Permission:** `CATALOG` — `LIST`
**Key params:** none
**Request body:**
- `categoryIds` (array of strings, max 100, required)
- `contextId` (string, minLength: 1, required)
**Response:** `CategoryStateSearchListResponse` — array of `{ categoryId, context, papiOutOfSync, papiPublishState }`
```typescript
const { data } = await getAxiosInstance().post(
  '/api/papi-sync/category/state/list/by-ids',
  { categoryIds: ['cat-id-1'], contextId: 'en' }
);
```

---

### GET /papi-sync/session/{id}/report
**Tags:** Session report
**Permission:** `PRODUCTS` — `PUBLISH`
**Key params:**
- `id` (path, string, required)
**Response:** `SessionReportResponse` — `{ totalProducts, failedProducts, successProducts, totalCategories, metrics, categoriesMetrics }`
```typescript
const { data } = await getAxiosInstance().get(
  `/api/papi-sync/session/${sessionId}/report`
);
// data.failedProducts → count of products that did not sync successfully
```

---

### POST /papi-sync/session/{id}/report/products/file
**Tags:** Session report
**Permission:** `PRODUCTS` — `PUBLISH`
**Key params:**
- `id` (path, string, required)
**Request body:**
- `format` (enum: `csv` | `xlsx`, required)
**Response:** `204 No Content` — triggers async file generation; poll the link endpoint
```typescript
await getAxiosInstance().post(
  `/api/papi-sync/session/${sessionId}/report/products/file`,
  { format: 'csv' }
);
```

---

### GET /papi-sync/session/{id}/report/products/link
**Tags:** Session report
**Permission:** `PRODUCTS` — `PUBLISH`
**Key params:**
- `id` (path, string, required)
- `format` (query, string, required) — `csv` or `xlsx`
**Response:** `SessionReportLinkResponse` — `{ url: string }`
```typescript
const { data } = await getAxiosInstance().get(
  `/api/papi-sync/session/${sessionId}/report/products/link`,
  { params: { format: 'csv' } }
);
// data.url → pre-signed download URL for the report file
```

---

### GET /papi-sync/session/{id}/report/issues/products
**Tags:** Session report
**Permission:** `PRODUCTS` — `PUBLISH`
**Key params:**
- `id` (path, string, required)
**Response:** `ProductsIssuesResponse` — array of `{ context, issueType, count }`
- Issue types include: version conflicts, missing attributes, invalid states, and 12+ others
```typescript
const { data } = await getAxiosInstance().get(
  `/api/papi-sync/session/${sessionId}/report/issues/products`
);
// data → [{context:'en', issueType:'MISSING_ATTRIBUTE', count:3}, ...]
```

---

### POST /papi-sync/session/{id}/report/issues/products/cursor
**Tags:** Session report
**Permission:** `PRODUCTS` — `PUBLISH`
**Key params:**
- `id` (path, string, required)
**Request body:**
- `issueTypes` (array of enum strings, minItems: 1, required)
- `context` (string, required)
- `cursor` (ObjectId, optional) — omit for first page
- `pageSize` (integer int32, max 500, required)
**Response:** `ProductsByIssueTypeCursorResponse` — `{ data: [], nextCursor }`
```typescript
const { data } = await getAxiosInstance().post(
  `/api/papi-sync/session/${sessionId}/report/issues/products/cursor`,
  { issueTypes: ['MISSING_ATTRIBUTE'], context: 'en', pageSize: 100 }
);
// Use data.nextCursor in subsequent calls to page through all affected products
```

---

### POST /papi-sync/session/{id}/report/entities/file
**Tags:** Session report
**Permission:** `PRODUCTS` — `PUBLISH`
**Key params:**
- `id` (path, string, required)
**Request body:**
- `format` (enum: `csv` | `xlsx`, required)
**Response:** `204 No Content`
```typescript
await getAxiosInstance().post(
  `/api/papi-sync/session/${sessionId}/report/entities/file`,
  { format: 'xlsx' }
);
```

---

### GET /papi-sync/session/{id}/report/entities/link
**Tags:** Session report
**Permission:** `PRODUCTS` — `PUBLISH`
**Key params:**
- `id` (path, string, required)
- `format` (query, string, required)
**Response:** `SessionReportLinkResponse` — `{ url: string }`
```typescript
const { data } = await getAxiosInstance().get(
  `/api/papi-sync/session/${sessionId}/report/entities/link`,
  { params: { format: 'xlsx' } }
);
```

---

### GET /papi-sync/session/{id}/report/issues/entities
**Tags:** Session report
**Permission:** `PRODUCTS` — `PUBLISH`
**Key params:**
- `id` (path, string, required)
**Response:** `EntitiesIssuesResponse` — array of `{ context, issueType, count, entityType }`
- Entity issue types: missing category names, attribute definitions, relation names (10 types)
```typescript
const { data } = await getAxiosInstance().get(
  `/api/papi-sync/session/${sessionId}/report/issues/entities`
);
```

---

### POST /papi-sync/session/{id}/report/assets/file
**Tags:** Session report
**Permission:** `PRODUCTS` — `PUBLISH`
**Key params:**
- `id` (path, string, required)
**Request body:**
- `format` (enum: `csv` | `xlsx`, required)
**Response:** `204 No Content`
```typescript
await getAxiosInstance().post(
  `/api/papi-sync/session/${sessionId}/report/assets/file`,
  { format: 'csv' }
);
```

---

### GET /papi-sync/session/{id}/report/assets/link
**Tags:** Session report
**Permission:** `PRODUCTS` — `PUBLISH`
**Key params:**
- `id` (path, string, required)
- `format` (query, string, required)
**Response:** `SessionReportLinkResponse` — `{ url: string }`
```typescript
const { data } = await getAxiosInstance().get(
  `/api/papi-sync/session/${sessionId}/report/assets/link`,
  { params: { format: 'csv' } }
);
```

---

### GET /papi-sync/session/{id}/report/issues/assets
**Tags:** Session report
**Permission:** `PRODUCTS` — `PUBLISH`
**Key params:**
- `id` (path, string, required)
**Response:** `AssetsIssuesResponse` — array of `{ context, issueType, count }`
- Asset issue types: invalid attribute values, missing categories (10 types)
```typescript
const { data } = await getAxiosInstance().get(
  `/api/papi-sync/session/${sessionId}/report/issues/assets`
);
```

---

### POST /papi-sync/session/{id}/report/issues/assets/cursor
**Tags:** Session report
**Permission:** `PRODUCTS` — `PUBLISH`
**Key params:**
- `id` (path, string, required)
**Request body:**
- `issueTypes` (array of enum strings, minItems: 1, required)
- `context` (string, required)
- `cursor` (ObjectId, optional)
- `pageSize` (integer int32, max 500, required)
**Response:** `AssetsByIssueTypeCursorResponse` — `{ data: [], nextCursor }`
```typescript
const { data } = await getAxiosInstance().post(
  `/api/papi-sync/session/${sessionId}/report/issues/assets/cursor`,
  { issueTypes: ['INVALID_ATTRIBUTE_VALUE'], context: 'en', pageSize: 100 }
);
```

---

## Page

**Base path:** `/api/v1/...` on `api.test.bluestonepim.com`

**Authentication:** `x-api-key` header (not OAuth2). Pass via Axios headers.

**Context:** All endpoints accept an optional `context` header (default: `"en"`) to control language/locale of returned data.

---

### POST /v1/products/list
**Tags:** Products
**Permission:** API key (`x-api-key`)
**Key params:**
- `sort` (query, string, optional) — e.g. `name:asc`, `number:desc`
- `itemsOnPage` (query, number, optional)
- `pageNo` (query, number, optional)
- `context` (header, string, default: `"en"`)
**Request body:** `FilteringCriteriaRequest` — all fields optional:
- `ids` (array of strings)
- `names` (array of strings)
- `numbers` (array of strings)
- `labels` (array of strings)
- `categories` (array of strings)
- `attributes` (array of strings)
- `attributeFilters` (array of `AttributeFilter`)
- `relations` (array of strings)
- `productTypes` (array of `ProductType`)
- `bundles` (array of strings)
**Response:** `PaginatedProduct` — paginated list of `Product` objects
```typescript
const { data } = await getAxiosInstance().post(
  '/api/v1/products/list',
  { categories: ['cat-id-1'], labels: ['new'] },
  { params: { itemsOnPage: 20, pageNo: 0 }, headers: { 'x-api-key': API_KEY, context: 'en' } }
);
```

---

### POST /v1/products/by-ids
**Tags:** Products
**Permission:** API key (`x-api-key`)
**Key params:**
- `context` (header, string, default: `"en"`)
**Request body:** `ProductsByIdsRequest`
- `ids` (array of strings, required)
**Response:** `ProductResults` — map or array of `Product`
```typescript
const { data } = await getAxiosInstance().post(
  '/api/v1/products/by-ids',
  { ids: ['prod-id-1', 'prod-id-2'] },
  { headers: { 'x-api-key': API_KEY, context: 'en' } }
);
```

---

### POST /v1/products/by-numbers
**Tags:** Products
**Permission:** API key (`x-api-key`)
**Key params:**
- `context` (header, string, default: `"en"`)
**Request body:** `ProductsByNumbersRequest`
- `numbers` (array of strings, required)
**Response:** `ProductResults`
```typescript
const { data } = await getAxiosInstance().post(
  '/api/v1/products/by-numbers',
  { numbers: ['SKU-001', 'SKU-002'] },
  { headers: { 'x-api-key': API_KEY } }
);
```

---

### POST /v1/products/cursor/all
**Tags:** Products
**Permission:** API key (`x-api-key`)
**Key params:**
- `context` (header, string, default: `"en"`)
**Request body:** `CursorQuery`
- `cursor` (string, optional) — omit for first page
- `limit` (number, default: 10, max: 100)
**Response:** `CursorPageProduct` — `{ data: Product[], nextCursor }`
```typescript
let cursor: string | undefined;
do {
  const { data } = await getAxiosInstance().post(
    '/api/v1/products/cursor/all',
    { cursor, limit: 100 },
    { headers: { 'x-api-key': API_KEY } }
  );
  // process data.data
  cursor = data.nextCursor;
} while (cursor);
```

---

### GET /v1/products/{id}
**Tags:** Products
**Permission:** API key (`x-api-key`)
**Key params:**
- `id` (path, string, required) — product ID
- `context` (header, string, default: `"en"`)
**Response:** `Product` (200) or `PapiError` (404)
```typescript
const { data } = await getAxiosInstance().get(
  `/api/v1/products/${productId}`,
  { headers: { 'x-api-key': API_KEY } }
);
```

---

### GET /v1/categories
**Tags:** Categories
**Permission:** API key (`x-api-key`)
**Key params:**
- `name` (query, string, optional) — filter by name
- `itemsOnPage` (query, number, optional)
- `pageNo` (query, number, optional)
- `context` (header, string, default: `"en"`)
**Response:** `PaginatedCategory` — paginated root categories
```typescript
const { data } = await getAxiosInstance().get(
  '/api/v1/categories',
  { params: { itemsOnPage: 50, pageNo: 0 }, headers: { 'x-api-key': API_KEY } }
);
```

---

### GET /v1/categories/{categoryId}
**Tags:** Categories
**Permission:** API key (`x-api-key`)
**Key params:**
- `categoryId` (path, string, required)
- `deep` (query, number, default: 10) — depth of child tree to include
- `context` (header, string, default: `"en"`)
**Response:** `Category` (200) or `PapiError` (404)
```typescript
const { data } = await getAxiosInstance().get(
  `/api/v1/categories/${categoryId}`,
  { params: { deep: 2 }, headers: { 'x-api-key': API_KEY } }
);
```

---

### GET /v1/categories/{categoryId}/ancestors
**Tags:** Categories
**Permission:** API key (`x-api-key`)
**Key params:**
- `categoryId` (path, string, required)
- `itemsOnPage` (query, number, optional)
- `pageNo` (query, number, optional)
- `context` (header, string, default: `"en"`)
**Response:** array of `SimplifiedCategory` (200) or `PapiError` (404)
```typescript
const { data } = await getAxiosInstance().get(
  `/api/v1/categories/${categoryId}/ancestors`,
  { headers: { 'x-api-key': API_KEY } }
);
```

---

### GET /v1/categories/{categoryId}/products
**Tags:** Categories, Products
**Permission:** API key (`x-api-key`)
**Key params:**
- `categoryId` (path, string, required)
- `subCategories` (query, boolean, optional) — include products from subcategories
- `labels` (query, string, optional)
- `name` (query, string, optional)
- `number` (query, string, optional)
- `itemsOnPage` (query, number, optional)
- `pageNo` (query, number, optional)
- `context` (header, string, default: `"en"`)
**Response:** `PaginatedProduct`
```typescript
const { data } = await getAxiosInstance().get(
  `/api/v1/categories/${categoryId}/products`,
  { params: { subCategories: true, itemsOnPage: 20 }, headers: { 'x-api-key': API_KEY } }
);
```

---

### GET /v1/categories/scan
**Tags:** Categories
**Permission:** API key (`x-api-key`)
**Key params:**
- `categoryId` (query, string, optional) — root to scan from; omit for top level
- `itemsOnPage` (query, number, optional)
- `pageNo` (query, number, optional)
- `context` (header, string, default: `"en"`)
**Response:** `PaginatedSimplifiedCategory` (200) or `PapiError` (404)
```typescript
const { data } = await getAxiosInstance().get(
  '/api/v1/categories/scan',
  { params: { categoryId: 'root-cat-id', itemsOnPage: 100 }, headers: { 'x-api-key': API_KEY } }
);
```

---

### POST /v1/categories/by-numbers
**Tags:** Categories
**Permission:** API key (`x-api-key`)
**Key params:**
- `context` (header, string, default: `"en"`)
**Request body:** `CategoriesByNumbersRequest`
- `numbers` (array of strings, required)
**Response:** array of `Category`
```typescript
const { data } = await getAxiosInstance().post(
  '/api/v1/categories/by-numbers',
  { numbers: ['CAT-001', 'CAT-002'] },
  { headers: { 'x-api-key': API_KEY } }
);
```

---

### GET /v1/attributes
**Tags:** Attributes
**Permission:** API key (`x-api-key`)
**Key params:**
- `id` (query, string, optional) — filter by attribute definition ID
- `itemsOnPage` (query, number, optional)
- `pageNo` (query, number, optional)
- `context` (header, string, default: `"en"`)
**Response:** `PaginatedAttribute`
```typescript
const { data } = await getAxiosInstance().get(
  '/api/v1/attributes',
  { params: { itemsOnPage: 50 }, headers: { 'x-api-key': API_KEY } }
);
```

---

### POST /v1/attributes/by-numbers
**Tags:** Attributes
**Permission:** API key (`x-api-key`)
**Key params:**
- `context` (header, string, default: `"en"`)
**Request body:** `AttributesByNumbersRequest`
- `numbers` (array of strings, required)
**Response:** `AttributeResults`
```typescript
const { data } = await getAxiosInstance().post(
  '/api/v1/attributes/by-numbers',
  { numbers: ['ATTR-001', 'ATTR-002'] },
  { headers: { 'x-api-key': API_KEY } }
);
```

---

### POST /v1/attributes/dictionary/{attributeDefinitionId}/values/cursor/all
**Tags:** Attributes
**Permission:** API key (`x-api-key`)
**Key params:**
- `attributeDefinitionId` (path, string, required)
- `context` (header, string, default: `"en"`)
**Request body:** `CursorQuery`
- `cursor` (string, optional)
- `limit` (number, default: 10, max: 100)
**Response:** `DictionaryAttributeCursorResponse` — `{ data: [], nextCursor }`
```typescript
let cursor: string | undefined;
do {
  const { data } = await getAxiosInstance().post(
    `/api/v1/attributes/dictionary/${attributeDefinitionId}/values/cursor/all`,
    { cursor, limit: 100 },
    { headers: { 'x-api-key': API_KEY } }
  );
  // process data.data (dictionary values)
  cursor = data.nextCursor;
} while (cursor);
```

---

### GET /v1/relations
**Tags:** Relations
**Permission:** API key (`x-api-key`)
**Key params:**
- `context` (header, string, default: `"en"`)
**Response:** array of `RelationDefinitionDto` — describes relation types between products (name, direction: one-way/two-way)
```typescript
const { data } = await getAxiosInstance().get(
  '/api/v1/relations',
  { headers: { 'x-api-key': API_KEY } }
);
```

---

### GET /v1/syncs
**Tags:** Syncs
**Permission:** API key (`x-api-key`)
**Key params:**
- `context` (query, string, optional)
- `sort` (query, string, optional)
- `publishStates` (query, string, optional)
- `createdAfter` (query, number, optional) — epoch ms
- `itemsOnPage` (query, number, default: 20)
- `pageNo` (query, number, optional)
**Response:** array of `PublishInfo`
```typescript
const { data } = await getAxiosInstance().get(
  '/api/v1/syncs',
  { params: { context: 'en', itemsOnPage: 10 }, headers: { 'x-api-key': API_KEY } }
);
```

---

### GET /v1/syncs/{id}
**Tags:** Syncs
**Permission:** API key (`x-api-key`)
**Key params:**
- `id` (path, string, required) — sync/publish identifier
**Response:** `PublishInfo` (201) or error (404)
```typescript
const { data } = await getAxiosInstance().get(
  `/api/v1/syncs/${syncId}`,
  { headers: { 'x-api-key': API_KEY } }
);
```

---

### GET /v1/differences/products
**Tags:** Differences
**Permission:** API key (`x-api-key`)
**Key params:**
- `diffType` (query, string, optional) — `ADD` | `DELETE` | `CHANGE`
- `itemsOnPage` (query, number, optional)
- `pageNo` (query, number, optional)
- `context` (header, string, default: `"en"`)
**Response:** array of `DiffResponse` — products changed since last sync
```typescript
const { data } = await getAxiosInstance().get(
  '/api/v1/differences/products',
  { params: { diffType: 'CHANGE', itemsOnPage: 50 }, headers: { 'x-api-key': API_KEY } }
);
```

---

### GET /v1/differences/sync/{id}/products
**Tags:** Differences
**Permission:** API key (`x-api-key`)
**Key params:**
- `id` (path, string, required) — sync ID to diff against
- `diffType` (query, string, optional)
- `itemsOnPage` (query, number, optional)
- `pageNo` (query, number, optional)
**Response:** array of `DiffResponse` (200) or error (404)
```typescript
const { data } = await getAxiosInstance().get(
  `/api/v1/differences/sync/${syncId}/products`,
  { params: { diffType: 'ADD' }, headers: { 'x-api-key': API_KEY } }
);
```

---

### GET /v1/differences/categories
**Tags:** Differences
**Permission:** API key (`x-api-key`)
**Key params:**
- `diffType` (query, string, optional)
- `itemsOnPage` (query, number, optional)
- `pageNo` (query, number, optional)
- `context` (header, string, default: `"en"`)
**Response:** array of `DiffResponse`
```typescript
const { data } = await getAxiosInstance().get(
  '/api/v1/differences/categories',
  { params: { diffType: 'DELETE' }, headers: { 'x-api-key': API_KEY } }
);
```

---

### GET /v1/differences/sync/{id}/categories
**Tags:** Differences
**Permission:** API key (`x-api-key`)
**Key params:**
- `id` (path, string, required)
- `diffType` (query, string, optional)
- `itemsOnPage` (query, number, optional)
- `pageNo` (query, number, optional)
**Response:** array of `DiffResponse` (200) or error (404)
```typescript
const { data } = await getAxiosInstance().get(
  `/api/v1/differences/sync/${syncId}/categories`,
  { headers: { 'x-api-key': API_KEY } }
);
```

---

### GET /v1/differences/attributes
**Tags:** Differences
**Permission:** API key (`x-api-key`)
**Key params:**
- `diffType` (query, string, optional)
- `itemsOnPage` (query, number, optional)
- `pageNo` (query, number, optional)
- `context` (header, string, default: `"en"`)
**Response:** array of `DiffResponse`
```typescript
const { data } = await getAxiosInstance().get(
  '/api/v1/differences/attributes',
  { params: { diffType: 'CHANGE' }, headers: { 'x-api-key': API_KEY } }
);
```

---

### GET /v1/differences/sync/{id}/attributes
**Tags:** Differences
**Permission:** API key (`x-api-key`)
**Key params:**
- `id` (path, string, required)
- `diffType` (query, string, optional)
- `itemsOnPage` (query, number, optional)
- `pageNo` (query, number, optional)
**Response:** array of `DiffResponse` (200) or error (404)
```typescript
const { data } = await getAxiosInstance().get(
  `/api/v1/differences/sync/${syncId}/attributes`,
  { headers: { 'x-api-key': API_KEY } }
);
```

---

### GET /v1/differences/relations
**Tags:** Differences
**Permission:** API key (`x-api-key`)
**Key params:**
- `diffType` (query, string, optional)
- `itemsOnPage` (query, number, optional)
- `pageNo` (query, number, optional)
- `context` (header, string, default: `"en"`)
**Response:** array of `DiffResponse`
```typescript
const { data } = await getAxiosInstance().get(
  '/api/v1/differences/relations',
  { params: { diffType: 'ADD' }, headers: { 'x-api-key': API_KEY } }
);
```

---

### GET /v1/differences/sync/{id}/relations
**Tags:** Differences
**Permission:** API key (`x-api-key`)
**Key params:**
- `id` (path, string, required)
- `diffType` (query, string, optional)
- `itemsOnPage` (query, number, optional)
- `pageNo` (query, number, optional)
**Response:** array of `DiffResponse` (200) or error (404)
```typescript
const { data } = await getAxiosInstance().get(
  `/api/v1/differences/sync/${syncId}/relations`,
  { headers: { 'x-api-key': API_KEY } }
);
```

---

### GET /v1/contexts
**Tags:** Contexts
**Permission:** API key (`x-api-key`)
**Key params:** none
**Response:** array of `SegmentContextRequest` — all available publication contexts with language/segment info
```typescript
const { data } = await getAxiosInstance().get(
  '/api/v1/contexts',
  { headers: { 'x-api-key': API_KEY } }
);
// data → [{id, name, language, ...}] — use these IDs in context headers and sync sessions
```

---

## Global Settings

**Base URL:** `https://api.test.bluestonepim.com/global-settings`

This service manages two resource domains: **Contexts** (locale/language contexts for PIM data) and **Filters** (saved search/filter configurations per user or shared publicly).

---

### Contexts

#### `GET /context`
- **Description:** List all context definitions.
- **Query params:** `contextState` (enum: `ACTIVE` | `ARCHIVE`, default `ACTIVE`)
- **Request body:** None
- **Response `200`:**
  ```json
  {
    "data": [ContextResponseDto]
  }
  ```
  `ContextResponseDto` fields: `id` (string), `name` (string), `locale` (string), `fallback` (string — fallback context ID), `initial` (boolean), `internalId` (string), `archived` (boolean)
- **Permission:** `SETTINGS / READ`

---

#### `POST /context`
- **Description:** Create a new context definition.
- **Request body (`ContextRequestDto`):**
  | Field | Type | Required | Notes |
  |---|---|---|---|
  | `name` | string | yes | Context name |
  | `locale` | string | yes | Locale string (e.g. `en-US`) |
  | `fallback` | string | no | Fallback context ID |
- **Response:** `201 Created` (empty body)
- **Permission:** `SETTINGS / WRITE`

---

#### `GET /context/{id}`
- **Description:** Get a single context definition by ID.
- **Path params:** `id` (string)
- **Response `200`:** Single `ContextResponseDto` (same shape as above)
- **Permission:** `SETTINGS / READ`

---

#### `PUT /context/{id}`
- **Description:** Update an existing context definition.
- **Path params:** `id` (string)
- **Request body:** `ContextRequestDto` (same as create — `name` + `locale` required, `fallback` optional)
- **Response:** `204 No Content`
- **Permission:** `SETTINGS / WRITE`

---

#### `DELETE /context/{id}`
- **Description:** Archive (soft-delete) a context definition.
- **Path params:** `id` (string)
- **Response:** `204 No Content`
- **Permission:** `SETTINGS / WRITE`

---

#### `POST /context/list/by-names`
- **Description:** Look up context definitions by an explicit list of names.
- **Query params:** `contextState` (enum: `ACTIVE` | `ARCHIVE`, default `ACTIVE`)
- **Request body:**
  ```json
  { "names": ["string"] }   // 1–100 items
  ```
- **Response `200`:** `{ "data": [ContextResponseDto] }`
- **Permission:** `SETTINGS / READ`

---

### Filters

Filters are saved search/query definitions associated with a service + entity (e.g. service=`catalog`, entity=`PRODUCT`). They can be private (per-user) or public (visible to all).

#### `POST /filter`
- **Description:** Create a new filter.
- **Request body (`FilterCreateRequestDto`):**
  | Field | Type | Required | Notes |
  |---|---|---|---|
  | `name` | string | yes | Max 100 chars |
  | `service` | string | yes | Service identifier |
  | `entity` | string | yes | Entity type (e.g. `PRODUCT`, `MEDIA`) |
  | `public` | boolean | yes | If `true`, visible to all users |
  | `filter` | object | yes | Free-form filter definition (key-value map) |
  | `valid` | boolean | no | Validity flag |
- **Response:** `201 Created`
- **Permission:** `SETTINGS / WRITE`

---

#### `GET /filter/{id}`
- **Description:** Get a filter by ID.
- **Path params:** `id` (string)
- **Response `200` (`FilterResponseDto`):**
  | Field | Type | Notes |
  |---|---|---|
  | `id` | string | |
  | `ownerId` | string | |
  | `name` | string | |
  | `service` | string | |
  | `entity` | string | |
  | `public` | boolean | |
  | `valid` | boolean | |
  | `filter` | object | Filter criteria map |
  | `updatedAt` | int64 | Unix ms timestamp |
  | `readonly` | boolean | Whether caller has read-only access |
- **Permission:** `SETTINGS / READ`

---

#### `PATCH /filter/{id}`
- **Description:** Update a filter owned by the calling user.
- **Path params:** `id` (string)
- **Request body (`FilterUpdateRequestDto`):** All fields optional — `name`, `service`, `entity`, `public`, `valid`, `filter`
- **Response:** `204 No Content`
- **Permission:** `SETTINGS / WRITE`

---

#### `DELETE /filter/{id}`
- **Description:** Delete a filter owned by the calling user.
- **Path params:** `id` (string)
- **Response:** `204 No Content`
- **Permission:** `SETTINGS / WRITE`

---

#### `PATCH /filter/all/{id}`
- **Description:** Update any filter (public or private, any user). Admin operation.
- **Path params:** `id` (string)
- **Request body:** Same as `FilterUpdateRequestDto`
- **Response:** `204 No Content`
- **Permission:** `SETTINGS / ADMIN`

---

#### `DELETE /filter/all/{id}`
- **Description:** Delete any user's filter. Admin operation.
- **Path params:** `id` (string)
- **Response:** `204 No Content`
- **Permission:** `SETTINGS / ADMIN`

---

#### `POST /filter/list`
- **Description:** List filters belonging to the calling user, using search criteria.
- **Request body (`FilterSearchRequest`):**
  | Field | Type | Notes |
  |---|---|---|
  | `ids` | string[] | 1–100 filter IDs |
  | `ownerId` | string | Filter by owner |
  | `name` | string | Filter by name |
  | `service` | string | Filter by service |
  | `entity` | string | Filter by entity |
  | `public` | boolean | Filter by visibility |
  | `page` | int | Default 0 |
  | `pageSize` | int | Default 10, max 100 |
- **Response `200`:** `{ "data": [FilterResponseDto] }`
- **Permission:** `SETTINGS / READ`

---

#### `POST /filter/list/all`
- **Description:** List all filters (public and private, across users). Admin operation.
- **Request body:** Same `FilterSearchRequest`
- **Response `200`:** `{ "data": [FilterResponseDto] }`
- **Permission:** `SETTINGS / ADMIN`

---

#### `GET /filter/{id}/user`
- **Description:** List all users who have been granted permission on a specific filter.
- **Path params:** `id` (string)
- **Response `200`:**
  ```json
  {
    "data": [{ "userId": "string", "permissionType": "READ" | "WRITE" }]
  }
  ```
- **Permission:** `SETTINGS / READ`

---

#### `PUT /filter/{id}/user/{userId}`
- **Description:** Grant or update a user's permission on a filter.
- **Path params:** `id` (string), `userId` (string)
- **Request body:**
  ```json
  { "permissionType": "READ" | "WRITE" }
  ```
- **Response:** `204 No Content`
- **Permission:** `SETTINGS / WRITE`

---

#### `DELETE /filter/{id}/user/{userId}`
- **Description:** Revoke a user's permission on a filter.
- **Path params:** `id` (string), `userId` (string)
- **Response:** `204 No Content`
- **Permission:** `SETTINGS / WRITE`

---

### Common Error Shape (Global Settings)

```json
{
  "status": 400,
  "error": "Bad Request",
  "errorDetails": [{ "message": "...", "field": "...", "rejectedValue": "..." }],
  "requestId": "uuid",
  "timestamp": 1711123456789,
  "conflictingEntities": [{ "entityId": "..." }],
  "quotaLimitExceededDetails": [{ "message": "...", "quota": "...", "limit": 100 }]
}
```

---

## UI Settings

**Base URL:** `https://api.test.bluestonepim.com/ui-settings`

This service stores per-user or shared **column layout configurations** for UI table/grid views. Configurations are scoped by `service` and `entity` (similar to the Filters service).

---

### Column Setups

#### `GET /columnsSetups/all`
- **Description:** Retrieve all existing column setup configurations accessible to the caller.
- **Request body:** None
- **Response `200`:** Array of `ColumnsSetup` objects
- **Permission:** Not annotated (implicit via OAuth token)

---

#### `POST /columnsSetups/all`
- **Description:** Create a new column setup configuration.
- **Request body (`ColumnsSetup`):**
  | Field | Type | Required | Notes |
  |---|---|---|---|
  | `name` | string | yes | Display name for the setup |
  | `isDefault` | boolean | no | Whether this is the default layout |
  | `isPublic` | boolean | yes | Shared with all users if `true` |
  | `service` | string | yes | Owning service identifier |
  | `entity` | string | yes | Entity type (e.g. `PRODUCT`) |
  | `columns` | ColumnsSet[] | yes | Ordered list of column definitions |
  | `sorting` | object | no | Optional sort config: `{ columnId, direction: "ASC" | "DESC" }` |
- **`ColumnsSet` item shape:**
  | Field | Type | Notes |
  |---|---|---|
  | `id` | string | Column identifier |
  | `columnType` | string | Type classifier (e.g. `NAME`, `STATUS`, `CREATED`, `UPDATED`, `NUMBER_OF_ASSETS`, `ASSET_LABELS`, or attribute ID) |
  | `width` | number | Optional pixel width |
- **Response:** `201 Created`
- **Permission:** Not annotated

---

#### `GET /columnsSetups/all/{id}`
- **Description:** Retrieve a specific column setup by ID.
- **Path params:** `id` (string)
- **Response `200`:** Single `ColumnsSetup` object
- **Permission:** Not annotated

---

#### `PUT /columnsSetups/all/{id}`
- **Description:** Replace/update an existing column setup.
- **Path params:** `id` (string)
- **Request body:** `ColumnsSetup` (same shape as create)
- **Response:** `204 No Content`
- **Permission:** Not annotated

---

#### `DELETE /columnsSetups/all/{id}`
- **Description:** Delete a column setup by ID.
- **Path params:** `id` (string)
- **Response:** `204 No Content`
- **Permission:** Not annotated

---

**Note on Filter Types in UI Settings:** The spec internally references a `FilterResult` discriminated union supporting two modes — a generic item filter (`name`, `status`, `created`, `updated`, etc.) and an attribute-specific filter keyed on an attribute identifier. This is used to control which column types appear in the setup.

---

## IDP (Identity Provider)

**Base URL:** `https://api.test.bluestonepim.com/idp`

**Token endpoint:** `https://idp.bluestonepim.com/op/token` (OAuth 2.0 Client Credentials)

The IDP service is the core authentication, authorization, and identity management API. It covers: users, roles, managed roles, permissions, policies, user groups, API clients, organizations, SSO providers, consents/agreements, plugins, and MFA management.

> There are **no raw OIDC/token endpoints** (e.g. `/authorize`, `/token`, `/.well-known/openid-configuration`) exposed in this spec — those live on the IdP server directly (Keycloak-based). This spec covers the **management plane** on top of the IdP.

---

### Authentication & Token Validation Context

The IDP spec does not expose raw OIDC endpoints (discovery, JWKS, introspection) directly — those are served by the underlying OIDC provider at `https://idp.bluestonepim.com/op/`. However, the following endpoints are relevant for auth integration:

| What you need | Where |
|---|---|
| Obtain a token | `POST https://idp.bluestonepim.com/op/token` (client credentials grant) |
| Check caller's permissions | `GET /me/ui-permissions` (returns simplified permission list from token) |
| Reset own MFA | `PUT /me/resetMfa` |
| Check consent status | `GET /consent` |
| Accept agreement | `POST /consent/{agreementId}` |
| Get latest ToS/agreement | `GET /latestAgreement` |

---

### Me (Current User)

#### `GET /me/ui-permissions`
- **Description:** Returns simplified list of UI permissions derived from the bearer token.
- **Response `200` (`MinePermissions`):**
  ```json
  {
    "uiPermissions": ["string"],          // e.g. ["READ", "WRITE"]
    "pluginsUIPermissions": {             // map: pluginId -> [actionName]
      "pluginId": ["actionName"]
    }
  }
  ```
- **Permission:** None required (reads from token)

#### `PUT /me/resetMfa`
- **Description:** Reset MFA secret for the currently authenticated user.
- **Response:** `204 No Content`
- **Permission:** None (self-service)

---

### Consents & Agreements

#### `GET /latestAgreement`
- **Description:** Return the latest Terms of Service / agreement document.
- **Response `200`:**
  ```json
  { "id": "string", "url": "string", "createdAt": "string" }
  ```

#### `GET /consent`
- **Description:** Check whether the current user has accepted the latest agreement.
- **Response `200`:**
  ```json
  { "accepted": true }
  ```

#### `POST /consent/{agreementId}`
- **Description:** Accept a specific agreement by ID.
- **Path params:** `agreementId` (string)
- **Response `200` (`Consent`):**
  ```json
  { "id": "string", "createdAt": "string", "agreementId": "string", "userId": "string" }
  ```

---

### Organizations

#### `GET /organizations`
- **Description:** List organizations the authenticated user belongs to.
- **Response `200`:** Array of `PresentableOrganization`:
  ```json
  {
    "id": "string", "name": "string",
    "createdAt": 1711000000000, "updatedAt": 1711000000000,
    "createdBy": "string",
    "mfa": { "enabled": true }
  }
  ```

#### `GET /organizations/{id}`
- **Description:** Get a specific organization by ID.
- **Path params:** `id` (string)
- **Response `200`:** Single `PresentableOrganization`

---

### Users

#### `GET /users`
- **Description:** List all users in the organization.
- **Query params:** `fetchInactive` (boolean, default `false`), `page` (default 0), `pageSize` (default 1000)
- **Response `200`:** Array of `User`:
  ```json
  {
    "id": "string", "email": "string", "name": "string", "picture": "string",
    "policies": ["string"], "roles": ["string"], "managedRoles": ["string"],
    "canSendEmail": true, "isInitial": false, "userGroups": ["string"]
  }
  ```
- **Permission:** `USER / LIST`

#### `POST /users`
- **Description:** Create a new user.
- **Request body:**
  ```json
  { "email": "string", "name": "string", "userType": "STANDARD" | "RESTRICTED" }
  ```
- **Response:** `204 No Content`
- **Permission:** `USER / ADD`

#### `GET /users/{id}`
- **Description:** Get a specific user by ID.
- **Response `200`:** Single `User`
- **Permission:** `USER / DETAILS`

#### `PUT /users/{id}`
- **Description:** Update a user's name/email.
- **Request body:** `{ "name": "string", "email": "string" }` (`name` required)
- **Response `200`:** Updated `User`

#### `PUT /users/{id}/picture`
- **Description:** Upload a user profile picture.
- **Request body:** `multipart/form-data` with `files` binary field

#### `PUT /users/{id}/changePassword`
- **Description:** Change a user's password.
- **Request body:** `{ "currentPassword": "...", "newPassword": "...", "confirmPassword": "..." }`
- **Response:** `204 No Content`

#### `PUT /users/{id}/resendJoinEmail`
- **Description:** Resend invitation/join email to the user.
- **Permission:** `USER / ADD`

#### `PUT /users/{id}/setUserStatus`
- **Description:** Activate or deactivate a user.
- **Request body:** `{ "userStatus": "ACTIVE" | "INACTIVE" }`
- **Permission:** `USER / DELETE`

#### `PUT /users/{id}/userType/{type}`
- **Description:** Change user type between STANDARD and RESTRICTED.
- **Path params:** `type` (enum: `STANDARD` | `RESTRICTED`)
- **Permission:** `USER / UPDATE`

#### `PUT /users/{id}/resetMfa`
- **Description:** Admin-reset MFA for a specific user.
- **Permission:** `USER / UPDATE`

#### Role/Policy/Group assignment on users:

| Endpoint | Method | Description | Permission |
|---|---|---|---|
| `/users/{id}/roles/{roleName}/grant` | PUT | Grant a role to user | `USER_ROLES / GRANT` |
| `/users/{id}/roles/{roleName}/revoke` | PUT | Revoke a role from user | `USER_ROLES / REVOKE` |
| `/users/{id}/policies/{policyId}` | PUT | Grant a policy to user | `USER_POLICIES / GRANT` |
| `/users/{id}/policies/{policyId}` | DELETE | Revoke a policy from user | `USER_POLICIES / REVOKE` |
| `/users/{id}/managedRoles/{roleId}` | PUT | Grant a managed role | `USER_ROLES / GRANT` |
| `/users/{id}/managedRoles/{roleId}` | DELETE | Revoke a managed role | `USER_ROLES / REVOKE` |
| `/users/{id}/additionalProperties/grant` | PUT | Set custom key-value properties on user | `USER / LIST` |
| `/users/{id}/additionalProperties/revoke` | PUT | Remove custom properties from user | `USER / LIST` |
| `/users/{id}/userGroups/{userGroupId}` | PUT | Assign user to a user group | `USER_GROUP / GRANT` |
| `/users/{id}/userGroups/{userGroupId}` | DELETE | Remove user from a user group | `USER_GROUP / REVOKE` |

---

### Roles

#### `GET /roles`
- **Description:** List all roles.
- **Query params:** `page` (default 0), `pageSize` (default 1000)
- **Response `200`:** Array of `Role`:
  ```json
  {
    "id": "string", "name": "string",
    "permissions": ["string"], "dependentPermissions": ["string"],
    "pluginPermissions": { "id": "string", "pluginPermissions": [{ "module": "...", "actionName": "..." }] },
    "createdAt": 1711000000000, "updatedAt": 1711000000000
  }
  ```
- **Permission:** `ROLES / LIST`

#### `POST /roles`
- **Description:** Create a new role.
- **Request body:** `{ "name": "string", "permissions": ["string"] }`
- **Permission:** `ROLES / ADD`

#### `GET /roles/{id}`
- **Description:** Get a role by ID.
- **Permission:** `ROLES / DETAILS`

#### `PUT /roles/{id}`
- **Description:** Update a role's permissions list.
- **Request body:** `{ "permissions": ["string"] }`
- **Permission:** `ROLES / UPDATE`

#### `DELETE /roles/{id}`
- **Description:** Archive (soft-delete) a role.
- **Permission:** `ROLES / DELETE`

#### `POST /roles/{id}/permissions/assign`
- **Description:** Assign additional permission IDs to a role.
- **Request body:** `{ "permissionIds": ["string"] }`
- **Permission:** `ROLES / UPDATE`

#### `POST /roles/{id}/permissions/revoke`
- **Description:** Revoke permission IDs from a role.
- **Request body:** `{ "permissionIds": ["string"] }`
- **Permission:** `ROLES / UPDATE`

#### Plugin permissions on roles:

| Endpoint | Method | Description | Permission |
|---|---|---|---|
| `/roles/{id}/plugin/{pluginId}` | GET | Get plugin permissions for role | `ROLES / DETAILS` |
| `/roles/{id}/plugin/{pluginId}` | PUT | Replace plugin permissions for role | `ROLES / UPDATE` |
| `/roles/{id}/plugin/{pluginId}/assign` | POST | Assign specific plugin permissions | `ROLES / UPDATE` |
| `/roles/{id}/plugin/{pluginId}/revoke` | POST | Revoke specific plugin permissions | `ROLES / UPDATE` |

#### `GET /getDefaultRole`
- **Description:** Get the currently configured default role for new users.
- **Response:** `{ "roleId": "string", "isStandardRole": true }`
- **Permission:** `ROLES / LIST`

#### `POST /setDefaultRole`
- **Description:** Set the default role for new users.
- **Request body:** `{ "roleId": "string" }`
- **Permission:** `ROLES / UPDATE`

#### `DELETE /clearDefaultRole`
- **Description:** Clear the default role assignment.
- **Permission:** `ROLES / UPDATE`

---

### Managed Roles

Managed roles are system-defined roles (not user-created). They carry `permissions` (standard permission IDs) and `pluginPermissions`.

#### `GET /managedRoles`
- **Description:** List all managed roles.
- **Response `200`:** Array of `ManagedRole`: `{ id, createdAt, updatedAt, permissions: [string], pluginPermissions: [PluginPermission] }`
- **Permission:** `ROLES / LIST`

#### `GET /managedRoles/{id}`
- **Description:** Get a specific managed role.
- **Permission:** `ROLES / DETAILS`

---

### Permissions

#### `GET /permissions`
- **Description:** List all available system permissions.
- **Response `200`:** Array of `Permission`:
  ```json
  { "id": "string", "module": "string", "accessType": "string", "moduleDisplayName": "string", "accessTypeDisplayName": "string" }
  ```
- **Permission:** `PERMISSIONS / LIST`

#### `GET /permissions/{id}`
- **Description:** Get a specific permission definition.
- **Permission:** `PERMISSIONS / DETAILS`

#### `GET /dependentPermissions`
- **Description:** Get dependency map — for each permission, what other permissions it requires.
- **Response `200`:** Array of `{ "permissionId": "string", "dependentPermissionIds": ["string"] }`
- **Permission:** `PERMISSIONS / LIST`

---

### Policies (Detailed/Fine-Grained Permissions)

Policies are named fine-grained permission sets used with `RESTRICTED` user types.

#### `GET /policies`
- **Description:** List all policies.
- **Response `200`:** Array of `{ "id": "string", "name": "string" }`
- **Permission:** `DETAILED_PERMISSION / READ`

#### `POST /policies`
- **Description:** Create a new policy.
- **Request body:** `{ "name": "string" }`
- **Permission:** `DETAILED_PERMISSION / WRITE`

#### `GET /policies/{id}` / `PUT /policies/{id}` / `DELETE /policies/{id}`
- Get, update, or archive a policy.
- **Permission:** `DETAILED_PERMISSION / READ` (GET) or `DETAILED_PERMISSION / WRITE` (PUT/DELETE)

---

### User Groups

#### `POST /userGroups`
- **Description:** Create a new user group.
- **Request body:** `{ "name": "string", "number": "string", "description": "string", "status": "ACTIVE" | "INACTIVE" }`
- **Response:** `201 Created`
- **Permission:** `USER_GROUP / ADD`

#### `GET /userGroups/list`
- **Description:** List user groups with pagination.
- **Query params:** `page` (default 0), `pageSize` (default 1000)
- **Response `200`:**
  ```json
  {
    "userGroups": [{
      "id", "name", "number", "description",
      "roles": ["string"], "managedRoles": ["string"], "policies": ["string"],
      "createdAt", "updatedAt", "status", "createdBy", "updatedBy"
    }]
  }
  ```
- **Permission:** `USER_GROUP / READ`

#### `GET /userGroups/count`
- **Description:** Get total count of user groups.
- **Response `200`:** `{ "count": 42 }`
- **Permission:** `USER_GROUP / READ`

#### `GET /userGroups/{id}` — Get a user group. Permission: `USER_GROUP / READ`
#### `PATCH /userGroups/{id}` — Update name/number/description/status. Permission: `USER_GROUP / UPDATE`
#### `DELETE /userGroups/{id}` — Delete a user group. Permission: `USER_GROUP / DELETE`
#### `POST /userGroups/{id}/duplicate` — Duplicate a user group. Permission: `USER_GROUP / ADD` + `READ` + `USER_ROLES/GRANT` + `USER_POLICIES/GRANT` + `USER_GROUP/GRANT`

#### Role/policy/managed-role assignment on groups:

| Endpoint | Method | Description | Permission |
|---|---|---|---|
| `/userGroups/{id}/roles/assign` | PUT | Add a role to group (`{ roleId }`) | `USER_ROLES / GRANT` |
| `/userGroups/{id}/roles/unassign` | PUT | Remove a role from group | `USER_ROLES / REVOKE` |
| `/userGroups/{id}/policies/assign` | PUT | Assign policy to group (`{ policyId }`) | `USER_POLICIES / GRANT` |
| `/userGroups/{id}/policies/unassign` | PUT | Unassign policy from group | `USER_POLICIES / REVOKE` |
| `/userGroups/{id}/managedRoles/assign` | PUT | Add managed role to group (`{ managedRoleId }`) | `USER_ROLES / GRANT` |
| `/userGroups/{id}/managedRoles/unassign` | PUT | Remove managed role from group | `USER_ROLES / REVOKE` |

---

### API Clients

#### `GET /clients`
- **Description:** List all registered API clients (OAuth machine-to-machine clients).
- **Query params:** `page` (default 0), `pageSize` (default 1000)
- **Response `200`:** Array of `APIClientV2`:
  ```json
  {
    "id": "string",             // Used as client_id in token requests
    "clientSecret": "string",   // Used as client_secret
    "name": "string",
    "roles": ["string"],
    "organizationId": "string",
    "createdBy": "string", "createdDate": 1711000000000,
    "lastModifiedBy": "string", "lastModifiedDate": 1711000000000
  }
  ```
- **Permission:** `API_CLIENTS / READ`

#### `GET /clients/name`
- **Description:** List API client IDs and names only (lighter response).
- **Query params:** `page`, `pageSize`
- **Response `200`:** Array of `{ "id": "string", "name": "string" }`
- **Permission:** None annotated

#### `POST /services/by-ids`
- **Description:** Resolve service names from a list of IDs.
- **Request body:** `{ "ids": ["string"] }`
- **Response `200`:** Array of `{ "id": "string", "name": "string" }`

---

### SSO Providers

#### `GET /sso-providers`
- **Description:** List SSO (OIDC/SAML) provider configurations for the authenticated organization. If an SSO config is shared across multiple organizations, it is returned for any matching one.
- **Response `200`:** Array of `SSOProviderResponse`:
  ```json
  {
    "clientId": "string",
    "clientSecretExpirationDate": 1711000000000   // Unix ms — informational hint only
  }
  ```
- **Permission:** `SSO / ADMIN`

#### `POST /sso-providers/{clientId}`
- **Description:** Update the client secret for an SSO provider. Affects all organizations sharing this config.
- **Path params:** `clientId` (string)
- **Request body:**
  ```json
  { "clientSecret": "string", "clientSecretExpirationDate": 1711000000000 }
  ```
- **Response:** `204 No Content`
- **Permission:** `SSO / ADMIN`

---

### Plugins

#### `GET /plugins`
- **Description:** List IDs of plugins assigned to the organization from the bearer token.
- **Response `200`:** Array of `{ "id": "string" }`

#### `GET /plugins/{id}`
- **Description:** Get permission definitions for a specific plugin in the organization.
- **Response `200` (`DetailedPlugin`):**
  ```json
  {
    "id": "string",
    "createdAt": 1711000000000, "updatedAt": 1711000000000,
    "permissionDefinitions": [{ "id": "...", "module": "...", "actionName": "...", "description": "..." }],
    "dependentPermissions": [{ "permissionId": "...", "dependentPermissionIds": ["..."] }]
  }
  ```
- **Permission:** `ROLES / DETAILS`

---

### Additional User Properties

#### `GET /additionalUserProperties`
- **Description:** Returns the list of custom property keys that can be set on users.
- **Response `200`:** `{ "additionalProperties": ["string"] }`

---

### IDP — Authentication Integration Summary

| Concern | How it works |
|---|---|
| **Obtain a token** | `POST https://idp.bluestonepim.com/op/token` with `grant_type=client_credentials`, `client_id`, `client_secret`, `scope=openid profile systemRoles permissions organization email name nickname` |
| **Validate caller permissions** | `GET https://api.test.bluestonepim.com/idp/me/ui-permissions` — returns `uiPermissions[]` and `pluginsUIPermissions{}` derived from the token |
| **OpenID Connect discovery** | Not in this spec; available at `https://idp.bluestonepim.com/op/.well-known/openid-configuration` (standard OIDC) |
| **JWKS / token introspection** | Not in this spec; standard OIDC endpoints on `https://idp.bluestonepim.com/op/` |
| **API Client credentials** | `GET /clients` returns `id` (= `client_id`) and `clientSecret` for M2M flows |
| **SSO (federated login)** | Configured via `/sso-providers`; `clientId` + `clientSecret` managed there |
| **MFA** | Self-reset via `PUT /me/resetMfa`; admin-reset via `PUT /users/{id}/resetMfa` |
| **User consent** | Check with `GET /consent`; accept with `POST /consent/{agreementId}` |

---

### IDP — x-bluestone-permission Quick Reference

| Module | Access Types seen |
|---|---|
| `API_CLIENTS` | READ |
| `PERMISSIONS` | LIST, DETAILS |
| `ROLES` | LIST, DETAILS, ADD, UPDATE, DELETE |
| `USER` | LIST, DETAILS, ADD, UPDATE, DELETE |
| `USER_ROLES` | GRANT, REVOKE |
| `USER_POLICIES` | GRANT, REVOKE |
| `USER_GROUP` | READ, ADD, UPDATE, DELETE, GRANT, REVOKE |
| `DETAILED_PERMISSION` | READ, WRITE |

---

## Confirmed Response Body Shapes

Shapes in this section were verified against live API responses via browser DevTools.
Only add entries here when the shape has been confirmed — never from assumptions.

---

### `DictionaryAttributeResponse`

**Endpoint:** `GET /api/pim/definitions/dictionary/{definitionId}/values/{valueId}`

The response is the value object directly — **no `data` wrapper**.

```typescript
interface DictionaryAttributeResponse {
    id: string;
    definitionId: string;
    number: string;
    lastUpdate: number;
    createdDate: number;
    toBeRemoved: boolean;
    value: {
        value: {
            [languageId: string]: string;  // e.g. { "en": "Shoes", "fr": "Chaussures" }
        };
    };
}
```

**Access pattern:**
```typescript
const { data } = await getAxiosInstance().get(
    `/api/pim/definitions/dictionary/${definitionId}/values/${valueId}`,
    { headers: { context: languageId, 'context-fallback': 'true' } }
);
const label = data.value.value[languageId] ?? data.value.value['en'] ?? valueId;
```

---

### `ListableAttributeDefinitionResponse` — confirmed definition shape

**Endpoint:** `GET /api/pim/definitions`
Wrapped in `{ data: [...] }`. Key fields:

```typescript
interface SelectOption {
    valueId: string;   // ID stored in product attribute values[]
    value: string;     // human-readable label
    number: string;
    metadata?: string; // e.g. hex color "#c6d8b8" for color-type selects
}

interface AttributeDefinition {
    id: string;
    name: string;
    dataType: 'text' | 'integer' | 'decimal' | 'boolean' | 'date' | 'formatted_text'
            | 'dictionary' | 'single_select' | 'multi_select' | string;
    contextAware: boolean;
    isCompound: boolean;
    unit?: string;
    restrictions?: {
        enum?: {
            type?: string;           // "color" for color-picker selects
            values: SelectOption[];  // ALL options — present for single_select / multi_select
        };
        range?: { min?: string; max?: string; step?: string };
    };
}
```

**Select/multiselect option labels are embedded in the definition — zero extra API calls required:**
```typescript
const selectLabels: Record<string, string> = {};
definitions.forEach((def) => {
    if (def.dataType === 'single_select' || def.dataType === 'multi_select') {
        def.restrictions?.enum?.values?.forEach((opt) => {
            selectLabels[opt.valueId] = opt.value;
        });
    }
});
// attr.values[] for select types: attr.values.map(id => selectLabels[id] ?? id)
```

---

### `ListableAttributeValueAll` — product attributes

**Endpoint:** `GET /api/pim/products/{id}/attributes`

Response wrapped in `{ data: [...] }`.

```typescript
interface RawAttribute {
    definitionId: string;
    // Scalar types (text, number, boolean, date, select, multiselect):
    values?: string[];
    // Dictionary type — always opaque IDs, always requires resolution:
    dictionary?: string[];
    // Neither field present = attribute exists but has no value set
}

interface AttributesResponse {
    data: RawAttribute[];
}
```

**Access pattern:**
```typescript
const { data } = await getAxiosInstance().get(
    `/api/pim/products/${productId}/attributes`,
    { headers: { context: languageId, 'context-fallback': 'true' } }
);
const attrs: RawAttribute[] = data.data ?? [];
```

**Resolving attribute values — correct pattern (dictionary vs select are different):**

> `dictionary[]` values require one API call each.
> `single_select`/`multi_select` option IDs are in `values[]` and are resolved inline from the
> definitions list — **no extra API calls**. Never route select values through the dictionary endpoint.

```typescript
// Step 1: fetch attributes + definitions in parallel
const [attrsRes, defsRes] = await Promise.all([
    getAxiosInstance().get(`/api/pim/products/${productId}/attributes`, { headers }),
    getAxiosInstance().get('/api/pim/definitions', { headers })
]);
const attrs: RawAttribute[] = attrsRes.data.data ?? [];
const defs: AttributeDefinition[] = defsRes.data.data ?? [];

// Step 2: build select label map from definitions (zero extra API calls)
const selectLabels: Record<string, string> = {};
defs.forEach((def) => {
    if (def.dataType === 'single_select' || def.dataType === 'multi_select') {
        def.restrictions?.enum?.values?.forEach((opt) => {
            selectLabels[opt.valueId] = opt.value;
        });
    }
});

// Step 3: resolve dictionary values — one call per unique {definitionId, valueId}
const dictLabels: Record<string, string> = {};
const pairs: Array<{definitionId: string; valueId: string}> = [];
const seen = new Set<string>();
attrs.forEach((attr) => {
    attr.dictionary?.forEach((valueId) => {
        const key = `${attr.definitionId}/${valueId}`;
        if (!seen.has(key)) { seen.add(key); pairs.push({ definitionId: attr.definitionId, valueId }); }
    });
});
await Promise.all(
    pairs.map(async ({ definitionId, valueId }) => {
        try {
            const { data } = await getAxiosInstance().get(
                `/api/pim/definitions/dictionary/${definitionId}/values/${valueId}`,
                { headers }
            );
            // confirmed shape: data.value.value[languageId] — no extra wrapper
            dictLabels[valueId] = data.value.value[languageId] ?? data.value.value['en'] ?? valueId;
        } catch { dictLabels[valueId] = valueId; }
    })
);

// Step 4: render
attrs.forEach((attr) => {
    let display: string;
    if (attr.dictionary?.length) {
        display = attr.dictionary.map((id) => dictLabels[id] ?? id).join(', ');
    } else if (attr.values?.length) {
        display = attr.values.map((v) => selectLabels[v] ?? v).join(', ');
    } else {
        display = '—';
    }
});
```
| `SSO` | ADMIN |