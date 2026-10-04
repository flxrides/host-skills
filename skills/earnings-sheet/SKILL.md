---
name: earnings-sheet
description: Use when the host keeps a Google Sheet of their car business and wants each car's weekly
  earnings and idle days added to it, or on the scheduled Monday update.
version: 1.0.0
author: FLX Rides
license: MIT
metadata:
  flx:
    title: Keep a weekly earnings sheet per car
    why: "See which car pays for itself. Every Monday, each car gets a row in your own Google Sheet: what it earned, its idle days, and what reached your bank."
    elsewhere: "Adds a row per car each week to your own sheet."
    never:
    - "It changes nothing on FLX."
    - "It never rounds or recomputes an amount."
    - "It adds rows and never touches your own columns."
    mode: reads
    preview:
      title: "Sheet updated for Sep 22–28"
      body: "Civic $610, no idle days. Niro $140, idle 6 days."
    tools: [read_vehicles, read_vehicle, read_finances]
    scopes: [read]
    tier: 1
    events: []
    schedule: "0 8 * * 1"
    unattended: true
    apps: [google-sheets]
    jobs: [track-the-money]
    flow: "Every Monday → reads each car's earnings and idle days, and your payouts → adds last week's row for each car to your sheet"
    waits: Reads only on FLX
    agents: [openclaw, hermes, claude, grok]
  hermes:
    tags: [flx, money, sheets]
    related_skills: [flx-daily-brief]
    blueprint:
      schedule: "0 8 * * 1"
      prompt: Add last week's FLX earnings and idle days for each car to my sheet.
  openclaw:
    emoji: "📊"
---

# Keep a weekly earnings sheet per car

Which car pays for itself? This recipe adds a row per car to your own Google Sheet every
Monday: what it earned, how many days it sat idle, and what reached your bank. Your sheet,
your formulas; FLX only supplies the figures.

## When to use

- Every Monday, on the bundled schedule.
- When you ask: "Update my earnings sheet."

## Prerequisites

- FLX connected to your agent, with **read** access. Nothing else.
- Google Sheets connected in **your agent**, and the sheet to write to.

## Procedure

1. Call `read_vehicles` for the fleet.
2. For each car, call `read_vehicle` and read its earnings and idle days.
3. Call `read_finances` for payouts over the period.
4. Add one row per car: the week, the car, earnings, idle days. Add one row for payouts.
   Use each amount's `display` value exactly as FLX returns it.
5. Tell the host the sheet is updated, with the best and the quietest car.

## Pitfalls

- **Reads only on FLX.** It changes nothing in FLX.
- Never recompute or round an amount. Copy what FLX returns.
- Add rows; never overwrite the host's own columns.

## Verification

Every figure in the new rows matches `read_vehicle` or `read_finances` for that week.

## Example

> **Sheet updated for Sep 22–28.** Best: the Civic, $610 and no idle days. Quietest: the Niro,
> idle 6 days. $1,140 reached your bank.

## Adapt this

- Change the day, or add a monthly summary tab.
- Name the columns you want, in your order.
