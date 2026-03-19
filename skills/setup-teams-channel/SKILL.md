---
name: setup-teams-channel
description: >-
  Discover Teams and channels, parse Teams deep links, and configure
  the agent to post to the right channel. Setup-only skill for
  channel configuration.
  USE FOR: set up Teams channel, configure Teams channel, find my Teams,
  list Teams channels, parse Teams link, Teams channel setup,
  discover teams, configure agent channel, Teams deep link,
  get link to channel, channel configuration.
  DO NOT USE FOR: sending Teams messages (use send-teams-message),
  sending emails (use send-email),
  Teams Activity notifications (use send-activity-notification).
---

# 🔧 Set Up Teams Channel

The agent helps you discover your Teams and channels, then configures itself to post there. No Portal clicking, no copying GUIDs — just ask, or paste a link.

## Try saying…

> 🔧 _"Set up my Teams channel for agent alerts"_

> 🔧 _"Find my Engineering team and the General channel"_

> 🔧 _"Use this channel: https://teams.microsoft.com/l/channel/19%3Aabc123%40thread.tacv2/General?groupId=201c2bb2-812d-4986-ac92-9bfad0d6cc59"_

## Fastest Path: Paste a Deep Link

Right-click any channel in Teams → **Get link to channel** → paste it:

> _"Set up my agent to post here: https://teams.microsoft.com/l/channel/19%3A...%40thread.tacv2/My%20Channel?groupId=201c2bb2-..."_

The agent parses the URL and extracts the IDs automatically:

| URL Component | Extracted Value | Maps To |
|---------------|----------------|---------|
| Path after `/l/channel/` | `19:abc...@thread.tacv2` | `TEAMS_CHANNEL_ID` |
| `groupId=` query param | `201c2bb2-...` | `TEAMS_TEAM_ID` |
| Path segment after channel ID | `My Channel` (URL-decoded) | Channel display name (for confirmation) |

### Deep link format

```
https://teams.microsoft.com/l/channel/{channelId}/{channelName}?groupId={teamId}
```

After parsing, the agent confirms what it found and runs:

```bash
azd env set TEAMS_TEAM_ID "201c2bb2-..."
azd env set TEAMS_CHANNEL_ID "19:abc...@thread.tacv2"
azd env set TEAMS_MENTION_NAME "Your Name"
azd deploy
```

## Discovery Path: Let the Agent Find It

If you don't have a link, the agent discovers your teams and channels interactively.

### Step 1: List your teams

```bash
teams.list_teams()
# Returns: [{"displayName": "Engineering", "id": "201c2bb2-..."}, ...]
```

### Step 2: List channels in your team

```bash
teams.list_channels(team_id="201c2bb2-...")
# Returns: [{"displayName": "General", "id": "19:abc...@thread.tacv2"}, ...]
```

### Step 3: Verify with a deep link

The agent gives you a clickable Teams deep link so you can confirm it's the right channel:

```
https://teams.microsoft.com/l/channel/19%3Aabc123%40thread.tacv2/My%20Agent?groupId=201c2bb2-812d-4986-ac92-9bfad0d6cc59
```

Click it → Teams opens → you see the channel → confirm → agent configures.

## Recommended Channel Setup

| Channel | Purpose | Who sees it |
|---------|---------|-------------|
| **🤖 My Agent** | Personal alerts, VIP notifications, task confirmations | Just you |
| **📢 Team Updates** | Incident alerts, announcements, status posts | Your whole team |

> **Tip:** Start with one channel for everything. Split into personal + team channels once you've tuned your VIP rules.

## Rules

- This skill is for **setup only** — it doesn't send messages (use `send-teams-message` for that)
- Always verify with a deep link before configuring — wrong channel = alerts going to the wrong people
- Channel IDs look like `19:abc123...@thread.tacv2` — if it doesn't start with `19:`, it's not a channel ID
- When the user pastes a Teams URL, ALWAYS parse it — don't ask them to extract the IDs manually
