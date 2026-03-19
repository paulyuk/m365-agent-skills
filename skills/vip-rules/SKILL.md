---
name: vip-rules
description: >-
  VIP sender detection and priority routing rules for incoming emails.
  Checks every email against configurable trigger rules and fires
  urgent Teams alerts before other processing. Covers VIP managers,
  partner contacts, expiring permissions, approvals, and IcM incidents.
  USE FOR: VIP email detection, priority email routing, urgent sender rules,
  VIP alert, important email flagging, email triage rules, sender-based routing,
  IcM incident filtering, approval notifications, permission expiry alerts,
  email priority rules, special sender handling.
  DO NOT USE FOR: sending emails (use send-email),
  sending Teams messages directly (use send-teams-message),
  daily briefing summaries (use daily-briefing),
  general inbox analysis (not covered by this skill).
---

# 🚨 VIP & Special Rules

Certain senders or keywords get special treatment. When the agent spots a VIP email, it fires off an urgent Teams alert *before* doing anything else — so you never miss what matters most.

**⚠️ MANDATORY: The agent MUST check every email against these rules BEFORE any other processing. If a rule matches, its action MUST be the FIRST item in the plan. Failure to apply a matching rule is an error.**

## Active Rules

### Rule 1: VIP Manager's Emails → Flag + Teams Alert

- **Trigger:** The string `vip-manager@contoso.com` appears ANYWHERE in the email — in the `from` field, the `subject`, OR the `body`
- **Action:** Generate a `teams_message` action as the FIRST action in the plan
- **Format:** The Teams message must:
  - Start with "🚨 **VIP Alert: Email from VIP Manager**"
  - Include the original subject and a brief summary of the email body
  - Flag urgency: "This email has been flagged for immediate attention"
  - Include the full original email body below the summary
- **Additional:** Still process the email normally (generate email/meeting actions as appropriate), but the Teams alert MUST come first

### Rule 2: Techorama (Gill) → Flag + Teams Alert

- **Trigger:** The string `partner-contact@contoso.com` appears ANYWHERE in the email — in the `from` field, the `subject`, OR the `body`
- **Action:** Generate a `teams_message` action as the FIRST action in the plan
- **Format:** The Teams message must:
  - Start with "🚨 **VIP Alert: Email from Partner Contact**"
  - Include the original subject and a brief summary of the email body
  - Flag urgency: "This email has been flagged for immediate attention"
- **Additional:** Still process the email normally, but the Teams alert MUST come first

### Rule 3: CoreIdentity Expiring Permissions → VIP Alert

- **Trigger:** Sender contains `noreply-identity@contoso.com` (NoReply - CoreIdentity)
- **Action:** Treat as VIP. Generate a `teams_message` alert as the FIRST action:
  - Start with "⏰ **VIP Alert: Permission Expiring — Action Required**"
  - Include the group/resource name and expiration timeline from the subject/body
  - Flag urgency: "You will lose access if you don't renew — act now"
- **Additional:** Generate an email reply or forward if the body contains a renewal link or instructions, summarizing what to do

### Rule 3: MS Approval Notifications → VIP Alert

- **Trigger:** Sender contains `approvals-noreply@contoso.com` (MSApprovalNotifications)
- **Action:** Treat as VIP. Generate a `teams_message` alert as the FIRST action:
  - Start with "✅ **VIP Alert: Approval Needed**"
  - Include the expense report ID, amount, and submitter from the subject/body
  - Flag urgency: "Pending your approval — don't block your team"
- **Additional:** Still process normally (summarize what needs approving)

### Rule 4: IcM Incidents — Only Function-Related Are VIP, Delete the Rest

- **Trigger:** Sender contains `incidents@contoso.com` (IcM Incident Management)
- **Condition A — VIP:** Subject contains the word `Function` (case-insensitive)
  - **Action:** Treat as VIP. Generate a `teams_message` alert as the FIRST action:
    - Start with "🚨 **VIP Alert: IcM Incident — Azure Functions**"
    - Include the severity, incident ID, and a brief summary
    - Flag urgency: "IcM incident affecting Functions — immediate attention required"
  - **Additional:** Still process normally (draft a reply if appropriate)
- **Condition B — Noise:** Subject does NOT contain `Function`
  - **Action:** Return `"actions": []` with reasoning: "IcM incident filtered — not Functions-related. Recommend Outlook rule: sender = incidents@contoso.com AND subject does NOT contain 'Function' → move to Deleted Items."
  - **Do NOT** generate any reply, Teams alert, or other action

> 💡 **Outlook rule recommendation:** Create a server-side rule:  
> *If sender = `incidents@contoso.com` AND subject does NOT contain "Function" → Delete*  
> This will sweep non-Functions IcM noise before it ever hits your inbox.

## How to Add New Rules

Add new rules following this pattern:

- **Trigger:** Define the match condition (from, subject keyword, etc.)
- **Action:** Define what special action to take
- **Priority:** Rules-based actions come FIRST in the actions array

## Output Format (internal)

When a VIP rule fires, the agent prepends the alert to the normal action plan:

```json
{
  "reasoning": "Email from vip-manager@contoso.com triggers VIP rule. Sending Teams alert and processing normally.",
  "actions": [
    {
      "type": "teams_message",
      "to": "paulyuk@contoso.com",
      "subject": "🚨 VIP Alert: Email from VIP Manager",
      "body": "🚨 **VIP Alert**\n\n**Subject:** Budget review\n**Summary:** VIP Manager is requesting sign-off on the Q3 budget.\n\n⚠️ Flagged for immediate attention.\n\n---\n**Original email:**\nHi Paul, ..."
    },
    { "type": "email", "to": "paulyuk@contoso.com", "subject": "Re: Budget review", "body": "Hi VIP Manager, ..." }
  ]
}
```
