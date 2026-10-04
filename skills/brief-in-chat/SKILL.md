---
name: brief-in-chat
description: Use when the host wants their morning FLX brief delivered to a chat app they already read
  (WhatsApp, Telegram or Slack), or on the scheduled morning check-in.
version: 1.0.0
author: FLX Rides
license: MIT
metadata:
  flx:
    title: Get your morning brief where you already read
    why: "The few things that need you today, sent to the chat you open first. Every item links back to FLX, where you act on it."
    elsewhere: "Sends you one message each morning, in the chat you pick."
    never:
    - "It changes nothing on FLX."
    - "It can’t approve anything from chat. You act in FLX."
    - "It never sends an empty brief. A quiet morning gets one line."
    mode: reads
    preview:
      title: "Good morning. 2 things need you."
      body: "Maya asked about a Friday pickup, and a bid on the Niro waits for your yes."
    tools: [read_attention, read_proposals, read_events]
    scopes: [read]
    tier: 1
    events: []
    schedule: "0 7 * * *"
    unattended: true
    apps: [whatsapp, telegram, slack]
    jobs: [hear-about-it]
    flow: "Every morning → reads what needs you and what's waiting for your yes → sends the brief to your chat, with a link to each item in FLX"
    waits: Reads only
    agents: [openclaw, hermes]
  hermes:
    tags: [flx, daily, chat]
    related_skills: [flx-daily-brief, bid-from-chat]
    blueprint:
      schedule: "0 7 * * *"
      prompt: Send me my FLX morning brief in this chat.
  openclaw:
    emoji: "💬"
---

# Get your morning brief where you already read

The morning brief, sent to the chat you open first: the few things that need you today,
what's waiting for your yes, and what changed overnight. Each item links back to FLX, where
you act on it.

## When to use

- Every morning, on the bundled schedule.
- When you ask in chat: "What needs me today?"

## Prerequisites

- FLX connected to your agent, with **read** access. Nothing else.
- The chat app (WhatsApp, Telegram or Slack) connected in **your agent**, and the chat to send
  to. FLX never holds that login.

## Procedure

1. Call `read_attention` for what needs the host today.
2. Call `read_proposals` for what's waiting for their yes.
3. Call `read_events` with your saved cursor for what changed; save the new `next_cursor`.
4. Write the brief: the few items that need the host first, then what's waiting, then one line
   on what changed. Link each item to `https://flxrides.com/host/agents`.
5. Send it to the host's chat. Keep it short enough to read on a phone.

## Pitfalls

- **Reads only.** It can't approve anything from chat; approvals happen in FLX.
- Don't send an empty brief. When nothing needs the host, send one line saying so.

## Verification

Every item traces to a row from `read_attention`, `read_proposals` or `read_events`.

## Example

> **Good morning. 2 things need you.**
> 1. Maya asked about an early pickup Friday. Reply in FLX.
> 2. A bid on a 4-week hybrid call is waiting for your yes: $320 a week for the Niro.
> Overnight: the Civic came back on time.

## Adapt this

- Change the time, or send it to a different chat.
- Ask for money figures to be left out, or added.
