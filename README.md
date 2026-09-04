# jms-api-skill

A coding-agent skill for the JMS (Journal Management System) API — `jms-saas-publisher`'s
REST backend. Load it into any frontend project built against that API so the agent working
there (Claude Code, Cursor, Copilot, etc.) already knows the routes, parameters, auth model,
and quirks — instead of a human re-explaining them every time.

## How it stays current

`references/openapi.json` and `references/meta.json` are **not hand-maintained**. On every
push to `main` in `jms-saas-publisher`, its `publish-openapi.yml` workflow regenerates the
OpenAPI 3.1 spec from the live `@hono/zod-openapi` route definitions and pushes the result
here as a commit. Only `references/api-guide.md` (concepts/prose) and `SKILL.md`/`AGENTS.md`
are hand-curated; everything endpoint-specific comes from that automated sync.

## Installing this skill in a new frontend repo

Add it as a git submodule at a fixed path so it can be updated with one command later:

```bash
git submodule add https://github.com/hasanur-rahman079/jms-api-skill .claude/skills/jms-api
git commit -m "Add jms-api skill"
```

To pull the latest synced spec later:

```bash
git submodule update --remote --merge
```

- **Claude Code** picks up `.claude/skills/jms-api/SKILL.md` automatically.
- **Other coding agents** (Cursor, Copilot, etc.) should be pointed at
  `.claude/skills/jms-api/AGENTS.md` — same content, no Claude-specific frontmatter.

## Contents

```
SKILL.md                 # Claude Code entry point
AGENTS.md                # generic fallback for other coding agents
references/
  openapi.json           # ground truth spec, auto-synced — do not hand-edit
  meta.json              # {version, sourceCommit, generatedAt} — auto-synced
  api-guide.md           # hand-curated: auth model, envelopes, caching, resource map, gotchas
```
