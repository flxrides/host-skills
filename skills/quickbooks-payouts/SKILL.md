---
name: quickbooks-payouts
description: Use when the host keeps their books in QuickBooks and wants each month's FLX payouts recorded
  there, or on the scheduled first-of-the-month update.
version: 1.0.0
author: FLX Rides
license: MIT
metadata:
  flx:
    title: Record your FLX payouts in QuickBooks
    why: "Month end is easier when the income is already in your books. Last month’s FLX payouts are drafted as a deposit in QuickBooks for you to check and save."
    elsewhere: "Drafts one deposit a month for you to check and save."
    never:
    - "It never saves the deposit. You check it and save it."
    - "It never changes or rounds an amount."
    - "It won’t record a month twice."
    mode: waits
    preview:
      title: "September payouts"
      body: "$4,380 in 9 payouts. Deposit drafted to FLX income."
      ask: "Save it?"
      reply: "Saved"
      done: "September is in your books."
    tools: [read_finances]
    scopes: [read]
    tier: 1
    events: []
    schedule: "0 8 1 * *"
    unattended: true
    apps: [quickbooks]
    jobs: [track-the-money]
    flow: "On the first of the month → reads what FLX paid to your bank last month → drafts the deposit in QuickBooks → you check it and save it"
    waits: You save every entry yourself
    agents: [openclaw, hermes, claude, grok]
  hermes:
    tags: [flx, money, bookkeeping]
    related_skills: [earnings-sheet, weekly-email]
    blueprint:
      schedule: "0 8 1 * *"
      prompt: Read last month's FLX payouts and draft the deposit entry in QuickBooks for me to save.
  openclaw:
    emoji: "📒"
---

# Record your FLX payouts in QuickBooks

Month end is easier when the income is already in your books. This recipe reads what FLX
paid to your bank last month and drafts the deposit in QuickBooks for you to check and save.

## When to use

- On the first of each month, on the bundled schedule.
- When you ask: "Put last month's FLX payouts in QuickBooks."

## Prerequisites

- FLX connected to your agent, with **read** access. Nothing else.
- QuickBooks connected in **your agent**, and the account the deposit goes to.

## Procedure

1. Call `read_finances` with `period_days` covering last month.
2. Draft one deposit: the month, the total paid to the bank and the number of payouts, using
   the `display` values exactly as FLX returns them.
3. Show the host the draft. Save it only when they say so.

## Pitfalls

- **FLX supplies totals, not a line per trip.** Record them as one monthly deposit.
- Never change or round an amount.
- Don't save twice: check the month isn't already recorded.

## Verification

The drafted amount and payout count match `read_finances` for the month.

## Example

> **September payouts: $4,380 in 9 payouts.** Draft deposit to "FLX income" is ready.
> Save it?

## Adapt this

- Name the account and memo you want.
- Run it weekly instead.
