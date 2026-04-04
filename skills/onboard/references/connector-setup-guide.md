# Connector Setup Guide

Use this reference during Phase 5 of /onboard. Walk the user through each connector one at a time. Only recommend connectors for tools the company actually uses (check the `How We Work` doc content from Phase 0).

## Connector Priority Order

1. **Gmail** — high priority for any role that sends or receives email
2. **Google Calendar** — high priority for meeting-heavy roles
3. **Slack** — only if the company uses Slack (check `How We Work` doc)

## Setup Instructions (per connector)

### Gmail

> "To connect Gmail:
> 1. Open the sidebar
> 2. Go to Customize, then Connectors
> 3. Find Gmail in the list
> 4. Click Enable and follow the authorization prompts
>
> Were you able to connect it?"

**If it works:** "Great — Claude can now read your email threads when you need it."

**Common issues:**
- "I don't see Gmail in the list" — "That's fine, it may not be available yet. Skip it for now — you can always add it later from the same menu."
- Authorization error — "Try clicking Enable again. If it still gives an error, skip it for now."

### Google Calendar

> "To connect Google Calendar:
> 1. Same place — Customize, then Connectors
> 2. Find Google Calendar
> 3. Click Enable and authorize
>
> Were you able to connect it?"

**If it works:** "Nice — Claude can now check your calendar for meeting prep and scheduling context."

**Common issues:**
- Same as Gmail — guide to skip if unavailable.

### Slack

Only offer if the company uses Slack (confirmed from `How We Work` doc).

> "To connect Slack:
> 1. Customize, then Connectors
> 2. Find Slack in the list
> 3. Click Enable — you may need to authorize through your Slack workspace
>
> Were you able to connect it?"

**If it works:** "Good — Claude can now pull context from Slack channels when you need it."

**Common issues:**
- "I need admin approval" — "No problem — ask your Slack admin to approve the integration, or skip this for now. It's not required."
- Workspace not found — "Make sure you're signed into the right Slack workspace, then try again. If it still doesn't work, skip it."

## General Rules

- Never block onboarding on connector setup. Every connector is optional except Google Drive (handled in Phase 0).
- If a connector fails, say "No worries — you can always set this up later from the same menu." Record the skip.
- At the end of Phase 5, summarize: "[X] tools connected, [Y] skipped."
