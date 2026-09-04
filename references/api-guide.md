# JMS API Guide

Concepts and conventions for the JMS (Journal Management System) backend API
(`jms-saas-publisher`) that don't change often. For exact per-endpoint parameters and
schemas, use `references/openapi.json` — this file is deliberately not a full endpoint
reference, since that part is now auto-generated and always current.

## Base URL & versioning

- Base path: `/api/v1` (URI-based versioning)
- Format: JSON
- Interactive docs: `GET /api/v1/scalar` (Scalar UI)
- Raw spec: `GET /api/v1/openapi.json` — same content as `references/openapi.json` here

## Auth model

- All `GET` requests are public — no auth required.
- `POST` / `PUT` / `DELETE` require a Bearer token: `Authorization: Bearer jms_...`
- `401 UNAUTHORIZED` → invalid/missing token. `403 FORBIDDEN` → valid token, but the caller
  lacks write permission for that journal (this is a multi-tenant, per-journal permission
  check, not a global role check).

## Standard response envelopes

Every response is one of these four shapes:

```ts
// Single object
{ success: true; data: T | null }

// List (non-paginated)
{ success: true; data: T[]; count: number }

// Paginated list
{
  success: true;
  data: T[];
  pagination: { page: number; limit: number; total: number; totalPages: number; hasMore: boolean };
}

// Error
{
  success: false;
  error: {
    code: "UNAUTHORIZED" | "FORBIDDEN" | "NOT_FOUND" | "VALIDATION_ERROR"
        | "RATE_LIMITED" | "JOURNAL_NOT_FOUND" | "INTERNAL_ERROR";
    message: string;
  };
}
```

Always branch on `success` first — don't assume `data` exists without checking it.

## Rate limiting & caching

- Rate limit: 100 requests / 60s per IP.
- Several read-heavy routes carry `Cache-Control` (`s-maxage` / `stale-while-revalidate`):
  homepage (5m/10m), about/indexing/quicklinks (1h/24h), volumes (30m/1h), stats (10m/30m),
  article-types & member-roles (1h, no SWR), all `/publisher/*` routes (30m/1h). Don't
  fight this with your own aggressive client-side polling.

## Resource map

- **System**: `GET /health` — API health, app version, DB migration state. If
  `migrationsPending > 0`, something is wrong server-side — not a frontend bug.
- **Publisher scope** (`/publisher/...`, not journal-specific): `publisher` (org info),
  `publisher/news`, `publisher/events`, `publisher/committee` — each with list + `{slug}`/`{id}`
  detail routes.
- **Journals scope**: `GET /journals` — list of all journals (used for switchers/directories).
- **Journal scope** (`/journals/{journalSlug}/...` — nearly everything else lives here):
  `homepage`, `about`, `stats`, `statistics`, `indexing`, `quicklinks`, `article-types`,
  `member-roles`, `keywords`, `authors`, `members`, `pages`, `news`, `special-issues`,
  `volumes`, `issues`, `publications` (plus nested `publications/{manuscriptId}/analytics`
  and `publications/{manuscriptId}/citing-works`).

Every journal-scoped route requires a valid `{journalSlug}` in the path — an unknown slug
returns `JOURNAL_NOT_FOUND`, not a generic 404.

## Known gotchas

- The publications list endpoint's `country` filter matches **any** author affiliation
  country, not just the corresponding author — consistent with MDPI-style multi-country
  attribution. Don't assume it filters on corresponding-author country only.
- `info.version` in the OpenAPI doc reflects `jms-saas-publisher`'s `package.json` version
  at spec-generation time (see `references/meta.json` for the exact source commit).
