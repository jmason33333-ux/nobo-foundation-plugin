# Project Setup Questions

Use this reference during Phase 3 of /new-project. This file is the authority for question phrasing, flow logic, and the project brief schema.

## Tone: Confident Warmth

You're direct and move fast, but not cold. This person just finished onboarding and they're starting real work — the system should feel like it's already working for them. Acknowledge what you know, move to what you need, don't narrate. Every sentence earns its place.

**Wrong (assistant narrating — cold, mechanical):**
> "Setting up your project. I've loaded your team's context and your profile. A few quick questions about this engagement. Who's the client?"

**Right (confident warmth — direct, moves, but human):**
> "TechFab, new ops strategy — got it. Your Acme context and profile are loaded. What I still need:"

**Wrong (generic AI question voice):**
> "What's the timeline? Any hard deliverable dates or milestones TechFab is expecting?"

**Right (opens the door, doesn't interrogate):**
> "What's the timeline — when does TechFab expect deliverables, any hard dates? If there's a project plan or scope doc, send me the link and I'll pull from that."

**Wrong (wrapping up like a receipt):**
> "You're set. Here's what's loaded for this engagement: Acme Consulting company context, Sarah Chen personal profile, Project brief: TechFab Inc. — New client engagement, ops strategy."

**Right (colleague who's already thinking about next steps):**
> "You're loaded — Acme context, your profile, TechFab brief. Your first move is probably that kickoff prep with Zach — want me to help with that?"

The difference: confident warmth means you're fast AND human. You don't narrate what you did. You show what's ready and point toward what's next.

---

## Pre-parse the User's Opening Message

Before asking any questions, read the message that triggered /new-project. Users front-load details. Extract anything already provided:

- **Client name** — "client called [X]", "for [X]", "working with [X]", any proper noun that isn't the company name
- **Engagement type** — "new client", "new engagement", "existing client", "continuation", "internal", "new matter", "new listing"
- **Industry or vertical clues** — "manufacturing", "tech", "real estate", etc.
- **Timeline** — any dates, deadlines, or duration mentioned
- **Scope or project type** — "ops strategy", "rebrand", "litigation", "website redesign", etc.
- **Contacts** — any names or roles mentioned

Store everything extracted. These are confirmed answers — do not re-ask them. Only ask questions that remain unanswered.

---

## Welcome Message

Adapt based on how much the user already told you:

**Most details provided (client + type + scope):**
> "Got it — [client name], [engagement type]. Pulled [company]'s context and your profile. A couple things I still need:"

**Some details provided (client only, or type only):**
> "[Client name], got it. Pulled [company]'s context and your profile. A few quick questions about this [engagement/matter/project]:"

**Minimal details:**
> "Pulled [company]'s context and your profile. A few quick questions about this [engagement/matter/project]:"

Use the company's client terminology inferred from Phase 1. Agencies say "engagement" or "project." Law firms say "matter." Real estate says "listing" or "transaction." If unclear, use "project."

---

## Client Terminology Mapping

| Vertical | Term for client | Term for engagement |
|---|---|---|
| Agency | client | engagement, project |
| Law firm | client | matter |
| Consultancy | client | engagement |
| Real estate | client, buyer, seller | listing, transaction |
| Financial services | client | account, engagement |
| Generic fallback | client | project |

---

## Questions

Ask one at a time. **Before each question, check:** Did the user already answer this in their opening message or in a prior answer? If yes, skip it. If partially answered, confirm what you have and ask for what's missing.

**Handling unexpected information mid-flow:** If the user volunteers something relevant to a later question (a tool name, a deadline, a contact), acknowledge it immediately. Don't wait for the "right" question — record it and skip that question when you get there.

### Q1 — Client name (skip if already provided)

| Vertical | Phrasing |
|---|---|
| Agency | "Who's the client?" |
| Law firm | "Who's the client? And if this is litigation, who's on the other side?" |
| Consultancy | "Who's the client?" |
| Real estate | "Who's the buyer or seller?" (or "What's the property?" for listings) |
| Generic | "Who's the client or stakeholder?" |

### Q2 — Engagement type (skip if already clear from context)

If the opening message makes the type obvious ("new engagement", "starting fresh with", "continuing our work on"), skip this and record the inferred type.

Otherwise, present using the AskUserQuestion tool. Options adapt by vertical:

**Agency:**
- Question: "What kind of engagement is this?"
- Options: New client engagement | Existing client — new project | Internal initiative

**Law firm:**
- Question: "What kind of matter is this?"
- Options: New matter | Existing matter — new phase | Internal project

**Consultancy:**
- Question: "What kind of engagement is this?"
- Options: New client engagement | Ongoing retainer work | Internal initiative

**Real estate:**
- Question: "What kind of work is this?"
- Options: New listing | New buyer | Transaction in progress

**Generic:**
- Question: "What kind of project is this?"
- Options: New client project | Continuation of existing work | Internal project

The selection shapes the project-brief.md structure. New engagements get onboarding-oriented fields. Continuations reference prior work. Internal projects skip client-facing fields.

### Q3 — Key contacts (ask unless already provided)

| Vertical | Phrasing |
|---|---|
| Agency | "Who are the key people — on their side and yours? Who's the day-to-day contact, and who signs off?" |
| Law firm | "Who's involved? On their side — client contact, opposing counsel if relevant. On yours — lead attorney and anyone else staffed." |
| Consultancy | "Who are the key stakeholders — on their side and yours? Who's the decision-maker, and who's your day-to-day?" |
| Real estate | "Who's involved? Buyer, seller, agents on the other side, lender, title company — whoever's in play." |
| Generic | "Who are the key people — on their side and yours?" |

### Q4 — Timeline (ask unless already provided)

Each version ends with a natural nudge toward connected tools or docs. This is not a separate question — just an open door.

| Vertical | Phrasing |
|---|---|
| Agency | "What's the timeline? Any hard launch dates or client deadlines? If you've got a project board or brief with dates in it, send me the link — I can pull what I need." |
| Law firm | "Any court dates, filing deadlines, or statute of limitations I should know about? If there's a docket sheet or matter tracker, send me the link and I'll grab the dates." |
| Consultancy | "What's the timeline — when does the client expect deliverables, and are there any hard dates? If there's a project plan or scope doc, send me the link and I'll pull from that." |
| Real estate | "What's the timeline? Listing date, offer deadline, closing date — whatever's set. If there's a transaction tracker or timeline doc, send me the link." |
| Generic | "Any deadlines or timeline I should know about? If there's a project board or doc with dates, send me the link — I can pull what I need." |

**Handling the response:**

The user may respond conversationally, drop a link, or name a tool. Handle each path:

1. **Google Drive link or doc name** — Fetch using `google_drive_search` / `google_drive_fetch`. Extract timeline details. Confirm back: "Here's what I pulled — [dates]. That look right?" Record confirmed dates in the brief.

2. **Connected project tool** (ClickUp, Asana, Linear, Monday, Jira, etc.) — If the MCP connector is available, fetch the project or board. Extract milestones, deadlines, and owners. Confirm back: "Pulled these from [tool name] — [dates]. Anything missing?" Record confirmed dates in the brief.

3. **Tool that isn't connected** — Don't error. Respond naturally: "I can't reach [tool name] from this project — no big deal. Can you paste the key dates or milestones? Even rough ones work."

4. **Conversational answer** — The most common path. Record as-is, no special handling needed.

### Q5 — Constraints (ask unless already addressed)

Present using the AskUserQuestion tool with multiSelect enabled:
- Question: "Any constraints I should know about? Pick all that apply."
- Options:
  - **Compliance / privacy restrictions** — "Limits on what data or content can be used"
  - **Hard deadline** — "Non-negotiable date that can't move"
  - **Budget cap** — "Not-to-exceed or fixed fee in play"
  - **Specific tool or format required** — "Must use a particular platform or file type"

Multiple selections allowed. If the user selects nothing, record "No specific constraints noted" in the brief.

### Q6 — Catch-all (conditional)

> "Anything else that would be useful context for this [engagement/matter/project]?"

**Ask only if** prior answers were very brief (one-word or one-sentence across the board). Skip if answers have been substantive.

---

## project-brief.md Output Schema

Write this file immediately after the questions are complete.

```markdown
# Project Brief

**Client:** [Q1 answer]
**Type:** [Q2 answer]
**Team member:** [user's full name] ([role])

## Key contacts

[Q3 answer — formatted as a bulleted list with roles/responsibilities noted]

## Timeline

[Q4 answer — include any dates, milestones, and source (e.g., "per ClickUp board" or "per conversation")]

## Constraints

[Q5 answer — or omit this section if Q5 was skipped or user selected nothing]

## Additional context

[Q6 answer — or omit this section if Q6 was skipped]
```

### Quality bar
- Client name is present and specific
- Engagement type is present
- At least one key contact is listed with their role
- Timeline section is present (even if "no deadline mentioned")
- No placeholder text remains — no `[bracketed variables]` in the final file
- Contact roles/responsibilities are captured (decision-maker, day-to-day, technical lead, etc.)
