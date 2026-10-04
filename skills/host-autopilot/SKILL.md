---
name: host-autopilot
description: Use when the host wants the whole FLX routine run as one crew — brief, reprice, answer drivers,
  bid, run trips and file fees — on a short schedule, by their rules.
version: 1.1.0
author: FLX Rides
license: MIT
platforms:
- macos
- linux
metadata:
  flx:
    title: Run the whole crew as one
    why: "The brief, the repricer, the inbox, the bidder and trip requests, run as one crew. Accepting a trip, bids and anything with money still wait for you."
    never:
    - "It never accepts a trip, places a bid or moves money without your yes."
    - "It never reprices a car during a trip, or bids a car that’s already out."
    mode: waits
    preview:
      title: "Today so far"
      body: "Repriced 2 cars, answered 3 driver questions and drafted 1 bid."
      ask: "1 bid waits for you"
      reply: "Approved"
      done: "Bid placed. Next check in 15 minutes."
    waits: Accepting a trip, bids and anything with money wait for you
    tools:
    - read_attention
    - read_trip_requests
    - read_proposals
    - read_conversations
    - read_conversation
    - read_open_calls
    - read_policy
    - listings_update_pricing
    - messaging_reply
    - trips_place_bid
    - trips_withdraw_bid
    - trips_respond
    - trips_screening_decision
    - trips_record_dropoff
    - money_file_fee
    - money_withdraw_proposal
    scopes:
    - read
    - listings
    - messaging
    - trips
    - money
    tier: 4
    events: []
    schedule: '*/15 * * * *'
    unattended: true
    apps:
    - flx
    jobs:
    - find-drivers
    - fill-empty-days
    - answer-faster
    - track-the-money
    flow: Every 15 minutes → runs the brief, repricer, inbox, bidder and trip requests together → you approve accepting a trip, every bid and anything with money
    agents:
    - openclaw
    - hermes
    - claude
    - grok
    bundle_of:
    - flx-daily-brief
    - fleet-repricer
    - inbox-autopilot
    - open-call-bidder
    - trip-concierge
    - mileage-fee-filer
  hermes:
    tags:
    - flx
    - autopilot
    - fleet
    related_skills:
    - flx-daily-brief
    - fleet-repricer
    - inbox-autopilot
    - open-call-bidder
    - trip-concierge
    - mileage-fee-filer
    blueprint:
      schedule: '*/15 * * * *'
      prompt: Run the FLX host autopilot — brief, reprice, answer drivers, bid, run trips, and file mileage
        fees, deconflicted, by my rules.
  openclaw:
    emoji: 🚗
---

# Host Autopilot — the whole crew, one install

The morning brief, repricer, inbox, open-call bidder, trip concierge, and mileage fee
filer, running as one coordinated crew instead of six separate skills. This is the closest
a host gets to fully hands-off, with the same fence on every part: anything with money,
accepting a trip, and every bid wait for the host's approval.

Install this instead of the six individually; the host can still turn any part off, or
install the pieces one at a time from the same FLX skills library.

## When to use

- When the host wants the full crew rather than picking skills one by one.
- The bundled schedule runs the crew on a short loop; the money and event steps also fire
  on their triggers.
- Grant only the access the host wants live — a part whose access isn't granted simply
  doesn't run.

## Prerequisites

- FLX connected, granting the access the host wants: **read**, and any of **listings**,
  **messaging**, **trips**, **money**.

## Procedure

Run the parts in order each cycle, and never let two fight over the same trip:

1. **Brief** — `read_attention`, `read_trip_requests`, `read_proposals`; surface the few
   things that need the host. (See `flx-daily-brief`.)
2. **Reprice** — `listings_update_pricing`, inside the host's floor and ceiling. (See
   `fleet-repricer`.)
3. **Answer** — `messaging_reply` in existing threads only. (See `inbox-autopilot`.)
4. **Bid** — draft bids with `trips_place_bid` within the host's rules and floor; each
   waits for the host's approval. (See `open-call-bidder`.)
5. **Run requests** — `trips_respond` (declines and new dates by the host's rules; an
   accept goes to the host for approval), `trips_screening_decision`,
   `trips_record_dropoff`. (See `trip-concierge`.)
6. **File the mileage fee** — `money_file_fee` with `kind: mileage_overage`, staged for the host's approval.
   (See `mileage-fee-filer`.)

## Pitfalls

- **Deconflict.** Never bid a car already committed to a trip in the same window, never
  reprice a car mid-trip, never both recommend accepting a request and draft a bid for its car. The committed
  trip wins.
- **Money, accepting a trip and bids wait for the host's approval**, always. Access
  is enforced per call on FLX's servers; the host can pause the whole crew or revoke any
  part in one click.

## Verification

Each part obeyed its own fence, no two parts acted on the same car in one cycle, and every
money action, accept and bid is staged for the host.

## Adapt this

- Turn a part of the crew off by telling it ("don't reprice this month").
- Adapt each part the way its own skill says; the rules in FLX still decide.
