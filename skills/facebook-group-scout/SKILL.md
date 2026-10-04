---
name: facebook-group-scout
description: Use when the host is a member of local driver groups on Facebook and wants to know when
  someone there is asking for a car one of their idle cars could fill.
version: 1.0.0
author: FLX Rides
license: MIT
metadata:
  flx:
    title: Spot drivers asking for a car in your Facebook groups
    why: "Drivers ask for cars in local groups every day. This spots the posts an idle car could fill and drafts your reply. You post it yourself."
    elsewhere: "Reads posts in groups you’re in. It never posts."
    never:
    - "It never posts, comments or messages in a group."
    - "It only reads groups you’re already in."
    - "It never contacts a driver directly."
    mode: waits
    preview:
      title: "A driver needs a car"
      body: "Sam in Oakland Rideshare Drivers needs one from Monday. Reply drafted with your Niro."
      ask: "You post it"
      reply: "Posted it"
    tools: [read_vehicles, read_vehicle]
    scopes: [read]
    tier: 1
    events: []
    schedule: "0 */4 * * *"
    unattended: true
    apps: [facebook]
    jobs: [find-drivers]
    flow: "A few times a day → reads new posts in groups you're in → drafts a reply with a free car → you post it yourself"
    waits: You post every reply yourself
    care: Facebook limits automated activity in groups. This recipe only reads groups you're in and never posts for you.
    agents: [openclaw, hermes]
  hermes:
    tags: [flx, facebook, drivers]
    related_skills: [post-idle-cars]
    blueprint:
      schedule: "0 */4 * * *"
      prompt: Look through my Facebook groups for drivers asking for a car and draft replies with my idle cars.
  openclaw:
    emoji: "👥"
---

# Spot drivers asking for a car in your Facebook groups

Drivers ask for cars in local groups every day. This recipe reads the groups you're already
in, spots the posts one of your idle cars could fill, and drafts a reply for you. You post it
yourself.

## When to use

- A few times a day, on the bundled schedule.
- When you ask: "Anyone looking for a car in my groups?"

## Prerequisites

- FLX connected to your agent, with **read** access. Nothing else.
- Your agent able to open Facebook as you, and the groups to watch.

## Procedure

1. Read new posts in the host's chosen groups since the last check.
2. Keep posts from drivers asking for a car.
3. Call `read_vehicles`, then `read_vehicle` for idle cars, and match each post to a car
   that fits its dates and needs.
4. Draft a short, friendly reply: the car, the weekly rate and
   `https://flxrides.com/listings/<vehicle id>`.
5. Send the host each post with its draft. They post it themselves.

## Pitfalls

- **Never post, comment or message in a group.** Facebook limits automated activity; the host
  posts every reply.
- Read only groups the host is in, and only when they asked you to.
- Don't contact the driver directly.

## Verification

Every draft answers a real post from a watched group and names a car idle over its dates.

## Example

> **1 driver in "Oakland Rideshare Drivers" needs a car** from Monday, 4 weeks.
> Draft: "Hi Sam, my 2022 Niro hybrid is free from Monday, $320 a week:
> flxrides.com/listings/…" Post it from your account.

## Adapt this

- Change which groups it reads, or how often.
- Ask it to skip posts older than a day.
