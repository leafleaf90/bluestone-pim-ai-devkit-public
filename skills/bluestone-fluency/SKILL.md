---
name: bluestone-fluency
description: >-
  Fluency in native Bluestone PIM: data model (FAMILY/GROUP/VARIANT/SINGLE,
  CLA, VLA, relations, contexts), MAPI and PAPI, and PBC UI plugins. Use when
  building or debugging Bluestone plugins, calling PIM APIs, mapping a catalog
  into PIM, writing products/definitions into TEST or PROD, or deciding where
  a value belongs. Triggers on: Bluestone, PIM, PBC, plugin, MAPI, PAPI,
  getAxiosInstance, metaInfo, pluginPartCreator, FAMILY, GROUP, VARIANT, VLA,
  CLA, completeness, media-bank, catalogs, attribute definitions, column setup,
  ui-settings, columnsSetups.
---

# Bluestone Fluency

You are fluent in **native Bluestone PIM** — how the product thinks, how you talk to it, and how you extend the UI. Do not invent a generic PIM. Do not guess paths.

This skill is the whole DevKit. Open the linked file for the current task; do **not** load the API catalogues unless you need a specific path.

## Official docs win

This skill is a **companion**. Do not treat it as a substitute for official Bluestone documentation.

| Source | Truth for |
|---|---|
| [help.bluestonepim.com](https://help.bluestonepim.com) | Product, data model, editor behaviour |
| [docs.api.bluestonepim.com](https://docs.api.bluestonepim.com) (TEST: [docs.api.test.bluestonepim.com](https://docs.api.test.bluestonepim.com)) | Management API and Public API contracts |
| Official PBC / plugin-template docs | Surfaces, `metaInfo`, `@bluestone-ext`, `getAxiosInstance` |

When this skill and those sources disagree, follow the official docs. When OpenAPI omits a runtime prefix or a call fails, confirm the path in the PIM UI Network tab, then record the gotcha here.

## Open next

| Task | Read |
|---|---|
| Where a value belongs, FAMILY/GROUP/VARIANT, CLA vs VLA, relations vs attributes | [model.md](model.md) |
| Choosing `dataType` / select vs dictionary vs text, colour chips, do not flip a live definition | [model.md](model.md) § Choose `dataType` first |
| OAuth client credentials vs plugin session auth | [auth.md](auth.md) |
| Create/upsert products, CLA, VLA, definitions, relations, relation attributes | [recipes.md](recipes.md) |
| `resource-id`, 409, `forceVla`, prefixes, attribute IDs | [gotchas.md](gotchas.md) |
| Completeness, labels, assets, contexts | [completeness.md](completeness.md) |
| Product-grid column setups (`ui-settings`) | [recipes.md](recipes.md) §12 and [gotchas.md](gotchas.md) |
| Scaffold or debug a PBC (surfaces, `metaInfo`, React 16, `providedEnv`) | [pbc.md](pbc.md) — **not** in full; open the relevant numbered section |
| Exact endpoint path after recipes/gotchas | [api-core.md](api-core.md) (PIM core) then [api-full.md](api-full.md) |
| Where a confirmed lesson belongs (public playbook vs private notes) | [insights.md](insights.md) |

## Two ways in

**PBC (in-app plugin).** Session auth via `getAxiosInstance()`. Paths include the service prefix (`/api/pim/...`). Never `fetch` or raw `axios`. Never store secrets in the bundle.

**MAPI (integration / ETL / loader).** OAuth2 client credentials against the IdP, then `Authorization: Bearer` on `https://api.<env>.bluestonepim.com/pim/...`. Secrets live in the server environment, not in plugin code.

Both talk to the same PIM. Auth and path prefix differ. See [auth.md](auth.md).

## Rules that always apply

- Own each fact at the **highest node where it is true for every child**. Inherit down. See [model.md](model.md).
- Closed small vocabularies are `single_select` (or `dictionary` if merchandisers grow the list). Open/unique values stay scalars. Never default a closed list to `text`. Do not flip `dataType` on a live definition number. See [model.md](model.md) § Choose `dataType` first.
- CLA is catalog **schema** on every product type in the node. CLA `mandatory` only when **all** those types must have the value. Type-specific rules → completeness requirements (`includedProductTypes`). VLA is GROUP → VARIANT **value inherit**. Do not mix them.
- Reusable objects are related **SINGLE** products, not flattened variant attributes.
- Names and descriptions are **contexts**, not attribute definitions.
- Never guess an endpoint. Look it up, or confirm in the PIM UI Network tab.
- New IDs on create often arrive in the **`resource-id` header** (lowercase), not the body.
- Include `context` (locale) and `context-fallback: true` on PIM core calls that return localised values.
- **PBC only:** React **16.14.0**; `showSystemMessage` not `console.error`; never change `metaInfo.json` `id`; keep `index.ts` in sync with `metaInfo`.
- **MAPI only:** never log or commit client secrets; pin writes to the intended organisation’s credentials.
- New durable modeling or API facts: promote into [model.md](model.md) / [gotchas.md](gotchas.md) in **generic** language. Do not name customers, plugin ids, or SKU schemes.

## Do not

- Treat this skill as a replacement for help.bluestonepim.com, docs.api.bluestonepim.com, or official PBC / plugin-template docs.
- Load [api-full.md](api-full.md) “just in case.”
- Copy customer-specific mappings (collection names, SKU schemes) into generic advice.
- Treat millimetre width × height (or any infinite axis) as variants.
- Default closed vocabularies to `text` because a loader writes `PUT …/simple` or a `text`-only union.
- Set VLA `copy` on SINGLE / VARIANT / BUNDLE create (API 400).
- Set `mandatory` / `locked` / `definingAttributes` without `copy: true` (API 400).
- Set CLA `mandatory` on a mixed catalog (FAMILY + GROUP + …) for values that only belong further down.

## Maintaining

Before ending a turn that learned a Bluestone fact:

1. Durable **generic** rule → [model.md](model.md), [gotchas.md](gotchas.md), [auth.md](auth.md), or [recipes.md](recipes.md).
2. A lesson that is only true for one catalog stays in that project’s repo (or a local gitignored `insights.internal.md`). Never in this skill.
3. Do not grow [api-full.md](api-full.md) by hand.
