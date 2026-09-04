# JMS API — Agent Context

This is context for the JMS (Journal Management System) backend API — `jms-saas-publisher`.
It's kept current automatically: `references/openapi.json` is regenerated from the backend's
`@hono/zod-openapi` route definitions on every merge to its `main` branch and synced here.
Never hand-edit `references/openapi.json` or `references/meta.json`.

## How to use this

1. Read `references/api-guide.md` first for concepts that rarely change: base URL/versioning,
   auth model, standard response envelopes, caching behavior, and the resource map (which
   endpoints are journal-scoped vs. publisher-scoped).
2. For the exact shape of a specific endpoint (parameters, request/response schema, required
   vs. optional fields), read the corresponding entry in `references/openapi.json` — a
   standard OpenAPI 3.1 document. Search its `paths` object for the route you need.
3. `references/meta.json` records which `jms-saas-publisher` commit/version the spec came
   from, if you need to check freshness.

## If this looks stale

This directory is normally pulled into a frontend repo as a git submodule at
`.claude/skills/jms-api`. If the endpoint info seems out of date, tell the user to run
`git submodule update --remote --merge` in that repo rather than editing files here by hand.

This file is a plain-Markdown mirror of `SKILL.md` for coding agents that don't use Claude
Code's skill-frontmatter convention. Content is the same; use whichever your tooling expects.
