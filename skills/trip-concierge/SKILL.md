---
name: trip-concierge
description: Use when a trip request needs its next step — a response, a screening decision or a drop-off
  record — by rules the host wrote, or on a scheduled sweep of their trips.
version: 1.1.0
author: FLX Rides
license: MIT
platforms:
- macos
- linux
metadata:
  flx:
    title: Answer trip requests by your rules
    why: "Every trip request is checked against the rules you wrote. The ones that fit come to you with a reason to accept; the rest are declined or get new dates."
    never:
    - "It never accepts a trip. It recommends, with a reason, and you decide."
    - "It can’t reprice a car, message a driver outside the trip, or move money."
    mode: waits
    preview:
      title: "Devon’s request"
      body: "The Civic is out until Oct 12, so I proposed Oct 13–27. Devon has been told."
    waits: Accepting a trip waits for you
    tools:
    - read_trip_requests
    - read_policy
    - trips_respond
    - trips_screening_decision
    - trips_record_dropoff
    scopes:
    - read
    - trips
    tier: 4
    events: []
    schedule: '*/15 * * * *'
    unattended: true
    apps:
    - flx
    jobs:
    - answer-faster
    flow: Every 15 minutes → reads trip requests and your rules → declines what breaks them and proposes dates that work → you approve the ones that fit
    agents:
    - openclaw
    - hermes
    - claude
    - grok
  hermes:
    tags:
    - flx
    - trips
    - requests
    - handoff
    related_skills:
    - inbox-autopilot
    - mileage-fee-filer
    - host-autopilot
    blueprint:
      schedule: '*/15 * * * *'
      prompt: Work my FLX trip requests by my rules — respond, screen, and record drop-offs.
  openclaw:
    emoji: 🔑
---

# Trip Concierge

Every trip is a request, and no one drives without the host's approval. This skill vets
each request against the rules the host wrote: it recommends accepting the ones that fit
(the host approves), declines or counters the rest, makes the screening call, and records
the drop-off when the car comes back.

## When to use

- When a request lands, and at each step of a trip — screening due, car returned.
- As a sweep — the bundled schedule checks every 15 minutes for a step to take.
- Don't decide a request that falls outside the rules — hand it to the host.

## Prerequisites

- FLX connected, granting **read** and **trips** access.
- Your rules in FLX: which requests to accept, when to counter dates, how to weigh a
  screening result, and what a clean drop-off looks like.

## Procedure

1. Call `read_trip_requests` for requests needing a response, and `read_policy` for the
   host's rules.
2. Apply the rules and call `trips_respond` with `action`: `accept`, `decline`, or
   `propose_dates` (with `proposed_start_date` / `proposed_end_date` when countering).
   Declines and new dates take effect directly. An `accept` comes back as a proposal: it
   waits for the host's approval and expires with the request, so recommend early.
3. When a trip is awaiting screening, apply the rule and call `trips_screening_decision`
   with `proceeded` or `declined` — the host's own risk call; the platform never decides
   it.
4. When the car comes back, call `trips_record_dropoff` with the agreed pickup odometer
   and fuel level (an eighths label like `full` or `half`) to complete the handoff.
5. Summarize what it did and anything it handed back.

## Pitfalls

- **Accepting always waits for the host.** Your accept is a recommendation with a reason,
  never the decision; say why it fits their rules.
- This skill can't reprice a car, message a driver off-thread, or move money.

## Verification

Every response, screening call, and drop-off traces to a rule from `read_policy`; nothing
outside the rules was decided.

## Example

> **Request ready for you.** Maya, 4Runner, Fri–Sun. Your rule: accept 2-night SUV
> requests from verified drivers. She fits, so I've recommended accepting; approve it in
> your Inbox before it expires at 11:40.

## Adapt this

- Write your rules in FLX (who you say yes to, notice you need); the skill follows them.
- Tell it which steps to leave for you, or to only record drop-offs.
