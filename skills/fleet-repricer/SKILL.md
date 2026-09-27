---
name: fleet-repricer
description: Use when the host wants their cars' rates moved with nearby demand, overnight or on request,
  inside the floor and ceiling they set on each car.
version: 1.1.0
author: FLX Rides
license: MIT
platforms:
- macos
- linux
metadata:
  flx:
    tools:
    - read_attention
    - listings_update_pricing
    scopes:
    - read
    - listings
    tier: 2
    events: []
    schedule: 0 2 * * *
    unattended: true
    apps:
    - flx
    jobs:
    - fill-empty-days
    flow: Every night → reads each car's rate and nearby demand → reprices inside your floor and ceiling
      (reversible, runs on its own)
    agents:
    - openclaw
    - hermes
    - claude
    - grok
  hermes:
    tags:
    - flx
    - pricing
    - listings
    related_skills:
    - flx-daily-brief
    - open-call-bidder
    - host-autopilot
    blueprint:
      schedule: 0 2 * * *
      prompt: Reprice my FLX fleet for tonight, inside the floor and ceiling I set on each car.
  openclaw:
    emoji: 🏷️
---

# Fleet Repricer

Watches nearby rates and reprices each car overnight — inside the floor and ceiling you
set, never outside them. Every change is reversible.

## When to use

- Once a night, while the fleet is idle — the bundled schedule runs it at 2am.
- Changes take effect for new trips only; a car already booked is untouched.
- Don't use it on a car with no floor/ceiling set — it will skip that car by design.

## Prerequisites

- FLX connected, granting **read** and **listings** access.
- A floor and ceiling per car (or fleet-wide) in your FLX rules. Without them a car is
  left alone.

## Procedure

1. Call `read_attention` and read the fleet to get each active car and its current daily
   rate.
2. For each car, judge nearby demand and the host's floor/ceiling. Pick a new rate only
   if it's a meaningful move and stays **inside** the bounds.
3. Call `listings_update_pricing` with the car and the new rate (dollar strings, e.g.
   `"95.00"`). A rate outside the configured min/max is refused by the server — treat a
   refusal as "leave it where it is," not an error to retry.
4. Summarize: which cars moved, from/to, and why; which were left alone and why (at
   ceiling, no demand signal, no bounds set).

## Pitfalls

- **Bounds are enforced on FLX's servers.** Editing this file can't let a rate out of
  the floor/ceiling.
- A refusal isn't an error — it means the number was out of range. Log it and move on.
- This skill never opens or blocks dates, edits photos, answers a driver, or touches
  money.

## Verification

Every rate written came back accepted and sits inside that car's floor/ceiling; skipped
cars are named with a reason.

## Example

> **Repriced 3 of 5 cars overnight.**
> - 4Runner $88 → $95 (weekend demand up, ceiling $110)
> - Camry $52 → $49 (soft midweek, floor $45)
> - Model 3 $70 → $76 (nearby rates firmed)
> Left alone: Sienna (already at ceiling), Tacoma (no floor/ceiling set — set one to
> include it).

## Adapt this

- Change the schedule to reprice more or less often, or only on weekends.
- Tell it how big a move is worth making ("only move a rate by $5 or more").
- Set or change each car's floor and ceiling in FLX; the skill never goes outside them.
