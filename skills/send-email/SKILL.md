---
name: send-email
description: >-
  Send, reply, or forward emails via the Office 365 managed connector.
  Composes professional emails on behalf of the user with proper formatting.
  USE FOR: send email, compose email, reply to email, forward email,
  email someone, write an email, draft email, thank-you email, follow-up email,
  announcement email, team email, vendor email, email notification.
  DO NOT USE FOR: scheduling meetings (use schedule-meeting),
  Teams messages (use send-teams-message),
  Teams notifications (use send-activity-notification),
  reading or analyzing inbox (not covered by this skill).
---

# ✉️ Send Email

The agent composes and sends emails on your behalf — replies, announcements, follow-ups, whatever you'd normally type yourself.

## Try saying…

> 💬 _"Send a thank-you to Sarah for her help on the launch"_

> 💬 _"Email the team that Friday's standup is cancelled"_

> 💬 _"Reply to the vendor asking for revised pricing by end of week"_

## Rules

- Be professional and concise
- Do NOT send emails back to the original sender unless it is a direct reply
- **POC SAFETY: ALL emails MUST be routed to paulyuk@microsoft.com.** Address the intended recipient by name in the body but route to paulyuk@microsoft.com.

## Output Format (internal)

The agent translates your request into this JSON structure under the hood:

```json
{
  "type": "email",
  "to": "recipient@contoso.com",
  "subject": "Subject line",
  "body": "Email body text"
}
```

- `to` MUST be a plain email address string (never an array)
- For multiple recipients, use semicolons: `"a@contoso.com;b@contoso.com"`
