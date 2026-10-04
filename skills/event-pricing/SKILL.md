---
name: event-pricing
description: Use when the host keeps a Google Calendar of busy local dates (holidays, concerts, games) and
  wants their cars' rates raised ahead of them and put back after, or on the scheduled daily check.
version: 1.0.0
author: FLX Rides
license: MIT
metadata:
  flx:
    title: Raise rates ahead of busy dates on your calendar
    why: "Holidays, big games and festivals fill every car in town. Mark the busy dates on your calendar, and each car’s rate goes up ahead of them, inside your ceiling."
    elsewhere: "Reads the busy dates you mark. It never edits your calendar."
    never:
    - "It never goes above your ceiling. Without one, it asks first."
    - "It only puts back rates it changed."
    mode: runs
    preview:
      title: "Thanksgiving week is busy"
      body: "Civic raised from $295 to $340 a week, inside your $350 ceiling. Back on Dec 1."
    tools: [read_vehicles, listings_update_pricing]
    scopes: [read, listings]
    tier: 2
    events: []
    schedule: "0 6 * * *"
    unattended: true
    apps: [google-calendar]
    jobs: [price-it-right]
    flow: "Every morning → reads the busy dates on your calendar → raises each car's rate inside your ceiling ahead of them, and puts it back after"
    waits: Runs on its own, inside your ceiling, and every change can be undone
    agents: [openclaw, hermes, claude, grok]
  hermes:
    tags: [flx, pricing, calendar]
    related_skills: [fleet-repricer]
    blueprint:
      schedule: "0 6 * * *"
      prompt: Check my busy-dates calendar and raise my FLX rates ahead of them, inside my ceiling.
  openclaw:
    emoji: "📈"
---

# Raise rates ahead of busy dates on your calendar

Holidays, big games and festivals fill every car in town. This recipe reads the busy dates
you keep on a Google Calendar, raises each car's rate ahead of them inside the ceiling you
set, and puts the rate back afterwards.

## When to use

- Every morning, on the bundled schedule.
- When you ask: "Get ready for the holiday weekend."

## Prerequisites

- FLX connected to your agent, with **read** and **listings** access.
- Google Calendar connected in **your agent**, and the calendar of busy dates.
- Your ceiling for each car, and how much to raise, in your instruction to the agent.

## Procedure

1. Read the busy dates in the next 14 days.
2. Call `read_vehicles` for each car's current rates.
3. Ahead of a busy date, call `listings_update_pricing` with `dry_run=true`, check the change
   stays inside the host's ceiling, then save it. Remember the rates you replaced.
4. The day after it ends, put back the rates you replaced.
5. Tell the host what changed, in one line per car.

## Pitfalls

- **A car has one rate at a time.** While it's raised, every trip requested prices at the
  raised rate, even for other dates. Keep the window short.
- Never go above the host's ceiling. If there isn't one, ask before changing anything.
- Put back only rates this recipe changed.

## Verification

Every change traces to a busy date on the calendar, and each is back to the replaced rate
after it.

## Example

> **Thanksgiving week is busy.** Raised the Civic from $295 to $340 a week (your ceiling is
> $350) and the Niro from $320 to $360. I'll put both back on Dec 1.

## Adapt this

- Change how far ahead it raises, or by how much.
- Leave some cars out.
