---
name: weekly-email
description: Use when the host wants a short weekly summary of their car business in their Gmail inbox,
  or on the scheduled Monday morning send.
version: 1.0.0
author: FLX Rides
license: MIT
metadata:
  flx:
    title: Get a weekly business summary by email
    why: "Once a week, the whole business in one email: what each car earned, which sat idle, what reached your bank, and what needs you."
    elsewhere: "Sends one email a week, to you."
    never:
    - "It changes nothing on FLX."
    - "It never recomputes a total."
    mode: reads
    preview:
      title: "Your week, Sep 22–28"
      body: "$1,140 reached your bank. Best: the Civic. Quietest: the Niro, idle 6 days."
    tools: [read_finances, read_vehicles, read_vehicle, read_attention]
    scopes: [read]
    tier: 1
    events: []
    schedule: "0 8 * * 1"
    unattended: true
    apps: [gmail]
    jobs: [track-the-money, hear-about-it]
    flow: "Every Monday → reads last week's earnings, idle days, payouts and what needs you → emails you one short summary"
    waits: Reads only
    agents: [openclaw, hermes, claude, grok]
  hermes:
    tags: [flx, money, email]
    related_skills: [earnings-sheet, flx-daily-brief]
    blueprint:
      schedule: "0 8 * * 1"
      prompt: Email me last week's FLX summary, earnings per car, idle days, payouts and what needs me.
  openclaw:
    emoji: "📬"
---

# Get a weekly business summary by email

Once a week, the whole business in one email: what each car earned, which sat idle, what
reached your bank, and what needs you this week.

## When to use

- Every Monday, on the bundled schedule.
- When you ask: "Send me this week's summary."

## Prerequisites

- FLX connected to your agent, with **read** access. Nothing else.
- Gmail connected in **your agent**, and the address to send to.

## Procedure

1. Call `read_vehicles`, then `read_vehicle` for each car's earnings and idle days.
2. Call `read_finances` with `period_days=7` for payouts.
3. Call `read_attention` for what needs the host.
4. Email one short summary: the best and quietest car, each car's earnings, payouts, then
   what needs the host, with a link to `https://flxrides.com/host/dashboard`.

## Pitfalls

- **Reads only.** It changes nothing in FLX.
- Use each amount's `display` value exactly as FLX returns it. Never recompute a total.

## Verification

Every figure matches `read_vehicle` or `read_finances` for the week.

## Example

> **Your week, Sep 22–28.** Civic $610 (no idle days). Niro $140 (idle 6 days). $1,140 reached
> your bank. This week: one review due, and Maya's early pickup Friday.

## Adapt this

- Change the day, or send it to your co-host too.
- Leave money out, or add last month for comparison.
