---
name: send-teams-message
description: >-
  Post messages to Microsoft Teams channels with @mentions via the
  Teams managed connector. Supports HTML formatting for announcements,
  alerts, and status updates.
  USE FOR: send Teams message, post to Teams, Teams channel message,
  team announcement, alert channel, Teams notification, share in Teams,
  post update, channel post, @mention in Teams.
  DO NOT USE FOR: sending emails (use send-email),
  scheduling meetings (use schedule-meeting),
  Teams Activity feed notifications (use send-activity-notification),
  configuring which channel to post to (use setup-teams-channel),
  sending DMs in Teams (not supported without admin consent).
---

# 💬 Send Teams Message

The agent posts messages to Microsoft Teams channels — announcements, alerts, status updates — and @mentions the right people so nothing gets missed.

> **Note:** Teams messages are posted to a designated channel and @mention the recipient. Direct messages (DMs) via Graph API require `Chat.ReadWrite` or `ChatMessage.Send` permissions, both of which need **admin consent** in M365 tenants. Channel posting works without admin consent because the user already has permission to post in channels they belong to. See `docs/KNOWN_ISSUES.md` Issue #2 for details on tested approaches.

## Try saying…

> 💬 _"Post in the team channel that the release is live"_

> 💬 _"Alert the on-call channel about the P1 incident"_

> 💬 _"Share the Q3 OKR draft with the leadership team in Teams"_

## Rules

- Use this for urgent notifications, team announcements, or direct alerts
- Keep messages concise — Teams messages should be scannable
- `body` supports HTML formatting (bold, italic, lists, emoji)
- **POC SAFETY: ALL Teams messages MUST be routed to paulyuk@microsoft.com.**

## Configuration

The target channel and @mention user are configured via Function App app settings:

| App Setting | Description | Example |
|-------------|-------------|---------|
| `TEAMS_TEAM_ID` | Team GUID (from Teams URL or Graph API) | `201c2bb2-812d-4986-ac92-9bfad0d6cc59` |
| `TEAMS_CHANNEL_ID` | Channel thread ID | `19:95d8c41cc0ce...@thread.tacv2` |
| `TEAMS_MENTION_USER_ID` | Entra object ID of user to @mention | `3c9834d3-6a72-4938-...` |
| `TEAMS_MENTION_NAME` | Display name for the @mention | `Paul Yuknewicz` |

Run the discovery script to find these values automatically:

```bash
./infra/scripts/discover-teams-ids.sh
```

It queries Graph API, lists your Teams and channels, and prints the `azd env set` commands to copy-paste.

## Why Not DMs?

The Teams managed API connector blocks all DM/chat endpoints — they return 404. Calling Graph API directly would bypass this, but `Chat.ReadWrite` and `ChatMessage.Send` delegated permissions both **require admin consent**, which is a non-starter for many orgs. See `docs/AUTH-CAPABILITIES.md` for the full matrix.

## Output Format (internal)

The agent translates your request into this JSON structure under the hood:

```json
{
  "type": "teams_message",
  "to": "recipient@contoso.com",
  "subject": "Message subject/title",
  "body": "Message content (supports HTML formatting)"
}
```

- `to` is the user to @mention in the channel post (email address format)
- `subject` provides context/title for the message
