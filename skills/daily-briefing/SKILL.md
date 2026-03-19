---
name: daily-briefing
description: >-
  Morning summary email with today's meetings, emails needing reply,
  urgent items, and a catch-up calendar hold. Runs on a timer trigger
  at 8:00 AM weekdays. Returns exactly 2 actions: one briefing email
  and one calendar hold.
  USE FOR: daily briefing, morning summary, daily digest, today's schedule,
  inbox summary, email triage summary, morning email, daily standup prep,
  what's on my calendar today, catch-up block, daily overview.
  DO NOT USE FOR: sending individual emails (use send-email),
  scheduling specific meetings (use schedule-meeting),
  VIP alert routing (use vip-rules),
  Teams notifications (use send-teams-message or send-activity-notification).
---

# ☀️ Daily Briefing

Every morning the agent sends you a concise summary of your day — meetings, emails needing replies, and anything urgent — so you can hit the ground running.

## What You Get

A single email at **8:00 AM** with:

1. **📅 Today's Meetings** — chronological list with time, title, and attendees
2. **📬 Emails Needing Reply** — emails from this week where you're in the To line, haven't replied, and the sender is a real person (not a bot, group, or distribution list)
3. **🚨 Urgent / Leadership** — anything flagged urgent, from your management chain, or from someone with VP/President/Director in their title
4. **⏰ Catch-Up Block** — a 15-minute calendar hold in your next free slot to process the flagged items

## Filtering Rules

### Include emails that:
- Were received this week (Monday through today)
- Have you in the `To` field (not just CC/BCC)
- You have NOT already replied to
- Look like they expect a response (questions, requests, action items)

### Exclude emails from:
- Bots and automated senders (e.g., `prodcatbot@contoso.com`, `noreply@`, `notifications@`)
- Distribution lists and group mailboxes
- Emails with `[Test from m365 agent]` in the subject
- Newsletters, digests, and bulk mail

### Flag as urgent:
- Emails marked with high importance
- Sender title contains **VP**, **President**, **Director**, **CVP**, **GM**, or **Chief**
- Sender is in your direct management chain
- Subject contains words like **urgent**, **ASAP**, **EOD**, **escalation**, **blocking**

## Output Rules — CRITICAL

**Return EXACTLY 2 actions. No more, no less.**

1. **One summary email** — contains the entire briefing in a single HTML email. Do NOT send individual replies. Do NOT send VIP alerts. Do NOT draft response emails. Just summarize.
2. **One catch-up meeting** — a 15-minute calendar hold.

Do NOT:
- Draft reply emails to any of the flagged messages
- Send separate VIP/urgent alert emails or Teams messages
- Create multiple actions for individual emails
- Reply to emails on the user's behalf

The purpose of the briefing is **awareness**, not action. The user will decide what to reply to during their catch-up block.

## Output Format

```json
{
  "reasoning": "Daily briefing for Tuesday March 7. Found 6 meetings, 4 emails needing reply (1 urgent from VP).",
  "actions": [
    {
      "type": "email",
      "to": "paulyuk@contoso.com",
      "subject": "☀️ Daily Briefing — Tuesday, March 7",
      "body": "<h2>📅 Today's Meetings (6)</h2><ol><li><b>9:00 AM</b> — Team Standup (Sarah, Alex, Liwei)</li><li><b>10:30 AM</b> — 1:1 with Manager</li>...</ol><h2>📬 Emails Needing Reply (4)</h2><ol><li>🚨 <b>Amanda Silver (CVP)</b> — \"Q3 budget sign-off\" — <i>Received Mon</i></li><li><b>Liwei Zhang</b> — \"Design review feedback\" — <i>Received Tue</i></li>...</ol><h2>⏰ Catch-Up Block</h2><p>15 min hold at 11:00 AM to triage the above.</p>"
    },
    {
      "type": "meeting",
      "to": "paulyuk@contoso.com",
      "subject": "⏰ Catch-Up: Review flagged emails",
      "body": "15-minute block to review and respond to flagged emails from your daily briefing.",
      "duration_minutes": 15
    }
  ]
}
```

## Architecture

This skill uses a **timer trigger** in the Function App that fires at 8:00 AM weekdays:
- The `daily_briefing_trigger` timer function fetches today's calendar events and unread emails via O365 connector client
- Calls the LLM with the briefing prompt
- Executes the task plan directly via connector clients (send briefing email + create catch-up calendar hold)
