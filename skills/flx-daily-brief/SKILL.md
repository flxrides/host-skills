---
name: flx-daily-brief
description: Use when the host asks what's happening with their cars today, or on a scheduled morning check-in, to brief them on what needs their attention, what their agent proposed, and what changed since the last brief.
version: 1.0.0
author: FLX Rides
metadata:
  flx:
    tools: [read_attention, read_proposals, read_events, read_policy]
    scopes: [read]
    tier: 1
    events: []
    schedule: "0 7 * * *"
    unattended: true
    apps: [flx]
    jobs: ["hear-about-it"]
    flow: "Every morning → reads what needs you, what's waiting for your yes and what changed → sends you the brief (reads only)"
    agents: [openclaw, hermes, claude, grok]
  hermes:
    tags: [flx, daily]
  openclaw:
    emoji: "☀️"
---

# Daily brief

A morning read of the host's car business: what needs them, what's waiting for their yes, and what changed. Read-only — this skill changes nothing.

## When this fires

On the host's schedule (default 7am, their timezone) or when they ask "what's going on with my cars".

## Procedure

1. Read your stored cursor for `read_events` (from the last brief). If there is none, use the last 24 hours.
2. Call `read_attention`. These are ranked hardest-first by FLX; keep that order.
3. Call `read_proposals`. Anything listed is waiting for the host's yes — money that will not move until they act.
4. Call `read_events` with `since_cursor`. Group by type: new requests, messages, bid outcomes, payouts, trips returned.
5. Write the brief in this order, and only the sections that have content:
   - **Needs you** — the attention items, one line each, with the link FLX gave you.
   - **Waiting for your yes** — each proposal: what, how much, why your agent proposed it, when it expires.
   - **Since yesterday** — the grouped events, counts first ("2 new requests, 1 bid accepted").
   - **Quiet** — if nothing else applies, say so in one line.
6. Store `next_cursor` for tomorrow.

## Rules

- Never call a write tool from this skill.
- Amounts in dollars, dates in the host's timezone, names only when FLX gave them.
- If a tool fails, say which section is missing and why; do not invent it.

## Adapt this

- The schedule and the delivery channel (Telegram, Slack, email) are your harness's settings.
- Section order, length, and whether "Quiet" is sent at all.
