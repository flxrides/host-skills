---
name: open-call-bidder
description: Use when an open call fits one of the host's listed cars and the host wants a standing bid
  drafted for their yes, or on a short scheduled check of the open-call board.
version: 1.1.0
author: FLX Rides
license: MIT
platforms:
- macos
- linux
metadata:
  flx:
    tools:
    - read_open_calls
    - trips_place_bid
    - trips_withdraw_bid
    scopes:
    - read
    - trips
    tier: 4
    events: []
    schedule: '*/5 * * * *'
    unattended: true
    apps:
    - flx
    jobs:
    - find-drivers
    flow: Every 5 minutes → finds open calls that fit a car → drafts your standing bid (each bid waits
      for you)
    agents:
    - openclaw
    - hermes
    - claude
    - grok
  hermes:
    tags:
    - flx
    - open-calls
    - bidding
    related_skills:
    - trip-concierge
    - fleet-repricer
    - host-autopilot
    blueprint:
      schedule: '*/5 * * * *'
      prompt: Check the FLX open-call board and draft my standing bid on calls that fit a car, for my
        approval.
  openclaw:
    emoji: 📣
---

# Open-Call Bidder

An open call is a driver posting what they need so matching hosts can bid. This skill
watches the board and drafts your standing bid on calls that fit one of your cars, so
it's ready for your approval before another host gets there. A driver picking your bid
books the trip, so every bid waits for you.

## When to use

- On a short loop — the bundled schedule checks the board every 5 minutes.
- Speed matters: a good call gets several bids within minutes.
- Don't use it on calls outside your rules — leave those for you.

## Prerequisites

- FLX connected, granting **read** and **trips** access.
- Your bidding rules in FLX: eligible cars, weekly price (or formula) per class, dates
  you'll take, and a floor below which not to bid.

## Procedure

1. Call `read_open_calls` for the live board.
2. For each call, check it against your rules: does a car fit the class and dates, and is
   your standing number at or above your floor? Skip anything that isn't a clean fit.
3. Call `trips_place_bid` with the vehicle, the call, and the weekly price as a dollar
   string. Use `dry_run: true` first to validate and preview. The real call comes back as
   a proposal: it waits for the host's approval and expires with the call. The price is
   binding once placed, so draft only what the host would honor.
4. If your terms change or a car becomes unavailable, call `trips_withdraw_bid` to pull a
   live bid.
5. Summarize the bids drafted (car, call, price, when the call closes) and near-misses
   worth the host's eye.

## Pitfalls

- **A bid's price is binding.** Never bid a number you wouldn't honor.
- A driver picking a bid books the trip. That's why every bid waits for the host; this
  skill never messages the driver or moves money.

## Verification

Every bid drafted matches an eligible car, sits at or above the floor, and was previewed
with `dry_run` first.

## Example

> **Bid ready for you.** Open call: SUV, Denver, 2 weeks. Fit: your 4Runner. Drafted your
> standing $560/wk (floor $520); 3 other hosts are in. Approve it in your Inbox before the
> call closes at 6pm.

## Adapt this

- Tell it your standing price rules ("bid my rate, never under $250 a week").
- Change how often it checks the board, or limit it to some of your cars.
- Every bid still waits for your yes in FLX.
