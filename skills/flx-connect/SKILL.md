---
name: flx-connect
description: Use when a host has just connected this agent to FLX Rides, when a tool call fails with no_grant, scope_required, or paused, or when the host asks whether the connection is working.
version: 1.0.0
author: FLX Rides
metadata:
  flx:
    tools: [whoami, read_grants, read_policy, read_attention]
    scopes: [read]
    tier: 1
    events: []
    schedule: null
    unattended: false
    apps: [flx]
    jobs: []
    flow: "When you connect it, or a tool is refused → checks the connection → tells you in plain words what it can do (reads only)"
    agents: [openclaw, hermes, claude, grok]
  hermes:
    tags: [flx, setup]
  openclaw:
    emoji: "🔌"
---

# Connect to FLX Rides

You are working on behalf of one FLX Rides host — a person running their own car business. Everything you do runs as them, on their account only. This skill checks the connection and tells the host, in plain words, what you can do.

## When this fires

Right after the host connects you, or whenever a tool answers `[no_grant]`, `[scope_required]`, or `[paused]`.

## Procedure

1. Call `whoami`. If it fails with `[no_grant]`, tell the host: "I'm not connected to FLX yet — open Agents › Connect your agent in FLX and follow the steps for your agent." Stop.
2. Read the `scopes` it returns. Map each to what it means for the host (`read` — see the business; `listings` — manage listings; `messaging` — reply to renters; `trips` — run trips by their rules; `money` — propose money actions). Tell the host what you can and cannot do with the access they granted, in one short list.
3. Call `read_grants`. If more than one agent is connected, name them; the host may not remember.
4. Call `read_policy`. Summarize the host's standing rules in a sentence each ("Your trips are 3–30 days with a 2-hour buffer."). These are the host's fences — you follow them; you cannot change them.
5. Call `read_attention` and tell the host what needs them right now, hardest first.
6. Remind the host once: anything with money, accepting a trip, and every bid wait for their approval on FLX, and they can pause every agent from Agents at any time.

## If a tool answers `[paused]`

Say so and stop. Do not retry. The host resumes from the Agents page.

## If a tool answers `[scope_required]`

Name the scope and what it lets you do (step 2's words). Ask the host to reconnect granting it. Do not work around it.

## Adapt this

- The summary in step 2 — some hosts want one line, some want the list.
- Whether to run step 5 every time (a small fleet) or only on request (a large one).
