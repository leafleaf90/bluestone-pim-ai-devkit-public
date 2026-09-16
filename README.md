# Bluestone PIM AI DevKit

This GitHub repo is the **DevKit**. **[Bluestone Fluency](skills/bluestone-fluency/SKILL.md)** is the one Agent Skill inside it. It makes a coding agent fluent in **native Bluestone PIM**: the data model, MAPI/PAPI, and PBC UI plugins.

Load Fluency; it tells the agent which file to open next.

> **[Bluestone PIM Labs](https://labs.bluestonepim.com/) community project.** Not an official product. No SLA, no support commitments. Contributions and forks welcome.

Fluency is a **companion** to official Bluestone documentation, not a replacement. Product and data-model truth is [help.bluestonepim.com](https://help.bluestonepim.com). API truth is [docs.api.bluestonepim.com](https://docs.api.bluestonepim.com) (Management API and Public API). PBC truth is the official plugin-template documentation. This skill steers a coding agent; when it disagrees with those sources, those sources win.

## What is Fluency?

Bluestone is a specific product (FAMILY / GROUP / VARIANT, CLA, VLA, relations, contexts, PBC surfaces). Agents that only know “PIM” invent the wrong tree and guess OpenAPI paths.

Fluency packages:

| File | When |
|---|---|
| [SKILL.md](skills/bluestone-fluency/SKILL.md) | Router + always-on rules |
| [model.md](skills/bluestone-fluency/model.md) | Where a value belongs |
| [auth.md](skills/bluestone-fluency/auth.md) | Plugin session vs OAuth client credentials |
| [recipes.md](skills/bluestone-fluency/recipes.md) | Create/upsert product, CLA, VLA, relations |
| [gotchas.md](skills/bluestone-fluency/gotchas.md) | `resource-id`, 409, prefixes, attribute IDs |
| [completeness.md](skills/bluestone-fluency/completeness.md) | Completeness, labels, assets, locales |
| [pbc.md](skills/bluestone-fluency/pbc.md) | Surfaces, `metaInfo`, React 16, `providedEnv` |
| [api-core.md](skills/bluestone-fluency/api-core.md) / [api-full.md](skills/bluestone-fluency/api-full.md) | Path lookup **only**; do not load by default |
| [insights.md](skills/bluestone-fluency/insights.md) | Where a new lesson belongs (public playbook vs private notes) |

## Requirements

- Any [Agent Skills](https://skills.sh)-compatible coding agent
- For PBC work: a plugin project based on the Bluestone external plugin template
- For machine API work: an API client (client id/secret) for the target organisation

## Installation

Install Fluency from this GitHub repo with the [skills CLI](https://skills.sh). `leafleaf90/bluestone-pim-ai-devkit-public` is the DevKit; `@bluestone-fluency` is the skill. That command works now; it does not wait for the skills.sh directory to index the repo.

```bash
npx skills add leafleaf90/bluestone-pim-ai-devkit-public@bluestone-fluency
```

Or clone and point the agent at `skills/bluestone-fluency/`:

```bash
git clone https://github.com/leafleaf90/bluestone-pim-ai-devkit-public.git
```

This repo ships **one** skill. Do not force-add `skills/bluestone-fluency/insights.internal.md` (gitignored). That file is a private dated log. The public [insights.md](skills/bluestone-fluency/insights.md) is only a pointer.

## Usage

Describe the work in Bluestone terms. Examples:

```
Scaffold a productPanelTab that shows this product's attributes using getAxiosInstance.
```

```
Map this ERP catalog into FAMILY / GROUP / VARIANT. Colours are reusable. Width×height is not a variant axis.
```

```
Upsert products into TEST with MAPI client credentials. Reuse by product number. Attach mandatory CLAs after definitions exist.
```

The agent should:

- Put facts on the highest true node (CLA vs VLA vs relation vs context)
- Use `getAxiosInstance()` in a PBC, or Bearer tokens from the IdP in a server integration
- Look up paths instead of guessing; read `resource-id` on create
- Keep `index.ts` and `metaInfo.json` in sync (PBC)

## Remaining work

See [ROADMAP.md](ROADMAP.md) (OpenAPI-generated catalogue, PAPI recipes, Media Bank upload, skills.sh listing).

## Resources

Official (source of truth):

- [Bluestone PIM help](https://help.bluestonepim.com) (data model, editor behaviour)
- [API docs](https://docs.api.bluestonepim.com) (Management API and Public API). TEST: [docs.api.test.bluestonepim.com](https://docs.api.test.bluestonepim.com)
- Official PBC / plugin-template documentation (ships with the external plugin template)

Companion / community:

- [Bluestone PIM Labs](https://labs.bluestonepim.com/)
- [Bluestone PIM help: data model / VLA](https://help.bluestonepim.com/the-data-model#VLA)
- [UI Component Library](https://ui-external.test.bluestonepim.com/)
- [Icon Library](https://icons.test.bluestonepim.com/)
- TEST app: `https://app.test.bluestonepim.com`

## Contributing

- Confirmed path or response shape → [gotchas.md](skills/bluestone-fluency/gotchas.md) (short) or the relevant recipe. Do not grow `api-full.md` by hand if you can avoid it.
- Durable modeling rule → [model.md](skills/bluestone-fluency/model.md) in **generic** language (no customer names, plugin ids, or SKU schemes).
- A lesson that is only true for one catalog stays in that project’s repo (or local `insights.internal.md`). Never in the public playbook. Never `git add -f` that file.
