---
name: inbox-autopilot
description: Use when a driver has an unanswered question in a thread the host is already in, or on a
  scheduled sweep of the host's conversations, to answer in the host's voice.
version: 1.1.0
author: FLX Rides
license: MIT
platforms:
- macos
- linux
metadata:
  flx:
    title: Answer driver questions in your voice
    why: "Drivers get an answer in your voice the moment they ask: late pickups, extensions, where to park."
    never:
    - "It never starts a conversation. The driver writes first."
    - "It can’t change a price or a trip, or move money."
    - "When it isn’t sure, it asks you instead of guessing."
    mode: runs
    preview:
      title: "Answered Maya"
      body: "“Park in bay 4. The code is on your trip page.” Sent in her trip thread."
    waits: Replies in threads you are already in
    tools:
    - read_conversations
    - read_conversation
    - messaging_reply
    scopes:
    - read
    - messaging
    tier: 2
    events: []
    schedule: '*/15 * * * *'
    unattended: true
    apps:
    - flx
    jobs:
    - answer-faster
    flow: Every 15 minutes → reads unanswered driver questions → replies in your voice, in threads you're
      already in
    agents:
    - openclaw
    - hermes
    - claude
    - grok
  hermes:
    tags:
    - flx
    - messaging
    - inbox
    related_skills:
    - trip-concierge
    - flx-daily-brief
    - host-autopilot
    blueprint:
      schedule: '*/15 * * * *'
      prompt: Sweep my FLX conversations and answer any unanswered driver questions in my voice.
  openclaw:
    emoji: 💬
---

# Inbox Autopilot

Answers driver questions in your voice the moment they land — late pickups, extensions,
where to park. Only in conversations you're already in; starting a new thread is always
the driver's move.

## When to use

- When a new message arrives — wire it to your agent's new-message trigger.
- As a sweep — the bundled schedule clears unanswered threads every 15 minutes.
- Don't use it to decide anything you haven't delegated (a discount, an exception,
  anything about money) — hand those back.

## Prerequisites

- FLX connected, granting **read** and **messaging** access.

## Procedure

1. Call `read_conversations` to find threads with an unanswered driver message.
2. For each, call `read_conversation` for the full thread and the trip context (dates,
   car, pickup, the host's rules).
3. Draft a reply in the host's voice — direct, specific to their rules (pickup window,
   extension policy, parking). If it needs a decision the host hasn't delegated, don't
   answer it — flag it for the host.
4. Call `messaging_reply` with the thread and the message. It's delivered exactly like
   the host typed it. The server refuses any thread the host isn't a participant in.

## Pitfalls

- **First contact is the driver's, not yours.** This skill can only reply in existing
  threads; it can't open a new conversation.
- It can't change a price or a trip, or move money.
- When unsure, hand back — never guess a policy answer.

## Verification

Each reply landed in a thread the host already participates in, and no message asserted a
decision outside the host's rules.

## Example

> Driver: "Running 30 min late for the 4pm pickup, that ok?"
> Reply sent: "No problem, Sam — the 4Runner's held for you, come by when you land. It's
> in spot 12; text me at the gate. Have a good trip!"

## Adapt this

- Give it your voice: a few replies you've sent, words you use, things you never say.
- Tell it which questions to leave for you ("anything about damage, ask me").
- Change how often it sweeps.
