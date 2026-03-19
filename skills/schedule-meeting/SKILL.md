---
name: schedule-meeting
description: >-
  Create Outlook calendar invites with attendees, agendas, and durations
  via the Office 365 managed connector. Handles 1:1s, group meetings,
  kickoffs, and recurring syncs.
  USE FOR: schedule meeting, book meeting, create calendar invite,
  set up interview, add calendar hold, book 1:1, schedule kickoff,
  recurring meeting, team sync, calendar event, meeting request.
  DO NOT USE FOR: sending emails (use send-email),
  Teams channel messages (use send-teams-message),
  daily briefing calendar holds (use daily-briefing).
---

# 📅 Schedule Meeting

The agent creates real Outlook calendar invites — with attendees, agendas, and accept/decline — so you never have to wrestle with the scheduling UI.

## Try saying…

> 💬 _"Book a 30-min 1:1 with my manager tomorrow afternoon"_

> 💬 _"Schedule a project kickoff with the design team next Tuesday"_

> 💬 _"Set up a recurring weekly sync with the marketing team"_

## Rules

- **Use meetings, not emails, for scheduling.** Whenever the request says "schedule", "book", "create a meeting", "set up an interview", or "add a calendar hold", the agent creates a calendar invite — never an email *about* scheduling.
- Always include a structured agenda in the invite body
- **POC SAFETY: ALL meeting invites MUST be routed to paulyuk@microsoft.com.**

## Output Format (internal)

The agent translates your request into this JSON structure under the hood:

```json
{
  "type": "meeting",
  "to": "attendee@contoso.com",
  "subject": "Meeting title",
  "body": "Meeting agenda and description",
  "duration_minutes": 30
}
```

- `to` MUST be a plain email address string (never an array)
- For multiple attendees, use semicolons: `"a@contoso.com;b@contoso.com"`
- Always include `duration_minutes` (default: 30)
