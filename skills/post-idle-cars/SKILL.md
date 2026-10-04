---
name: post-idle-cars
description: Use when one of the host's cars has sat idle for a few days and the host wants it seen on
  their own social accounts (X, Instagram, a Facebook Page), or on a scheduled morning check for idle cars.
version: 1.0.0
author: FLX Rides
license: MIT
metadata:
  flx:
    title: Post a car when it sits idle
    why: "An idle car earns nothing. When one sits for a few days, a post with its photo, weekly price and link is drafted for your own accounts."
    elsewhere: "Posts to your own accounts, in your name, after your yes."
    never:
    - "It never posts without your yes."
    - "It won’t post the same car more than once a week."
    - "It never invents a discount. It quotes your FLX rate."
    mode: waits
    preview:
      title: "Draft for X"
      body: "2022 Niro hybrid, $320 a week in Oakland. Request it at flxrides.com/listings/…"
      ask: "Post it?"
      reply: "Post it"
      done: "Posted to X. Drivers can request the Niro from the link."
    tools: [read_vehicles, read_vehicle]
    scopes: [read]
    tier: 1
    events: []
    schedule: "0 9 * * *"
    unattended: true
    apps: [x, instagram, facebook]
    jobs: [fill-empty-days, find-drivers]
    flow: "Every morning → finds cars idle 3 or more days → drafts a post with the car's photo, weekly price and link → you approve, it posts"
    waits: Each post waits for you
    agents: [openclaw, hermes, claude, grok]
  hermes:
    tags: [flx, social, listings]
    related_skills: [fleet-repricer, demand-radar]
    blueprint:
      schedule: "0 9 * * *"
      prompt: Check my FLX cars for any idle 3 or more days and draft a post for each, for my approval.
  openclaw:
    emoji: "📸"
---

# Post a car when it sits idle

An idle car earns nothing. This recipe notices a car that has sat for a few days and drafts
a post for your own accounts, with the car's photo, weekly price and the link drivers can
request it from. Nothing posts until you say yes.

## When to use

- Every morning, on the bundled schedule.
- When you ask: "Draft posts for my free cars."

## Prerequisites

- FLX connected to your agent, with **read** access. Nothing else on FLX.
- The social account (X, Instagram or a Facebook Page) connected in **your agent**. FLX never
  holds that login.

## Procedure

1. Call `read_vehicles` and keep the listed cars.
2. For each, call `read_vehicle` and read its idle days and weekly rate.
3. Keep cars idle 3 or more days (or the number the host set).
4. For each, draft one post: year, make and model, the weekly rate, where it is, and the
   link `https://flxrides.com/listings/<vehicle id>`. Use the car's `primary_image_url` as the
   photo. Size the draft for each account the host picked.
5. Send the drafts to the host and ask: post, edit, or skip. Post only what they approve.

## Pitfalls

- **Never post without a yes.** The post goes out in the host's name.
- Don't repost the same car more than once a week unless the host asks.
- Quote the weekly rate FLX returns. Never invent a discount.

## Verification

Every draft names a car from `read_vehicles`, and its price and idle days match
`read_vehicle`.

## Example

> **2 cars have sat idle.** Drafts are ready:
> 1. **2022 Kia Niro**, idle 6 days: "Hybrid for gig driving, $320 a week in Oakland.
>    Request it: flxrides.com/listings/…" Post to X and Instagram?
> 2. **2019 Honda Civic**, idle 3 days. Post, edit or skip?

## Adapt this

- Change the idle threshold, or which accounts each car goes to.
- Ask for a different tone, or a line you always include.
