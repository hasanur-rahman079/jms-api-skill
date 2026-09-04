---
name: jms-api
description: Use when building or modifying a frontend that talks to the JMS (Journal Management System) API — jms-saas-publisher's REST backend. Provides current endpoints, request/response schemas, auth model, caching behavior, and known gotchas. Trigger on "JMS API", "jms-saas-publisher", or work involving journals/volumes/issues/publications/publisher/members/news endpoints.
---

# JMS API Skill

Context for the JMS (Journal Management System) backend API — `jms-saas-publisher`. This
skill exists so you don't have to be told the API's routes and parameters by hand every time;
`references/openapi.json` is synced automatically from the backend on every merge to its
`main` branch, so it is always current. Never hand-edit `references/openapi.json` or
`references/meta.json` — they are overwritten by that sync.

## How to use this

1. Read `references/api-guide.md` first — it covers concepts that don't change often:
   base URL/versioning, auth model, standard response envelopes, caching, and the resource
   map (which endpoints are journal-scoped vs. publisher-scoped).
2. For the exact shape of one endpoint (params, request/response schema, required vs.
   optional fields), look it up in `references/openapi.json` — it's a standard OpenAPI 3.1
   document. Find it by grepping the `paths` object for the route, e.g.:
   ```
   grep -n '"/journals/{journalSlug}/publications"' references/openapi.json
   ```
   then read the surrounding `get`/`post`/etc. operation object for `parameters`,
   `requestBody`, and `responses`.
3. Check `references/meta.json` for which commit/version of `jms-saas-publisher` the spec
   was generated from, if staleness is ever in question.

## Freshness

`references/meta.json` has `generatedAt` and `sourceCommit`. If the frontend you're working
in has an old copy of this skill (e.g. an outdated git submodule pointer), tell the user to run:
```
git submodule update --remote --merge
```
in the consuming repo to pull the latest spec — do not try to hand-patch stale endpoint info.
