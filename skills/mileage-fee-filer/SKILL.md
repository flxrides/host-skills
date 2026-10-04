---
name: mileage-fee-filer
description: Use when a trip has come back and the host wants any mileage overage fee filed for their
  yes, or on the daily catch-up of returned trips.
version: 1.1.0
author: FLX Rides
license: MIT
platforms:
- macos
- linux
metadata:
  flx:
    title: File the mileage fee after a return
    mode: waits
    preview:
      title: "Mileage fee filed"
      body: "The Civic came back 140 miles over. FLX computed the fee."
      ask: "Approve it in FLX"
    listed: false
    waits: Each fee waits for you
    tools:
    - read_proposals
    - read_trip
    - money_file_fee
    - money_withdraw_proposal
    scopes:
    - read
    - money
    tier: 4
    events: []
    schedule: 0 9 * * *
    unattended: true
    apps:
    - flx
    jobs:
    - track-the-money
    flow: Every morning → reads the last day's returned trips → files the mileage fee FLX computes (each
      fee waits for you)
    agents:
    - openclaw
    - hermes
    - claude
    - grok
  hermes:
    tags:
    - flx
    - money
    - fees
    - mileage
    related_skills:
    - trip-concierge
    - flx-daily-brief
    - host-autopilot
    blueprint:
      schedule: 0 9 * * *
      prompt: Check my returned FLX trips for a mileage overage and file the fee FLX computes, for my
        approval.
  openclaw:
    emoji: 🧾
---

# Mileage Fee Filer

After a trip comes back over its mileage allowance, this skill files the overage fee FLX
computes from the odometer. **Every fee waits in the host's queue for their approval**:
nothing is charged until they approve it.

## When to use

- Right after a drop-off is recorded — wire it to your agent's post-return trigger, or
  let the `trip-concierge` skill hand off.
- As a daily catch-up — the bundled schedule sweeps the last day's returns at 9am.
- On demand: "file the mileage fee on trip 4821".

## Prerequisites

- FLX connected, granting **read** and **money** access.
- Your per-mile rate set on each car in FLX.

## Procedure

1. Call `read_proposals` and read returned trips to find any over their mileage allowance
   with no fee staged or filed.
2. Call `read_trip` for the trip's allowance and per-mile rate.
3. Call `money_file_fee` with `kind: mileage_overage` and `dry_run: true` — it returns the amount FLX
   computed, without filing. Stop if it's zero (no overage).
4. Call `money_file_fee` (`kind: mileage_overage`) for real with a per-trip idempotency key, so a retry or
   the catch-up never files twice. It is staged for the host's approval.
5. If a fee was staged in error, call `money_withdraw_proposal` with its action id while
   it's still waiting — nothing executes; the row becomes `withdrawn`.

## Pitfalls

- **The amount is FLX's to compute, never the skill's to set** — it comes from the logged
  odometer and the host's per-mile rate.
- **It only stages.** A real charge always waits for the host's approval; FLX enforces
  that on its servers, whatever the agent is told.
- It can't refund, cancel, pay out, or file any other fee.

## Verification

Every filed or staged fee has a non-zero computed amount, a per-trip idempotency key, and
an entry on the host's activity log.

## Example

> **Mileage fee ready for your approval: $48.**
> Camry, returned Sun. Allowance 600 mi, driven 840 (240 over × $0.20). Approve it or
> decline it in your Inbox.

## Adapt this

- Change when it runs, or ask it to leave trips under a mileage you name.
- The amount is always the one FLX computes; each fee waits for your yes.
