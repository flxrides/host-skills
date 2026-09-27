# FLX Rides — host skills tap

Procedural skills a host's own agent harness installs to work their FLX Rides account. Each skill
is pure procedure over the host-agent tool surface — no scripts, no secrets. The harness runs as
the host, on their account only; anything that moves money waits for the host's yes on FLX.

## What's here

Recipes: FLX plus one or more apps, doing one job for the host's business (#288). Today every recipe
is "FLX only" (`apps: [flx]`):

- `flx-connect` — check the connection and tell the host what the agent can do.
- `flx-daily-brief` — a read-only morning brief (the one brief; `morning-brief` folded into it).
- `fleet-repricer`, `demand-radar`, `open-call-bidder`, `inbox-autopilot`, `trip-concierge`,
  `mileage-fee-filer` — the single-platform routines.
- `host-autopilot` — the whole crew as one skill (`bundle_of`).
- `_shared/references/apps.json` — the ONE list of apps, jobs and agents a recipe may name.
- `_shared/references/flx-tools.md` / `.generated.json` — the tool reference, generated from the
  backend registry (`api/app/mcp_host/registry.py`). **Do not edit these by hand** — run
  `npm run gen:tool-reference`.

## Frontmatter contract

Every `SKILL.md` carries YAML frontmatter with:

- `name` — must equal the directory slug.
- `description` — must start `Use when …` (the harness's trigger text).
- `version` — semver.
- `author` — `FLX Rides`.
- `metadata.flx` — `{ tools, scopes, tier, events, schedule, unattended }`. Every tool must exist in
  the generated reference and be shipped; every tool's scope must be listed in `scopes`.
- The recipe fields in `metadata.flx`: `apps` (at least one), `jobs`, `agents` — ids from
  `_shared/references/apps.json` — and `flow`, how it works in words (trigger → action, and what
  waits for the host). `bundle_of` names other skills. An optional `metadata.hermes.blueprint`
  needs a `schedule` and a `prompt`.
- The body must include an `## Adapt this` section.

`npm run check:skills` enforces all of the above (and bans internal jargon per
`docs/marketing/04-voice.md`). Run it before committing a skill change.

## Serving and installing

`npm run skills:publish` mirrors each `SKILL.md` to `public/skills/<slug>/SKILL.md`, served at
`https://<origin>/skills/<slug>/SKILL.md` (check:skills fails on drift). Every agent installs from
that URL with its own command, built in `src/lib/host-agent/harnesses.ts` (`installSkill`,
`schedule`, verified against each agent's docs via context7 on 2026-09-26): Hermes takes the URL
(`hermes skills install <url>`), OpenClaw a downloaded folder (`openclaw skills install ./<slug>`),
Claude Code and Grok a file in `~/.claude/skills/<slug>/` or `~/.grok/skills/<slug>/`. The public
tap (`flxrides/host-skills`, synced by `.github/workflows/publish-skills.yml`) is a convenience for
shorter lines, never a requirement.

See `docs/ops/host-agent-mcp.md` for the operator runbook (env vars, publish secrets) and
`docs/marketing/04-voice.md` for the copy rules the lint enforces.
