# Bluestone Fluency — remaining work

Shipped in the `bluestone-fluency` consolidation: one skill, generic model, session vs machine auth, recipes, gotchas, completeness/labels/assets, PBC catalogue without a duplicated API chapter.

## Next (public skill)

1. **Publish path** — list `skills/bluestone-fluency` on skills.sh. Keep `insights.internal.md` gitignored. Never `git add -f` that file.
2. **Generate `api-full.md`** from OpenAPI instead of hand-maintaining ~7k lines. Keep confirmed shapes in `gotchas.md` / a short `shapes.md`.
3. **PAPI recipes** — as paths are confirmed in Network tab, add a section to `recipes.md` (richer reads vs MAPI writes).
4. **Media Bank upload** — multipart/binary create + attach-to-product recipe (currently pointed at, not spelled out).
5. **Notifications / sync** — webhook subscribe + public API sync as one recipe page when a real integration needs it.
6. **Split `pbc.md`** if it stays large: `pbc-surfaces.md` vs `pbc-env.md` (still one hop from `SKILL.md`).
7. **Example plugin** — optional `examples/` with a minimal `productPanelTab` that follows the rules (React 16, `getAxiosInstance`, `metaInfo`).

## Next (Bluestone-internal)

- Keep appending gitignored `insights.internal.md` from implementation work; promote only generic rules into `model.md` / `gotchas.md`.
- After agents have used Fluency for a few projects, trim `api-core.md` to endpoints the recipes do not already cover.
