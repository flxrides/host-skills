---
name: calendar-sync
description: Use when the host lists a car on another car-sharing app or keeps a Google Calendar for it and
  wants those dates blocked on FLX, or on a scheduled hourly check of those calendars.
version: 1.0.0
author: FLX Rides
license: MIT
metadata:
  flx:
    title: Block FLX dates when a car is taken elsewhere
    why: "If a car is also listed on another app, a trip there has to close those dates on FLX. This keeps FLX in step, so two drivers never ask for the same week."
    elsewhere: "Reads your other calendars. It never writes to them."
    never:
    - "It never puts FLX trips on your other calendars. It syncs one way, into FLX."
    - "It only opens dates it blocked, never ones you blocked yourself."
    mode: runs
    preview:
      title: "Calendars synced"
      body: "Civic: blocked Oct 18–21, taken on your other app. Niro: nothing new."
    tools: [read_vehicles, listings_block_dates, listings_unblock_dates]
    scopes: [read, listings]
    tier: 2
    events: []
    schedule: "0 * * * *"
    unattended: true
    apps: [calendar-link, google-calendar]
    jobs: [keep-calendars-in-sync]
    flow: "Every hour → reads each car's other calendar → blocks those dates on FLX, and opens them again if the trip is cancelled"
    waits: Runs on its own, and every block can be undone
    agents: [openclaw, hermes, claude, grok]
  hermes:
    tags: [flx, calendar, listings]
    related_skills: [flx-daily-brief]
    blueprint:
      schedule: "0 * * * *"
      prompt: Read my cars' other calendars and block the taken dates on FLX.
  openclaw:
    emoji: "📅"
---

# Block FLX dates when a car is taken elsewhere

If a car is also listed on another app, a trip there has to close those dates on FLX, or
two drivers can ask for the same week. This recipe reads each car's other calendar and keeps
FLX in step, in one direction: into FLX.

## When to use

- Every hour, on the bundled schedule.
- When you ask: "Sync my calendars."

## Prerequisites

- FLX connected to your agent, with **read** and **listings** access.
- For each car, a calendar link (the iCal address other car-sharing apps publish) or a Google
  Calendar connected in your agent. Tell the agent which calendar belongs to which car.

## Procedure

1. Call `read_vehicles` and match each car to the calendar the host named.
2. Read each calendar's events for the next 90 days.
3. For each event not yet blocked, call `listings_block_dates` with the car and the dates.
   Remember the `date_id` it returns, with the event it came from.
4. When an event you blocked is gone (cancelled), call `listings_unblock_dates` with its
   `date_id`.
5. Tell the host what changed, in one line per car. Say nothing when nothing changed.

## Pitfalls

- **One direction only.** This puts other calendars into FLX. It can't show FLX trips on
  other calendars.
- A block that overlaps an existing one is refused. Skip it; the dates are already closed.
- Only unblock dates this recipe blocked. Never touch a block the host made by hand.

## Verification

Every block traces to an event on the car's calendar, and every unblock to a block this
recipe made.

## Example

> **Calendars synced.** Civic: blocked Oct 18–21 (taken on your other app). Niro: nothing new.

## Adapt this

- Change how often it checks, or how far ahead it looks.
- Add a buffer day before and after each outside trip.
