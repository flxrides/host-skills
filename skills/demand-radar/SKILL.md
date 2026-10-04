---
name: demand-radar
description: Use when the host asks which open calls fit one of their idle cars, or on a short scheduled
  scan of the open-call board; it reports the best matches and never bids.
version: 1.1.0
author: FLX Rides
license: MIT
platforms:
- macos
- linux
metadata:
  flx:
    title: Hear about open calls that fit an idle car
    why: "Drivers post open calls every day. This tells you which ones fit an idle car, so you bid on the right ones yourself."
    never:
    - "It never bids, messages a driver or changes anything."
    - "It suggests. Whether to bid is always your call."
    mode: reads
    preview:
      title: "3 open calls fit an idle car"
      body: "An SUV in Denver for 2 weeks fits your 4Runner. 2 hosts are bidding."
    waits: Reads only
    tools:
    - read_open_calls
    - read_attention
    scopes:
    - read
    tier: 1
    events: []
    schedule: '*/10 * * * *'
    unattended: true
    apps:
    - flx
    jobs:
    - find-drivers
    flow: Every 10 minutes → reads the open calls that fit an idle car → tells you the best ones (reads
      only)
    agents:
    - openclaw
    - hermes
    - claude
    - grok
  hermes:
    tags:
    - flx
    - open-calls
    - demand
    related_skills:
    - open-call-bidder
    - flx-daily-brief
    - host-autopilot
    blueprint:
      schedule: '*/10 * * * *'
      prompt: Scan the FLX open-call board for live calls that fit one of my idle cars and tell me the
        best ones — read-only, don't bid.
  openclaw:
    emoji: 📡
---

# Demand Radar

An open call is a driver posting what they need so matching hosts can bid. This skill
watches the board and flags the live calls that fit an idle car — so you catch the ones
worth bidding on, and bid them yourself. It reads only; it never bids.

## When to use

- On a short loop — the bundled schedule scans the board every 10 minutes.
- When you want to keep bidding in your own hands but not miss a fitting call.
- Don't use it to place bids — pair it with `open-call-bidder` if you want the bid placed
  automatically.

## Prerequisites

- FLX connected to your agent, granting **read** access. Nothing else.

## Procedure

1. Call `read_open_calls` for the live board.
2. Call `read_attention` and read the fleet to see which cars are idle over the call's
   dates.
3. Match each call against the fleet: does an idle car fit the class and the dates? Rank
   the fits by how well the car and window line up.
4. Report the top fits — the call, the car it fits, the dates, and how many hosts are
   already bidding — so the host can decide. Do not place a bid.

## Pitfalls

- **Read-only by design.** This skill can't bid, message a driver, or change anything —
  it only surfaces demand. Access is enforced on FLX's servers.
- A fit is a suggestion, not a commitment. The host bids (or doesn't) by hand.

## Verification

Every flagged call traces to a row from `read_open_calls`, and each names a real idle car
from the fleet over the call's dates.

## Example

> **3 open calls fit an idle car right now.**
> 1. **SUV, Denver, 2 weeks** — fits your 4Runner (idle Mon–Sun). 2 hosts bidding.
> 2. **Sedan, airport, 6 days** — fits your Camry (idle all week). No bids yet.
> 3. **7-seater, 10 days** — fits your Sienna. 4 hosts bidding — move fast if you want it.
> You're not bidding on any. Say the word and I'll hand one to the bidder.

## Adapt this

- Change how often it scans, or ask it to only report calls within a distance you name.
- Tell it which of your cars to match, or to skip cars already booked this week.
