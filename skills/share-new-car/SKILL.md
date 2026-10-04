---
name: share-new-car
description: Use when the host lists a new car on FLX and wants to announce it on their own social accounts
  (X, Instagram, a Facebook Page), or on a scheduled daily check for newly listed cars.
version: 1.0.0
author: FLX Rides
license: MIT
metadata:
  flx:
    title: Announce each new car when it goes live
    why: "A new car’s first week sets its rhythm. When you list one, a launch post with its photo, weekly price and link is drafted for your own accounts."
    elsewhere: "Posts to your own accounts, in your name, after your yes."
    never:
    - "It never posts without your yes."
    - "It never announces a car that’s still a draft."
    mode: waits
    preview:
      title: "Your 2021 Camry is live"
      body: "Launch post drafted with its photo: $295 a week in Fremont."
      ask: "Post it?"
      reply: "Post it"
      done: "Posted to Instagram and Facebook."
    tools: [read_vehicles]
    scopes: [read]
    tier: 1
    events: []
    schedule: "0 10 * * *"
    unattended: true
    apps: [x, instagram, facebook]
    jobs: [fill-empty-days, find-drivers]
    flow: "When a car goes live on FLX → drafts a launch post with its photo, weekly price and link → you approve, it posts"
    waits: Each post waits for you
    agents: [openclaw, hermes, claude, grok]
  hermes:
    tags: [flx, social, listings]
    related_skills: [post-idle-cars]
    blueprint:
      schedule: "0 10 * * *"
      prompt: Check FLX for cars I listed since yesterday and draft a launch post for each, for my approval.
  openclaw:
    emoji: "🎉"
---

# Announce each new car when it goes live

A new car's first week sets its rhythm. This recipe notices when you list a car and drafts a
launch post for your own accounts, with its photo, weekly price and the link drivers request
it from. Nothing posts until you say yes.

## When to use

- Every day, on the bundled schedule.
- When you ask: "Announce my new car."

## Prerequisites

- FLX connected to your agent, with **read** access. Nothing else on FLX.
- The social account (X, Instagram or a Facebook Page) connected in **your agent**.

## Procedure

1. Call `read_vehicles` and keep listed cars created since the last check.
2. For each, draft one launch post: year, make and model, the weekly rate, where it is, and
   `https://flxrides.com/listings/<vehicle id>`. Use `primary_image_url` as the photo.
3. Send the drafts to the host: post, edit or skip. Post only what they approve.

## Pitfalls

- **Never post without a yes.**
- A car still in draft isn't live. Announce only listed cars.

## Verification

Every draft names a listed car from `read_vehicles` created since the last check.

## Example

> **Your 2021 Camry is live.** Draft: "Just added: a 2021 Camry, $295 a week in Fremont.
> Request it: flxrides.com/listings/…" Post to Instagram?

## Adapt this

- Choose which accounts each announcement goes to.
- Add a line you always include, like your pickup area.
