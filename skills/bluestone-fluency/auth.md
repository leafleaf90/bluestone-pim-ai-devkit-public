# Auth

Bluestone has two API layers and two client modes. Pick both before writing a call.

## MAPI vs PAPI

| Layer | Typical prefix | Character |
|---|---|---|
| **MAPI** (Management API) | PBC: `/api/pim/…`. Machine: `https://api.<env>.bluestonepim.com/pim/…` | Same API the PIM UI uses. Raw, ID-heavy. Confirmed via Network tab. |
| **PAPI** (Partner/Public API) | Machine: `https://api.<env>.bluestonepim.com/v1/…`. Auth: `x-api-key` (a **PAPI key**, not the MAPI Bearer). Docs: `https://docs.api.<env>.bluestonepim.com/openapi/page.json` | Only products (and their attributes, relations, media) that have been **synced** via `papi-sync`. Unpublished and never-synced records are absent. Payloads are denormalised: attribute **name** + **number** + `values[]`, `media[].previewUri` / `downloadUri`, relations as `{ number, productId, reverse }`. |

Default to MAPI when matching UI behaviour (create product, CLA, VLA). Use **PAPI for downstream / published reads** (storefront, sales preview, export proof). Catalog loaders and PIM writes need **MAPI** `client_id` / `client_secret`. A PAPI key in `.env` does not publish anything — something must run a PAPI sync first.

OpenAPI specs **omit** the runtime prefix. PBC calls must include `/api/<service>/`. Machine calls use the host + service path (`/pim`, `/media-bank`, `/completeness-score`, …).

| Service | PBC prefix | Machine base (TEST) |
|---|---|---|
| PIM core | `/api/pim/` | `https://api.test.bluestonepim.com/pim` |
| Tasks | `/api/tasks/` | `https://api.test.bluestonepim.com/tasks` |
| Query builder | `/api/query-builder/` | `https://api.test.bluestonepim.com/query-builder` |
| Search | `/api/search/` | `https://api.test.bluestonepim.com/search` |
| Media Bank | `/api/media-bank/` | `https://api.test.bluestonepim.com/media-bank` |
| Completeness | `/api/completeness-score/` (confirm in Network tab) | `https://api.test.bluestonepim.com/completeness-score` |
| Relation attributes | `/api/relation/` | `https://api.test.bluestonepim.com/relation` |
| UI settings | `/api/ui-settings/` | `https://api.test.bluestonepim.com/ui-settings` |
| **PAPI** (published read) | — (not for PBC editors) | `https://api.test.bluestonepim.com/v1` (`x-api-key`) |

PROD hosts drop `.test` (`https://api.bluestonepim.com`, `https://idp.bluestonepim.com`). Never hardcode an environment URL inside a PBC — `getAxiosInstance()` resolves it.

## Mode A — PBC (session)

```typescript
import {getAxiosInstance} from '@bluestone-ext/plugin-framework';

const {data} = await getAxiosInstance().get(`/api/pim/products/${productId}`, {
  headers: {
    context: providedEnv.session.languageId,
    'context-fallback': 'true',
  },
});
```

- Auth is the logged-in user. Never put client secrets in plugin code (the bundle is visible to users).
- External HTTP must go through `/api/plugin-api/<plugin-id>/...` with `backendUrl` in `metaInfo.json`.
- Using `axios` or `fetch` breaks local proxying and fails Bluestone review.

## Mode B — Machine (client credentials)

OAuth2 client credentials against the IdP. Use for ETL, catalog loaders, and server-side integrations.

**TEST token URL:** `https://idp.test.bluestonepim.com/op/token`  
**PROD token URL:** `https://idp.bluestonepim.com/op/token`

```http
POST /op/token
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials
&client_id=...
&client_secret=...
&scope=openid profile systemRoles permissions organization email name nickname
```

If the IdP rejects `scope`, retry **without** `scope`. Cache `access_token` using `expires_in` (leave skew).

```http
GET https://api.test.bluestonepim.com/pim/products/{id}
Authorization: Bearer <access_token>
Content-Type: application/json
context: en
context-fallback: true
```

Store `client_id` / `client_secret` in server env for **that organisation**. Do not reuse one tenant’s keys against another.

## Headers that are not auth (but fail without)

| Header | Why |
|---|---|
| `context` | Locale key (`en`, …). Missing → empty or wrong localised names. |
| `context-fallback` | Usually `true` so missing locale falls back. |
| `resource-id` (response) | Create endpoints often return 200/201 with the new id **only** in this header (lowercase). |

## Permissions

MAPI operations are permissioned (`PRODUCTS` / `ADD`, `MEDIA` / `WRITE`, …). A 403 is usually the API client’s role, not a wrong path. PBC calls run as the current user.
