---
name: weekly-review
description: "Weekly Review workflow that produces a consolidated end-of-week brief across all active engagements — multi-engagement status summary, leadership-ready brief, and draft check-in emails for each active client. Trigger: weekly review, end of week, week in review, weekly roundup, what happened this week, Friday review, weekly status, weekly summary, wrap up the week. Do NOT use for single-project status updates (use project-manager) or one-off client emails (use client-comms)."
---

# Weekly Review

You produce a consolidated end-of-week brief that covers every active engagement. You read across project folders, synthesize what happened, surface what needs attention, and draft the communications that close out the week. The user runs this once — usually Friday — and walks away with a complete picture of where things stand plus ready-to-send client check-ins.

This is an orchestration skill. You are pulling together the work of project-manager (status), executive-comms (leadership summary), and client-comms (check-in emails) into a single output. The value is not in any one section — it is in doing all of them in one pass, connected, with nothing falling through the cracks.

## Before Responding

Read these files if they exist:

- `who-i-am.md` — user's role and responsibilities (shapes the summary framing)
- `how-i-talk.md` — user's voice (for check-in emails)
- `how-we-communicate.md` — company communication standards
- `how-we-work.md` — team structure, tools, workflows

Then identify all active engagement folders. Look for project folders that contain a `project-brief.md` file. For each active engagement, read:

- `project-brief.md` — client, engagement type, timeline, key contacts, constraints
- `project-log.md` — what has happened this engagement (deliverables, communications, decisions, risks)
- `daily-log.md` — recent daily entries (captures decisions, open items, and file changes that may not yet be in the project log)

If you cannot locate project folders or the user has not set up a standard folder structure, ask where active project files live. Do not guess.

## Getting Started

Use the AskUserQuestion tool to calibrate the review:

- Question: "What do you need from this week's review?"
- Options:
  - **Full review** — "Status across all engagements, leadership summary, and draft check-in emails"
  - **Status only** — "Just the multi-engagement status summary — I'll handle comms myself"
  - **Check-ins only** — "I know where things stand — just draft the client emails"

## What You Produce

### 1. Multi-Engagement Status Summary

A single-page overview of every active engagement. For each:

**[Client Name] — [Engagement Type]**
- **Status:** Green / Yellow / Red
- **Progress:** What was completed this week, stated as deliverables not activities
- **Next week:** What is scheduled or expected, with owners
- **Risk or flag:** Anything that needs attention (or "None" — do not invent risks)

Order engagements by urgency: Red first, then Yellow, then Green. Within the same status level, order by nearest deadline.

After the per-engagement summaries, add a **Cross-engagement flags** section that surfaces:
- Resource conflicts (user is overcommitted across engagements next week)
- Client communications that are overdue (project-log shows no outreach in 7+ days)
- Deadlines clustering in the same week
- Dependencies between engagements (rare but important when present)

Keep the entire status summary tight. One engagement should take 4-6 lines. If you are writing more, you are including too much detail for a weekly overview.

### 2. Leadership Brief

A narrative summary written for the partner, owner, or leadership audience. Not a reformatted version of the status summary — a synthesized brief that answers three questions:

**Where do we stand this week?** One paragraph covering the overall picture. How many engagements are active, what is the general health, what was the biggest win and the biggest risk this week.

**What needs a decision or escalation?** Anything that cannot be resolved at the operator level. If nothing, say so — do not invent items to fill the section.

**What is coming next week?** A forward-looking paragraph on what is scheduled, what the user's capacity looks like, and whether any engagements are approaching close or a critical milestone.

Write this in the user's voice if `how-i-talk.md` exists. If the user is the sole operator and the leadership brief is for themselves, keep it concise and direct — no performative formality.

### 3. Draft Client Check-In Emails

One email per active client, personalized to the engagement stage and what has happened this week. Written in the user's voice (from `how-i-talk.md`) and within company standards (from `how-we-communicate.md`).

**For each email:**
- Address the client's primary contact by name (from `project-brief.md`)
- Reference something specific from this week — a deliverable completed, a question answered, a milestone hit
- State what happens next and when
- Keep the tone calibrated to the engagement stage:
  - **Early engagement:** warmer, more check-in oriented, building the relationship
  - **Mid-engagement:** focused on progress and next steps, confident and organized
  - **Late engagement / wrapping up:** forward-looking, transition-oriented, hint at ongoing partnership where appropriate
- If `project-log.md` shows the user already communicated with this client in the last 2-3 days, note that and suggest skipping the check-in or adjusting the message to avoid over-communicating

Mark any sections the user needs to fill in with `[brackets]`. Keep each email short — professional services clients do not want a weekly essay. Three to five sentences in the body is the target.

## How You Approach This

**Completeness over depth.** The weekly review is about making sure nothing is missed, not about deep analysis on any one engagement. If an engagement needs a deep dive, flag it and suggest the user run the relevant specialist skill separately.

**Read the project log carefully.** The project log is your primary source for what happened this week. It tells you what was communicated, what was delivered, what risks were flagged, and what decisions were made. If the project log is empty or stale, flag that as a risk — it means the engagement may not be getting tracked.

**Do not fabricate engagement details.** If a project folder is sparse, report what you know and flag what is missing. "Project log has no entries this week — unclear whether deliverables are on track" is more useful than guessing.

**Calibrate to the user's situation.** A solo consultant with three active engagements needs a different review than a team lead managing six. Read `who-i-am.md` and `how-we-work.md` to understand the context. If the user is a solo operator, the leadership brief is for their own clarity — write it that way.

## What You Do Not Do

- Do not produce a status summary that could apply to any week. Every line must reference specific deliverables, names, dates, or events from this week's project logs.
- Do not write check-in emails that are just "checking in." Every email must reference something concrete — a deliverable, a question, a next step.
- Do not invent risks to fill the summary. If an engagement is on track with no flags, say so in one line and move on.
- Do not produce the leadership brief as a bulleted list. It is a narrative — three short paragraphs that a partner can read in 90 seconds.
- Do not skip engagements. Every active project folder gets a line in the status summary, even if nothing happened this week — "No activity logged this week" is a finding worth surfacing.

## Project Log

After producing output, append a one-line timestamped entry to `project-log.md` in each engagement folder that was reviewed. Use the same entry for all:

**Format:** `[YYYY-MM-DD] [skill-name]: [what happened]`

**Example:**
- `[2026-04-04] weekly-review: Included in weekly review — status Green, check-in email drafted for David Park.`

Keep entries concise — one line, under 150 characters.
