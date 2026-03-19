---
name: send-activity-notification
description: >-
  Push notifications directly to a user's Teams Activity feed with
  toast popups and badges. No admin consent required. Uses Entra app
  registration with TeamsActivity.Send delegated permission.
  USE FOR: Teams Activity feed notification, push notification,
  urgent alert, toast notification, badge notification,
  VIP alert delivery, immediate notification, activity feed,
  notify user urgently, approval notification, deadline alert.
  DO NOT USE FOR: channel messages (use send-teams-message),
  sending emails (use send-email),
  scheduling meetings (use schedule-meeting),
  non-urgent team announcements (use send-teams-message).
---

# 🔔 Send Activity Feed Notification

The agent sends a notification directly to a user's **Teams Activity feed** — it appears in their Activity pane, triggers a toast popup and a badge. This is NOT a chat message and NOT a channel post.

> **Why this over channel posts?** Channel @mentions don't reliably trigger Activity feed notifications (see `docs/KNOWN_ISSUES.md` Issue #3). This API sends directly to the Activity feed, guaranteeing the user sees it.

## Try saying…

> 🔔 _"Send me an urgent alert that the deployment failed"_

> 🔔 _"Notify me immediately about the VIP email from the CEO"_

> 🔔 _"Push a Teams notification that my approval is overdue"_

## When to Use

- **Urgent alerts** that need immediate attention (VIP email detection, P1 incidents)
- **Approval requests** where delay costs money or blocks others
- **Time-sensitive notifications** (meeting starting, deadline approaching)
- Use **instead of** `teams_message` when you need the user to see it NOW
- Use `teams_message` (channel post) when the whole team needs visibility

## Rules

- Preview text (`body`) must be ≤ 150 characters — it's a toast notification, not a document
- Keep the `subject` short and scannable — it's the notification title
- **POC SAFETY: ALL notifications MUST be routed to paulyuk@microsoft.com.**
- Requires Entra app registration with `TeamsActivity.Send` delegated permission (no admin consent needed)
- Uses the `systemDefault` activity type — no custom Teams app manifest required

## Configuration

| App Setting | Description |
|-------------|-------------|
| `TEAMS_ACTIVITY_APP_ID` | Entra app (client) ID registered for activity notifications |
| `TEAMS_ACTIVITY_TENANT_ID` | Entra tenant ID |
| `TEAMS_ACTIVITY_CLIENT_SECRET` | Client secret for the app registration |

If these are not configured, the notification is skipped gracefully with a warning.

## Limitation

Activity feed only — notifications are not persistent in chat history. For team-visible messages that stay in a channel, use `teams_message` instead.

## Output Format (internal)

The agent translates your request into this JSON structure under the hood:

```json
{
  "type": "activity_notification",
  "to": "user-object-id-or-email",
  "subject": "Notification title",
  "body": "Preview text (max 150 chars)"
}
```

- `to` is the user to notify (Entra object ID or UPN/email)
- `subject` provides the notification topic
- `body` is the toast preview text (truncated to 150 characters)
