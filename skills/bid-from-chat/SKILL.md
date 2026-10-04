---
name: bid-from-chat
description: Use when an open call fits one of the host's idle cars and the host wants it sent to their chat
  app (WhatsApp or Telegram) with a bid drafted, or on a short scheduled check of the open-call board.
version: 1.0.0
author: FLX Rides
license: MIT
metadata:
  flx:
    title: Bid on open calls from your chat
    why: "Open calls are drivers saying what they need so hosts can bid. When one fits an idle car, a bid lands in your chat, drafted and ready for your yes."
    elsewhere: "Sends each drafted bid to the chat you pick."
    never:
    - "It never places a bid without your yes in FLX."
    - "It never bids below your lowest price. It tells you instead."
    - "It bids once on each call."
    mode: waits
    preview:
      title: "An open call fits the Niro"
      body: "A hybrid for 4 weeks from Monday. Bid drafted at $320 a week."
      ask: "Approve it in FLX"
      reply: "Approved"
      done: "Your bid is on the board. I’ll tell you if the driver picks it."
    tools: [read_open_calls, read_vehicles, trips_place_bid]
    scopes: [read, trips]
    tier: 4
    events: []
    schedule: "*/15 * * * *"
    unattended: true
    apps: [whatsapp, telegram]
    jobs: [find-drivers, fill-empty-days]
    flow: "When an open call fits an idle car → sends it to your chat with a bid drafted → you approve it in FLX, and it's placed"
    waits: Each bid waits for you
    agents: [openclaw, hermes]
  hermes:
    tags: [flx, open-calls, chat]
    related_skills: [open-call-bidder, brief-in-chat]
    blueprint:
      schedule: "*/15 * * * *"
      prompt: Check the FLX open-call board for calls that fit an idle car, draft a bid, and send it to me here.
  openclaw:
    emoji: "📣"
---

# Bid on open calls from your chat

An open call is a driver saying what they need, so matching hosts can bid. This recipe
watches the board, drafts a bid when a call fits one of your idle cars, and sends it to your
chat. The bid is placed only after you approve it in FLX.

## When to use

- Every 15 minutes, on the bundled schedule.
- When you ask: "Anything worth bidding on?"

## Prerequisites

- FLX connected to your agent, with **read** and **trips** access.
- WhatsApp or Telegram connected in **your agent**. FLX never holds that login.
- Your lowest weekly price for each car, in your instruction to the agent.

## Procedure

1. Call `read_open_calls` for the live calls your cars can serve.
2. Call `read_vehicles` and keep the cars that are idle over each call's dates.
3. For the best fit, price the bid at or above the host's lowest weekly price.
4. Call `trips_place_bid`. FLX stages it for the host's approval.
5. Send the host the call, the car, your price and a link to approve it:
   `https://flxrides.com/host/agents`.

## Pitfalls

- **Every bid waits for the host.** A picked bid becomes a trip at that price, so FLX never
  places one without their yes.
- Never bid below the host's lowest price. If a call only works lower, tell them instead.
- One bid per call. If yours is already on the board, say so.

## Verification

Every bid names a call from `read_open_calls` and a car idle over its dates.

## Example

> **An open call fits the Niro.** A hybrid for 4 weeks from Monday, self-pickup.
> Bid drafted at $320 a week (your floor is $300). Approve it in FLX: flxrides.com/host/agents

## Adapt this

- Change how often it checks, or which cars it may bid with.
- Set a different lowest price per car.
