---
name: team-alerts-slack
description: Use when the host runs their cars with a co-host or small team in Slack and wants new trip
  requests, driver messages and bid outcomes posted to a channel, or on a short scheduled check.
version: 1.0.0
author: FLX Rides
license: MIT
metadata:
  flx:
    title: Keep your team posted in Slack
    why: "When more than one person runs the cars, everyone sees the same thing. What happens on FLX is posted to your team’s channel, so whoever is free picks it up."
    elsewhere: "Posts to the one channel you pick."
    never:
    - "It changes nothing on FLX. Replies happen in FLX."
    - "It keeps driver messages short: a first name and the first line."
    mode: reads
    preview:
      title: "#fleet"
      body: "New request: Devon wants the Civic, Oct 10–24. Maya asked about Friday."
    tools: [read_events]
    scopes: [read]
    tier: 1
    events: []
    schedule: "*/5 * * * *"
    unattended: true
    apps: [slack]
    jobs: [hear-about-it, answer-faster]
    flow: "When something happens on FLX → posts it to your team's channel: new requests, driver messages, bids won and payouts"
    waits: Reads only
    agents: [openclaw, hermes]
  hermes:
    tags: [flx, team, slack]
    related_skills: [brief-in-chat]
    blueprint:
      schedule: "*/5 * * * *"
      prompt: Post new FLX requests, driver messages, bid outcomes and payouts to our team channel.
  openclaw:
    emoji: "📢"
---

# Keep your team posted in Slack

When more than one person runs the cars, everyone needs to see the same thing. This recipe
posts what happens on FLX to your team's channel, so whoever is free can pick it up.

## When to use

- Every 5 minutes, on the bundled schedule.

## Prerequisites

- FLX connected to your agent, with **read** access. Nothing else.
- Slack connected in **your agent**, and the channel to post to.

## Procedure

1. Call `read_events` with your saved cursor; save the new `next_cursor`.
2. Post one short line per event: new trip requests, driver messages, bid outcomes and
   payouts, each with a link to FLX.

## Pitfalls

- **Reads only.** Answering happens in FLX, or with `messages-in-chat`.
- Keep driver messages short in the channel: the driver's first name and the first line.

## Verification

Every post traces to one event from `read_events`.

## Example

> 🚗 New request: Devon wants the Civic, Oct 10–24. · 💬 Maya asked about Friday pickup.
> · ✅ Your bid on the 4-week hybrid call was picked.

## Adapt this

- Choose which events to post, or split them across channels.
