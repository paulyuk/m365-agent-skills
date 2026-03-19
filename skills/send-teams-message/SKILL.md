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

### Subject formatting
- Prefix the subject with `[{FirstName}Agent]` where `{FirstName}` comes from the `TEAMS_MENTION_NAME` app setting (e.g., if `TEAMS_MENTION_NAME=Paul Yuknewicz`, prefix is `[PaulAgent]`). If the name is not configured, use `[Agent]`.
- The subject should be a clean, descriptive title — NOT the user's raw instruction
- Strip phrases like "Post to Teams", "Send a Teams message", "Post to PM channel" from the subject
- Example: User says "Post to Teams that the release is live" → subject: `[PaulAgent] Release is live`

### Body formatting
- The body is what the TEAM sees — it must read as a natural message to the channel
- Strip the user's instruction preamble (e.g., "Post a Teams message saying...", "Send to the channel:")
- Only include the actual message content the user wants the team to read
- Do NOT include meta-instructions like "Please post the following message"
- Do NOT echo back the user's prompt — just deliver the message
- Example: User says "Post a Teams message to PM channel: Hey team, standup in 5 min" → body: `Hey team, standup in 5 min`

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
  "to": "recipient@microsoft.com",
  "subject": "Message subject/title",
  "body": "Message content (supports HTML formatting)"
}
```

- `to` is the user to @mention in the channel post (email address format)
- `subject` provides context/title for the message
