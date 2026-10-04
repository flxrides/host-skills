---
name: messages-in-chat
description: Use when a driver messages the host on FLX and the host wants to read and answer it from the
  chat app they already use (WhatsApp, Telegram or Slack), or on a short scheduled check for new messages.
version: 1.0.0
author: FLX Rides
license: MIT
metadata:
  flx:
    title: Answer drivers from your chat
    why: "Drivers message at all hours. Each message reaches your chat with a reply drafted, and your answer goes back in FLX, so you never open another app."
    elsewhere: "Forwards each driver message to the chat you pick, and takes your reply."
    never:
    - "It never replies without your yes."
    - "It only replies in conversations you’re already in."
    mode: waits
    preview:
      title: "Maya, Civic trip"
      body: "“Can I pick up at 7am Friday?” Reply drafted: “Sure, 7am works.”"
      ask: "Send it?"
      reply: "Send it"
      done: "Sent to Maya in her trip thread."
    tools: [read_events, read_conversation, messaging_reply]
    scopes: [read, messaging]
    tier: 2
    events: []
    schedule: "*/5 * * * *"
    unattended: true
    apps: [whatsapp, telegram, slack]
    jobs: [answer-faster]
    flow: "When a driver messages you on FLX → forwards it to your chat with a drafted reply → you send it as is or change it, and it replies in FLX"
    waits: Sends only the reply you approve
    agents: [openclaw, hermes]
  hermes:
    tags: [flx, messaging, chat]
    related_skills: [inbox-autopilot, requests-in-chat]
    blueprint:
      schedule: "*/5 * * * *"
      prompt: Forward new FLX driver messages to me here with a drafted reply.
  openclaw:
    emoji: "✉️"
---

# Answer drivers from your chat

Drivers message about pickups, parking and extensions at all hours. This recipe forwards
each new FLX message to your chat with a reply drafted, and sends your answer back in FLX,
so you never have to open another app to reply.

## When to use

- Every 5 minutes, on the bundled schedule.
- When you ask: "Anything from drivers?"

## Prerequisites

- FLX connected to your agent, with **read** and **messaging** access.
- WhatsApp, Telegram or Slack connected in **your agent**. FLX never holds that login.

## Procedure

1. Call `read_events` with your saved cursor and keep new messages; save the new `next_cursor`.
2. For each, call `read_conversation` for the thread so far.
3. Send the host the driver's message and a short drafted reply in their voice.
4. When the host answers ("send", or their own words), call `messaging_reply` in that thread.

## Pitfalls

- **Send only what the host approved.** Never reply on your own with this recipe; use
  `inbox-autopilot` for that.
- Replies go only in threads the host is already in.

## Verification

Every reply sent traces to a message from `read_events` and to the host's approval in chat.

## Example

> **Maya (Civic trip):** "Can I pick up at 7am Friday instead of 9?"
> Draft: "Sure, 7am works. The car will be ready." Reply **send** or write your own.

## Adapt this

- Forward only messages from drivers on a trip now.
- Change the tone of the drafts.
