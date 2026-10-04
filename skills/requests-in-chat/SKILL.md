---
name: requests-in-chat
description: Use when a driver asks for one of the host's cars and the host wants to hear about it, and
  answer it, from their chat app (WhatsApp or Telegram), or on a short scheduled check for new requests.
version: 1.0.0
author: FLX Rides
license: MIT
metadata:
  flx:
    title: Answer trip requests from your chat
    why: "A driver who waits hours for an answer goes elsewhere. Each trip request reaches the chat you already read, and your reply answers it in FLX."
    elsewhere: "Sends each trip request to the chat you pick, and takes your reply."
    never:
    - "It never accepts a trip from chat. Accepting waits for your yes in FLX."
    - "It never answers a request you haven’t replied to."
    mode: waits
    preview:
      title: "New trip request"
      body: "Devon wants the Civic, Oct 10–24. It’s out until Oct 12, so I’d propose Oct 13–27."
      ask: "Yes, new dates or no?"
      reply: "New dates"
      done: "Proposed Oct 13–27 to Devon in FLX."
    tools: [read_trip_requests, read_policy, trips_respond]
    scopes: [read, trips]
    tier: 3
    events: []
    schedule: "*/10 * * * *"
    unattended: true
    apps: [whatsapp, telegram]
    jobs: [answer-faster, fill-empty-days]
    flow: "When a driver asks for a car → sends you the request with what it would do → you reply yes, new dates or no, and it answers in FLX"
    waits: Accepting a trip waits for you
    agents: [openclaw, hermes]
  hermes:
    tags: [flx, trips, chat]
    related_skills: [trip-concierge, messages-in-chat]
    blueprint:
      schedule: "*/10 * * * *"
      prompt: Check FLX for new trip requests and send each one to me here with what you'd do.
  openclaw:
    emoji: "🚗"
---

# Answer trip requests from your chat

A driver who waits hours for an answer books somewhere else. This recipe sends you each new
trip request in the chat you already read, with what it would do, and answers in FLX the
moment you reply.

## When to use

- Every 10 minutes, on the bundled schedule.
- When you ask in chat: "Any new requests?"

## Prerequisites

- FLX connected to your agent, with **read** and **trips** access.
- WhatsApp or Telegram connected in **your agent**. FLX never holds that login.

## Procedure

1. Call `read_trip_requests` and keep the ones where `your_turn` is true.
2. Call `read_policy` for the host's trip rules.
3. For each request, send the host: the driver, the car, the dates, and your suggestion
   (accept, propose the nearest dates that work, or decline because it breaks a named rule).
4. Act on the host's reply with `trips_respond`: `propose_dates` or `decline` run directly;
   `accept` is staged for the host's approval in FLX. Send them the link:
   `https://flxrides.com/host/agents`.

## Pitfalls

- **Accepting always waits for the host's yes in FLX.** A reply in chat stages it; it does
  not accept it.
- Decline only a request that breaks one of the host's rules, and say which one.
- Never answer a request the host hasn't replied to.

## Verification

Every answer traces to a request from `read_trip_requests` and to the host's own reply.

## Example

> **New request: Devon wants the Civic, Oct 10–24.** The Civic is out until Oct 12.
> I'd propose Oct 13–27. Reply **yes**, **new dates**, or **no**.

## Adapt this

- Change how often it checks, or which chat it uses.
- Ask it to propose dates on its own when a request only clashes by a day or two.
