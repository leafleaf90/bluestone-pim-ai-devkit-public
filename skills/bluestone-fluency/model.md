# Native Bluestone data model

Where a value belongs. For HTTP bodies and flags, see [recipes.md](recipes.md) and [gotchas.md](gotchas.md).

## Product types

| Type | Role |
|---|---|
| **FAMILY** | Parent of Variant Groups. Holds facts that are true for every GROUP under it. Empty of dimensions is correct when the source only has a name. Category copy (long description, USPs) still belongs here when the source has it — that is content, not a variant axis. |
| **GROUP** | Variant Group. Shared constraints live here and inherit to children via **VLA**. |
| **VARIANT** | A child of a GROUP. Create as **SINGLE**, then assign to the GROUP (`/products/variants/append/by-ids`). UI type becomes VARIANT. |
| **SINGLE** | Standalone product. Also the type used for reusable objects (colour, material, option, price list) and for SKUs before they are assigned as variants. |
| **BUNDLE** | A sellable set of existing products with quantities. Not a configured quote, not an order line, not “the SKU plus options.” The **bundle configurator** app is separate: choice rules + linked candidates on any main product — it does not require type BUNDLE, and it cannot hold per-link flags. |

In the product details panel, **Product structure** walks that tree: Variant family = FAMILY, Variant groups = GROUP, Variants = VARIANT. To open a known product from the Products grid, use the Filters tab and Number `is`.

FAMILY → GROUP has **no** VLA endpoint. Do not set `copy` on FAMILY create.

## Own the value once

Put each fact on the **highest node where it is true for every child**. Inherit down. Override only where siblings differ.

| Layer | Typical owner | How children see it |
|---|---|---|
| Catalog CLA | Category (range, channel, or reference folder) | Schema on **every** product type in that node. `mandatory` only if all types must have the value |
| FAMILY | Collection-true facts only | Product attributes on the family, if the type allows |
| GROUP | Model-true constraints | VLA `copy: true` |
| VARIANT | Differences between siblings (colourway, finish) | Written on the child |
| SINGLE (related) | Reusable object | **Relation**, not copied attributes |

Do not invent FAMILY attributes to fill the panel.

## CLA vs VLA

**CLA** (catalog node): bulk schema and **propagation** for a category. Attaching a CLA adds that definition to products in the node (and subcategories). Flags: `mandatory`, `locked`, `copyAttribute` (UI: Propagate). Attach after definitions exist. Pattern: POST the definition onto the node, then PATCH flags.

CLA is **not** required for a product to hold a value. You can PUT the attribute on a product (or add it in the product editor) with no CLA. Reads and exports that look at `product.attributes` will still see it. CLA is how you get the field onto every product in a catalog without editing them one by one. Propagation still does not fill a value unless you set one (or use propagate-value).

CLA does **not** target a product type. FAMILY, GROUP, VARIANT, and SINGLE in the same node all get the definition — and all get `mandatory` if that flag is set. Empty FAMILY then fails hard (red fields, blocks publish/sync) even when the value is meant to live on GROUP.

**CLA `mandatory`** — only for facts that every product type in that category must have.

**Completeness requirements** — for facts that are mandatory on some types only (`includedProductTypes`: FAMILY / GROUP / VARIANT / SINGLE / BUNDLE). Score and workflow, not the same hard gate as CLA mandatory.

**Split catalogs per product type** — the other way to keep CLA mandatory (a GROUP-only folder can require dims). More trees to maintain. Prefer completeness unless the hard gate is required.

**VLA** (GROUP → VARIANT only): value ownership on a variant group. `copy: true` means the attribute is a VLA and children inherit. `definingAttributes` / `locked` / `mandatory` **require** `copy: true` or the API returns 400. `copy` on SINGLE / VARIANT / BUNDLE create also returns 400.

`forceVla=true` overwrites existing variant values. `false` (default) fills empty variants only. Use `true` when the GROUP value is the source of truth; `false` when the child owns the value (charges, images, SKU-only fields).

Help: [The data model — VLA](https://help.bluestonepim.com/the-data-model#VLA).

## Attributes vs relations vs contexts vs assets

- **Attribute** — a typed fact on that product (decimal max load, text care code).
- **Relation** — a pointer to another product (colour SINGLE, material SINGLE). Do not flatten related-object fields onto the variant.
- **Context** — locale for name/description (and other context-aware fields). Not an attribute definition.
- **Asset** — media in Media Bank (binary or URL). A filename in an ERP column is not an asset until it is uploaded or resolved to a URL. File facts (QC, colour shown, dimensions, reject reason) are **asset attributes** on that Media Bank object (`POST /media-bank/assets/{id}/attributes/{definitionId}` then typed PUT), not a proxy PIM product. Select and dictionary definitions **are** allowed; the PUT body is `{ selectIds }` / `{ dictionaryIds }`, not `{ values }`. There is no separate “register this definition for DAM” API — assignment is per asset.

Join/lookup files in a source system are keys to related products, not hundreds of attributes on the SKU.

## Association facts

A fact that is true only for a **specific link** between two products belongs on the association, not on either product. Classic case: the same SKU is standard under one parent and optional under another. Duplicating the SKU to store that flag fails reuse.

PIM **core** connections (`POST /products/{id}/connections/products`) are binary: `relationId` + `to` + optional quantity. `ConnectionDto` has no typed attributes.

**Relation attributes** (v1-apps, host `api.<env>.bluestonepim.com/relation`) sit on top of those core edges. Two levels:

| Level | Meaning | Write |
|---|---|---|
| `relation` | One value for that relation type on this product (same for every related product of that type) | `PUT /products/{id}/relations/{relationId}/attributes/{attrDefId}` |
| `product` | One value **per related product pair** | `PUT /products/{id}/relations/{relationId}/relatedProducts/{relatedProductId}/attributes/{attrDefId}` |

Assign the definition first: `POST /relationDefinitions/relations/{relationId}/attributes` `{ "level": "relation" or "product", "attributeDefinitionId" }`.

Use **product-level** relation attributes when the fact is binary (this parent ↔ this SKU). They do **not** help a ternary (parent + choice group + SKU) on a **shared** choice product. A flag on a shared “choice → parent” edge collapses two SKUs in the same group to one value. A flag on a shared “choice → SKU” edge cannot differ by parent.

The ternary decomposes if you introduce a **parent×choice-group** node: one flag lives on that node (or on parent↔node); the other is then binary on node↔SKU and can be a product-level relation attribute. That is still a junction, just coarser than one card per pick. Cloning the shared choice product per parent so a binary link can hold the flag **is** the same junction in disguise.

Prefer a **pick-level SINGLE** when the flag must behave as a normal product attribute (grid, CLA, completeness, search, PAPI, native publish). Prefer the coarser node + relation attributes when you do not need to search or score the pick as a product. Do not flatten either flag onto the reusable SKU.

Core PIM keeps **one connection per relation type per pair**. Product-level relation attributes are therefore **one value set per pair**, not a list of dated records. That is enough for actor/reason/dates on a single exception. Use a junction SINGLE when the business needs a numbered, searchable log that can stack (many exceptions on the same pair), or when the fact is ternary. Relation attributes are also overwriteable PUTs — they are not an immutable audit trail. Do not hang pair-only payload (who approved this exception) as ordinary attributes on either end of the link.

## Availability scopes

Markets, regions, channels, sites, and similar “who can see this” objects are **not sellable**. Prefer a catalog node when you only need assortment membership. Use a reference SINGLE in a dedicated Availability / References catalog when you must relate to them (include/exclude). A **small closed set of regions** as “where this offer is sold” can be a `multi_select` on that sellable product (empty = all listed scopes). Use reference SINGLEs + relations when the region is an object with its own facts. Do not copy the same ticks onto a reused SKU. Do not model regions as FAMILY / GROUP / VARIANT. Do not clone a reusable option SKU per site so the project can “own” it — relate the shared option to the site or channel instead.

## Prices at more than one scope

PIM has **no native price-book merge** (list price + regional overlay + location). Do not clone the SKU per geography, and do not mint a new list-price attribute per region on the product.

| Scope | Where it belongs in PIM |
|---|---|
| One national list/display figure | Decimal (and book / version / effective-from / last change-reason) **attributes on that SKU**. A feed ingest **overwrites** that stamp. It is not a price-history stack — dated history needs price-line SINGLEs or the external engine. Optional: a related price-list SINGLE. |
| Same SKU, different figure per region or site | **Product-level relation attribute** on SKU↔region (or SKU↔site), or a **price-line junction SINGLE** related to the SKU and the scope. Junction if you need stacked dated rows. |
| Binding quote / tax / checkout price | Usually **outside PIM** (CPQ or commerce). An order line or location is not a product type. |

**Contexts are one axis** (the `context` header). They are built for **locale**. `contextAware` attributes + `context-fallback` give “this key, else default” — not a national→regional→location stack. Using sales-region ids as contexts can store a second list price on the same SKU **only if** (a) the org will not also need locale on that definition, (b) the overlay is one flat list with fallback to `en`, and (c) cardinality stays small. Compound keys (`en|REGION-A`) explode. Site or location counts do not belong in the context list. Assortment (which offer a region sells) still belongs on **relations**, not in a context switch. A brochure figure in PIM is a **projection** unless Pricing has agreed PIM is system of record.

## Variant axes

A variant axis is something that **differs per child** and is **finite**.

- Good: colour, size run, finish — preferably **relations** to SINGLEs when those objects are reused.
- Bad: customer-entered width × height millimetres, or any axis that explodes into thousands of children.
- Shared on every child (same certification, same max load) → GROUP inherit, not a value written only on the variant.

## Catalogs

Catalog nodes are navigation and CLA homes. They are not a substitute for FAMILY. A “range” or “shop family” in the source is often a **catalog node**; a “collection” is often a **FAMILY**. Confirm against how editors will browse, not against source file names.

## Write order

1. Catalog tree, then attribute groups + definitions, then **CLAs**.
2. Reference SINGLEs (colour, material, option, …) with their own attributes.
3. FAMILY (names; attributes only if collection-true).
4. GROUP with inherited values + VLA flags (`copy` / `mandatory`).
5. VARIANT as SINGLE, then assign to GROUP.
6. `PUT /products/{groupId}/variants/attributes/{defId}` for VLA flags (`forceVla` as above).
7. Relations from VARIANT or GROUP to SINGLEs.

Reuse by **product/definition number**. On definition reuse, assign `groupId` via GET-then-PUT (do not recreate). Deleted groups do not delete definitions — existing defs may be unassigned.

## Choose `dataType` first

This is the default. Pick the type from how the value is **used**, not from how easy the first write is. A loader that only implements `PUT …/simple`, or a TypeScript union that is `text`-only, is **not** a reason to define closed lists as `text`.

| Need | Type | Write |
|---|---|---|
| Closed, small vocabulary (Yes/No, Mandatory/Optional, Approve/Pending/Reject, Draft/Live) | `single_select` (`multi_select` if several apply at once) | Option **ids** via `PUT …/select` (or payload `selectValueIds`). Never the label string. Empty `multi_select` can mean “all listed scopes” (e.g. national) when ticks **restrict**. |
| Colour chip in the native UI | `single_select` with `restrictions.enum.type = "color"` and option `metadata` = hex (`#c6d8b8`) | Same as select. Do **not** store hex as a second text field. |
| Growable coded pick-list merchandisers grow (finishes) without a definition edit each time | `dictionary` | Value ids via `PUT …/dictionary`. Not a comment box. |
| Narrative reason the editor types (photo reject note, exception reason) | `text` | `values[]` or Media Bank `PUT …/simple`. Not a coded pick-list. |
| Reusable object with its own facts (a colourway that has a code, supplier, relations) | Related **SINGLE**, not an attribute | Relation |
| Open / unique / infinite (manufacturer code, ERP key, file name, prices, dates, rank) | `text` / `integer` / `decimal` / `date` / `formatted_text` | `values[]` |

Native product publish (green check / CONNECTED) is **not** a custom publish-status attribute. Do not add a Draft/Live text (or select) that shadows native state. Completeness must not score that field — `ATTRIBUTE_HAS_VALUE` only checks that something is filled, not that the product is published.

### Do not flip a live definition

Do **not** change `dataType` on a live definition **number**. Existing product values stay strings; they do not become option ids. A type change is a **new** definition (new number), then rewrite values as option ids. In-place flip is wrong.

Definition **display names are unique org-wide**. A replacement select cannot reuse “Room” while the old text def still exists. Pick a new name, move values / CLAs / completeness / column setups to the new number, then archive the old def.

### Payload shapes

| `dataType` | Product payload | Notes |
|---|---|---|
| `text`, `integer`, `decimal`, `boolean`, `date`, `formatted_text` | `values[]` | Plain values |
| `single_select`, `multi_select` | `values[]` are option IDs | Labels on the definition (`restrictions.enum.values`) |
| `dictionary` | `dictionary[]` are value IDs | Resolve via dictionary endpoints; not the same as select |
| `matrix` | Native grid | Axis ticks live on the definition; cell IDs are PIM-generated. Awkward for large external grids — a JSON/text attribute plus a PBC is often clearer |

`contextAware: true` on a definition means values are per locale context. Completeness `ATTRIBUTE_HAS_VALUE` only checks filled; a select still stops `Approove` / `approve` drift.

## Completeness

Empty FAMILY is correct when collection-level facts do not exist. Do not use CLA `mandatory` on a mixed catalog to force GROUP-owned fields. Use completeness requirements scoped by product type. See [completeness.md](completeness.md).
