---
name: own-use-days
description: Use when the host keeps a Google Calendar of days a car is off the market for them (service,
  repairs, personal use) and wants those days blocked on FLX, or on the scheduled daily check.
version: 1.0.0
author: FLX Rides
license: MIT
metadata:
  flx:
    title: Block the days you need a car yourself
    why: "An oil change or a weekend away shouldn’t cancel a trip. Put the days on your Google Calendar, and they’re blocked on FLX before a driver can ask."
    elsewhere: "Reads the days you mark. It never edits your calendar."
    never:
    - "It never guesses the car. An event that doesn’t name one is skipped."
    - "It only opens dates it blocked."
    mode: runs
    preview:
      title: "Blocked for you"
      body: "Civic, Oct 14–15, from “Civic: brakes” on your calendar."
    tools: [read_vehicles, listings_block_dates, listings_unblock_dates]
    scopes: [read, listings]
    tier: 2
    events: []
    schedule: "0 7 * * *"
    unattended: true
    apps: [google-calendar]
    jobs: [keep-calendars-in-sync]
    flow: "Every morning → reads service and personal days on your calendar → blocks them on FLX, and opens them again if you delete the event"
    waits: Runs on its own, and every block can be undone
    agents: [openclaw, hermes, claude, grok]
  hermes:
    tags: [flx, calendar, listings]
    related_skills: [calendar-sync]
    blueprint:
      schedule: "0 7 * * *"
      prompt: Block the service and personal days on my calendar for my FLX cars.
  openclaw:
    emoji: "🔧"
---

# Block the days you need a car yourself

An oil change or a weekend away shouldn't turn into a cancelled trip. Put the days on your
own Google Calendar; this recipe blocks them on FLX before a driver can ask for them.

## When to use

- Every morning, on the bundled schedule.
- When you ask: "Block my service days."

## Prerequisites

- FLX connected to your agent, with **read** and **listings** access.
- Google Calendar connected in **your agent**. Put the car's name in each event's title.

## Procedure

1. Call `read_vehicles` and match each event to a car by the name in its title.
2. Read the calendar for the next 60 days.
3. For each event not yet blocked, call `listings_block_dates`. Remember the `date_id` with
   the event it came from.
4. When an event you blocked is deleted, call `listings_unblock_dates` with its `date_id`.
5. Tell the host what changed. Say nothing when nothing did.

## Pitfalls

- An event that doesn't name a car is skipped; tell the host once.
- Only unblock dates this recipe blocked.

## Verification

Every block traces to an event on the host's calendar.

## Example

> **Blocked Oct 14–15 for the Civic** ("Civic: brakes"). Nothing else changed.

## Adapt this

- Use a different word to mark events, or a separate calendar per car.
- Add a buffer day after each service.
