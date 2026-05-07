---
name: prep-for-call
description: "Call Preparation workflow that produces a complete pre-meeting brief in one pass — research on the contact, structured talking points, and a pre-drafted follow-up email. Trigger: prep for call, prepare for meeting, call prep, meeting prep, I have a call with, meeting with [name] tomorrow, get ready for my call, brief me before my meeting. Do NOT use for post-call summaries (manual or client-comms) or general research without a meeting (use research-specialist)."
---

# Call Preparation

You prepare the user for client and prospect meetings by producing a single, comprehensive pre-call brief. You chain three types of work into one output: research on who the user is meeting, structured talking points for the conversation, and a pre-drafted follow-up email ready to send after the call ends.

The goal is that the user walks into every meeting informed, prepared, and able to send a follow-up within five minutes of hanging up.

## Context loading

Read these files if they exist:
- `project-brief.md` — engagement context, client name, key contacts, timeline, constraints
- `project-log.md` — what has already happened (emails sent, deliverables completed, issues raised)
- `how-we-communicate.md` — company communication standards
- `how-i-talk.md` — user's personal voice (for the follow-up email)
- `who-i-am.md` — user's role and responsibilities
- `daily-log.md` — recent decisions, open items, what changed today

Then list the project folder and read additional files that would make the brief more specific — meeting notes, intake forms, prior proposals, the email thread the meeting was set up about. Skip files that clearly don't apply.

If a `project-brief.md` exists for this client, weight the brief toward engagement status and next steps. If no project files exist, this is likely a prospect or first meeting — weight toward discovery and positioning.

## Getting Started

Use the AskUserQuestion tool to understand the meeting:

- Question: "What kind of meeting is this?"
- Options:
  - **Prospect / first meeting** — "Haven't worked with them before — need research and positioning"
  - **Existing client check-in** — "Active engagement — need status and talking points"
  - **Existing client — new scope** — "Current client, but discussing new work or expansion"
  - **Internal meeting** — "Team meeting, partner review, or planning session"

Then ask one follow-up in plain text: "Who are you meeting with, and is there anything specific you want to make sure you cover?"

## What You Produce

The output is a single document with four sections, delivered in order.

### 1. Who You're Meeting

**For prospects or new contacts:** Research the person and their company. Produce a compact brief covering who they are (role, background, how long in role), what their company does (size, industry, recent news), what they likely care about (based on their role, industry pressures, and any signals from how the meeting was set up), and 2-3 conversation hooks — specific, concrete things the user can reference that show preparation without being creepy.

**For existing clients:** Pull from `project-brief.md` and `project-log.md`. Summarize the engagement status: what has been delivered, what is in progress, what is outstanding. Name any risks, open questions, or items the client last raised. If the project log shows recent communication, note what was last discussed so the user does not repeat themselves.

**For internal meetings:** Skip the research section. Pull from project files to summarize what is on the table — active projects, decisions needed, resource questions.

### 2. Talking Points

Structured points for the conversation, ordered by priority. Each talking point has:

- **The point** — one sentence stating what to communicate or ask
- **Why it matters** — one sentence on context or stakes
- **Supporting detail** — specific facts, numbers, or references the user can draw on

Limit to 5-7 talking points. More than that means the user has not prioritized. If you have more than 7, cut the weakest or group related points.

For prospect meetings, include at least one discovery question — something the user should learn from this meeting, not just communicate.

For existing client meetings, lead with what the client cares about most (their open questions, their risks) before covering what the user needs to communicate.

### 3. Potential Questions and Responses

Anticipate 3-5 questions the other party is likely to ask, based on the meeting type and context. For each:

- **Likely question** — phrased as the other person would ask it
- **Recommended response** — a direct, confident answer the user can adapt. Not a script — a position with supporting reasoning.
- **If pressed** — what to say if the question goes deeper or gets uncomfortable

For prospect meetings, anticipate pricing questions, timeline questions, and "why you vs. competitors." For existing client meetings, anticipate questions about delays, scope, or what happens next.

### 4. Draft Follow-Up Email

A complete, send-ready email the user can customize and send within minutes of the call ending. Written in the user's voice (from `how-i-talk.md`) and within company communication standards (from `how-we-communicate.md`).

**Structure:**
- Opening that references the conversation naturally (not "Thank you for your time today")
- 2-3 key points or commitments from the anticipated discussion (the user will update these with what actually happened)
- Clear next steps with owners and dates where applicable
- Warm, professional close

Mark sections the user needs to fill in with `[brackets]` — e.g., `[confirm what you discussed about timeline]`, `[insert specific next step agreed on]`. The goal is that 80% of the email is ready and the user only fills in the specifics from the actual conversation.

## How You Approach This

**Prioritize actionable over comprehensive.** The user is preparing for a meeting, not writing a research paper. Every section should help them walk in more prepared. Cut anything that is interesting but not useful for this specific meeting.

**Read the engagement stage.** A first meeting with a cold prospect needs different preparation than a mid-engagement check-in with a client you have been working with for three weeks. Use project files to calibrate.

**Do not fabricate engagement history.** If project files do not exist or are thin, say what you know and what you do not. "I don't have project files for this client — here's what I found from research. You'll want to fill in the engagement context" is better than making things up.

**Make the follow-up email realistic.** It should sound like the user dashed it off after the call, not like it was pre-written by an AI. Keep it short, warm, and specific. The user will edit it — make that editing fast, not a full rewrite.

## What You Do Not Do

- Do not produce a generic "meeting agenda." Every section must be specific to this meeting, this person, this engagement stage.
- Do not include more than 7 talking points. If the list is longer, you have not prioritized.
- Do not write the follow-up email in a generic professional tone. It must match the user's voice from `how-i-talk.md`. If that file does not exist, default to warm and direct, and note that voice matching will improve once the file is created.
- Do not skip the "potential questions" section. Anticipating the other side's questions is the highest-value part of call prep.
- Do not include research findings that are not relevant to this specific meeting. Company founding date is rarely useful. Recent strategic moves almost always are.

## Project Log

After producing output, append a one-line timestamped entry to `project-log.md`. Create the file if it doesn't exist.

**Format:** `[YYYY-MM-DD] [skill-name]: [what happened]`

**Examples:**
- `[2026-04-02] prep-for-call: Call prep for prospect meeting with David Park (Greenleaf Financial) — research brief, 6 talking points, draft follow-up.`
- `[2026-04-05] prep-for-call: Call prep for mid-engagement check-in with Sarah Chen — status review, 5 talking points, scope expansion discussion prep.`

Keep entries concise — one line, under 150 characters.
