# m365-agent-skills

Copilot CLI plugin providing M365 agent skills for email, calendar, Teams messaging, and activity notifications via Azure managed connectors.

## Install

```bash
# From GitHub
copilot plugin install coreai-microsoft/m365-agent-skills

# From local path
copilot plugin install ./path/to/m365-agent-skills
```

## Included Skills

| Skill | Description |
|-------|-------------|
| **send-email** | Send, reply, forward emails via O365 connector |
| **schedule-meeting** | Create Outlook calendar invites with attendees and agendas |
| **send-teams-message** | Post to Teams channels with @mentions and HTML formatting |
| **send-activity-notification** | Push to Teams Activity Feed (no admin consent required) |
| **setup-teams-channel** | Discover teams/channels, parse deep links, configure target channel |
| **vip-rules** | VIP sender detection + priority routing with urgent Teams alerts |
| **daily-briefing** | Morning summary email + catch-up calendar hold |

## Prerequisites

- [azure-functions-connectors-python](https://github.com/coreai-microsoft/azure-functions-connectors-python) SDK (or any LLM agent that can produce the JSON action format)
- Azure Function App with O365 and Teams API connections provisioned
- Entra app registration with `TeamsActivity.Send` delegated permission (for activity notifications)

## How It Works

Each skill provides a `SKILL.md` with structured instructions that tell the AI agent:
- **When** to activate (via `USE FOR` / `DO NOT USE FOR` in the description)
- **What** to produce (JSON action format with `type`, `to`, `subject`, `body`)
- **Rules** for formatting, safety, and routing

The skills are designed for the [m365-agent-poc](https://github.com/coreai-microsoft/m365-agent-poc) reference architecture but work with any agent framework that can read markdown skill definitions and produce structured JSON actions.

## Related

- **Full app**: [coreai-microsoft/m365-agent-poc](https://github.com/coreai-microsoft/m365-agent-poc)
- **Connector SDK**: [coreai-microsoft/azure-functions-connectors-python](https://github.com/coreai-microsoft/azure-functions-connectors-python)
