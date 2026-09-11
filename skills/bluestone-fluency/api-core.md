> **Lookup only.** Do not load this file unless [recipes.md](recipes.md) / [gotchas.md](gotchas.md) did not contain the path. PBC examples use `getAxiosInstance()`; prepend `/api/pim/` (or the service prefix in each section). Machine clients use `https://api.<env>.bluestonepim.com/pim` + the spec path. Auth: [auth.md](auth.md).

## API Path Prefixes

The public OpenAPI specs list paths without their runtime prefix (e.g. `/products/{id}`). When calling from a PBC you must prepend the service prefix. Confirmed prefixes:

| Service | Prefix | Notes |
|---|---|---|
| PIM core (products, attributes, categories, relations, variants, bundles, assets, catalog) | `/api/pim/` | Confirmed via Network tab — not visible in OpenAPI spec |
| Tasks | `/api/tasks/` | Documented in `API_REQUESTS.md` |
| Query builder | `/api/query-builder/` | Documented in `API_REQUESTS.md` |
| Search | `/api/search/` | Documented in search spec |
| Media Bank | `/api/media-bank/` | Documented in media-bank spec |
| Completeness | `/api/completeness-score/` | Confirm in Network tab |
| UI settings (column setups) | `/api/ui-settings/` | OpenAPI: `ui-settings.json`. Recipe 12. |

> **If you are unsure of a prefix:** use browser DevTools → Network tab to inspect the path the Bluestone PIM UI calls for that data. That is the authoritative source.

---

## PIM Core API

> Paths below are shown **without prefix**. Prepend `/api/pim/` in all PBC calls.
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
**Params:** `id` (path, required), `definitionId` (path, required), `forceVla` (query), `context` (header)  
**Body:** `SetDictionaryValuesRequest`  
```typescript
const {data} = await getAxiosInstance().post(`/products/{id}/attributes/dictionary/{definitionId}/values`, body);
```

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
**Response:** `DictionaryAttributeResponse` — **no `data` wrapper**, object returned directly.

#### POST /definitions/dictionary/{id}/values
*Create a new value in a dictionary attribute definition.*
**Body:** `{ value: string (required), number?: string, metadata?: string }`
**Response:** 201 — new value ID in `Resource-Id` response header (lowercase, not in body)

#### PATCH /definitions/dictionary/{id}/values/{valueId}
*Update label/metadata of an existing dictionary value.*
**Body:** `{ value?: { value: string }, number?: { value: string }, metadata?: { value: string } }` (partial — wrap each field)
**Response:** 204

#### DELETE /definitions/dictionary/{id}/values/{valueId}
*Permanently delete a value from a dictionary definition.*
**Response:** 202 Accepted (async)

#### PUT /definitions/dictionary/{id}
*Full replace of a dictionary attribute definition.*
**Response:** 204

#### PATCH /definitions/dictionary/{id}
*Partial update — `selectedValuesLimit` or `dictionaryValuesMetadataType` only.*
**Response:** 204

**Confirmed response shape:**
```typescript
{
    id: string;
    definitionId: string;
    number: string;
    lastUpdate: number;
    createdDate: number;
    toBeRemoved: boolean;
    value: {
        value: {
            [languageId: string]: string;  // e.g. { "en": "Shoes" }
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

## Confirmed Response Shapes & Patterns

> Shapes here were verified against live API responses. Do not guess field names —
> if a type is not listed here, flag it and ask the developer to verify in DevTools first.

### `DictionaryAttributeResponse`
**Endpoint:** `GET /api/pim/definitions/dictionary/{definitionId}/values/{valueId}`
No `data` wrapper. Label at `data.value.value[languageId]`.

```typescript
// response.data shape:
{
    id: string;
    definitionId: string;
    value: { value: { [languageId: string]: string } };  // e.g. { "en": "Shoes" }
    toBeRemoved: boolean;
}

const label = data.value.value[languageId] ?? data.value.value['en'] ?? valueId;
```

### `ListableAttributeDefinitionResponse` — confirmed definition shape

**Endpoint:** `GET /api/pim/definitions`
Wrapped in `{ data: [...] }`.

```typescript
interface SelectOption {
    valueId: string;   // ID stored in product attribute values[]
    value: string;     // human-readable label — use this for display
    number: string;
    metadata?: string; // optional, e.g. hex color "#c6d8b8" for color-type enums
}

interface AttributeDefinition {
    id: string;
    name: string;
    number: string;
    groupId?: string;
    group?: string;
    dataType: 'text' | 'integer' | 'decimal' | 'boolean' | 'date' | 'formatted_text'
            | 'dictionary' | 'single_select' | 'multi_select' | string;
    contextAware: boolean;
    isCompound: boolean;
    toBeRemoved: boolean;
    readOnly: boolean;
    unit?: string;
    restrictions?: {
        enum?: {
            type?: string;           // e.g. "color" for color pickers
            values: SelectOption[];  // all selectable options — present for single_select and multi_select
        };
        range?: { min?: string; max?: string; step?: string };
        text?: { whitespaces?: boolean };
    };
}
```

### `values[]` vs `dictionary[]` in product attributes

| `dataType` | Field used | How to resolve |
|---|---|---|
| `dictionary` | `dictionary[]` | `GET /api/pim/definitions/dictionary/{definitionId}/values/{valueId}` — one call per value |
| `single_select`, `multi_select` | `values[]` | Options are **already in the definition** under `restrictions.enum.values` — no extra API calls |
| `text`, `integer`, `decimal`, `boolean`, `date`, `formatted_text` | `values[]` | Plain text — display as-is |

**Select/multiselect resolution — no extra API calls needed:**
```typescript
// Build valueId -> label map from definitions list (already fetched)
const selectLabels: Record<string, string> = {};
definitions.forEach((def) => {
    if (def.dataType === 'single_select' || def.dataType === 'multi_select') {
        def.restrictions?.enum?.values?.forEach((opt) => {
            selectLabels[opt.valueId] = opt.value;
        });
    }
});

// Render: values[] for select types
attr.values?.map((id) => selectLabels[id] ?? id).join(', ')
```
