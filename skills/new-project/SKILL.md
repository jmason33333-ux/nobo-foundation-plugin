---
name: new-project
description: >
  Sets up a new Cowork project with full team and personal context. Fetches
  company context and personal profile from Google Drive, writes local context
  files, and asks project-specific questions to create a project brief. Use
  when someone says "new client", "start a project", "new engagement", "new
  matter", "new listing", or is beginning work on a new client. Requires
  Google Drive MCP connector and completed /onboard.
version: 0.1.0
---

# New Project

Set up a Cowork project with full context in under two minutes. Fetch company and personal context from Google Drive, write it locally, ask a few project-specific questions, and generate a project brief. After this runs, every specialist skill works without any additional setup.

## Critical Rules

- **Tone:** Efficient, confident, brief. Shorter than /onboard. No explanations of what context files are — the user has been through onboarding.
- **Never use these words:** "prompt", "context window", "token", "MCP"
- **Complete all file writes.** Do not skip any file. Write all 7 context files plus the project brief. Write CLAUDE.md last.
- **Silent phases are silent.** Phases 0-2 happen without any visible output to the user. The first thing they see is the welcome message in Phase 3.
- **Speed matters.** This user is task-focused. Every second before they can start delegating is friction.
- **Take your time with file writes.** Do not skip or abbreviate any file. Write every file completely.

## Phase 0: User Identification (silent)

Search the current conversation context for:

```
My Claude profile: [name-slug]
```

**If found:** Extract `[name-slug]`. Store it. Proceed to Phase 1 silently.

**If not found:** Ask one question:
> "Quick question before we start — what's your name?"

Normalize the answer to a slug: lowercase, hyphens for spaces, remove apostrophes. "Sarah Chen" becomes `sarah-chen`. Store the slug.

## Phase 1: Context Fetch from Drive (silent)

All fetches are silent — the user sees nothing during this phase.

### Step 1: Fetch company context (3 calls)

Use `google_drive_search` to find each document, then `google_drive_fetch` to read it:

1. `What Is [Company]` — store as `company_identity`
2. `How We Communicate` — store as `company_voice`
3. `How We Work` — store as `company_operations`

**If any company doc is not found:** "I can't find your team's shared context in Google Drive. This usually means the shared folder hasn't been shared with your Google account yet. Ask your team lead to send you the Drive sharing link, then try `/new-project` again." Stop.

**If all three are fetched:** Extract key signals silently:
- Company name (from the `What Is` doc)
- Company vertical (agency, law firm, consultancy, real estate — infer from content)
- Team tools (from `How We Work`)
- Client terminology (what the company calls clients — "clients", "matters", "accounts", "listings", "engagements")

### Step 2: Fetch individual profile (1 call)

Search for the Google Doc named `[name-slug] — Claude Profile` in the shared Drive folder. Fetch its content.

**If found:** Parse the combined profile:

1. If the first line matches the pattern `[name] — Claude Profile`, strip it (this is the doc title — it may or may not appear in the fetched content)
2. Split the remaining content on `---` separators
3. Section 1 (before first `---`) is `who_i_am` content
4. Section 2 (between separators) is `how_i_talk` content
5. Section 3 (after second `---`) is `how_i_work` content

Extract from the profile:
- User's full name (from `**Name:**` line)
- User's role (from `**Role:**` line)
- Format preferences (from `## How I want output structured`)
- Tools they use (from `## Tools I use daily`)

**If not found:** Enter **degraded mode** (see section below). Continue without individual context.

### Step 3: Fetch company CLAUDE.md template (1 call)

Search for a Google Doc named `CLAUDE` or `CLAUDE.md` in the shared Drive folder. Fetch its content.

**If found:** Store as `company_claude_template`. This contains James's custom behavioral rules written during the Foundation build.

**If not found:** No error. The skill will generate CLAUDE.md from the fallback schema in the CLAUDE.md Assembly section.

## Phase 2: Local File Write (silent)

Write 7 files to the current project folder. Write in this order — CLAUDE.md last.

| Order | File | Source |
|---|---|---|
| 1 | `what-is-[company].md` | `company_identity` — passthrough |
| 2 | `how-we-communicate.md` | `company_voice` — passthrough |
| 3 | `how-we-work.md` | `company_operations` — passthrough |
| 4 | `who-i-am.md` | `who_i_am` section from profile — skip if degraded mode |
| 5 | `how-i-talk.md` | `how_i_talk` section from profile — skip if degraded mode |
| 6 | `how-i-work.md` | `how_i_work` section from profile — skip if degraded mode |
| 7 | `CLAUDE.md` | Template-based assembly (see next section) |

**Passthrough files (1-6):** Write the fetched content directly. Google Drive MCP returns Google Doc content as markdown natively — no conversion needed.

**Filename convention:** The company name in `what-is-[company].md` is lowercase with hyphens. "Monumental Agency" becomes `what-is-monumental-agency.md`.

**If any file write fails:** Retry once. If it still fails, log the failure. Company context files and CLAUDE.md are critical — tell the user at the end. Individual files are non-critical in degraded mode.

## CLAUDE.md Assembly

### If `company_claude_template` was fetched (preferred path)

Start with James's company CLAUDE.md as the base. Preserve all custom behavioral rules. Then append the project-specific section below. Do not duplicate any section that already exists in the template — if the template has `## Output rules`, keep the template version and merge any project-specific rules into it.

### If no company CLAUDE.md was found (fallback)

Generate from this schema:

```markdown
# CLAUDE.md

You are assisting [User's full name], [role] at [company name].

This is a [engagement type] project for [client name].

## Context files

Read these files for full context. They are loaded in this project folder:

**Company context:**
- `what-is-[company].md` — who [company name] is, what they do, their clients
- `how-we-communicate.md` — brand voice, tone, phrases to use and avoid
- `how-we-work.md` — team structure, tools, workflows, how decisions get made

**Your profile:**
- `who-i-am.md` — your role, responsibilities, current priorities
- `how-i-talk.md` — your personal communication style
- `how-i-work.md` — your tools, format preferences, working style

**This project:**
- `project-brief.md` — client, engagement type, key contacts, timeline, constraints
- `daily-log.md` — rolling 5-day log of what was accomplished, decided, and left open
- `project-log.md` — per-skill activity entries (auto-appended by specialist skills)

## Session start

Before starting work in a new session, read `daily-log.md` if it exists. Start with the most recent entry's **Pick up tomorrow** section to orient. If the user doesn't specify what to work on, reference that list.

## Output rules

- Write in [user's name]'s voice, not generic AI voice. Match the style in `how-i-talk.md`.
- Follow the company's communication standards in `how-we-communicate.md`.
- [Format preference from profile — e.g., "Create polished documents ready to send" or "Provide editable drafts, not final copy"]
- Save output as files in this project folder, not as inline chat responses.
- When context is thin, flag it explicitly rather than filling gaps with assumptions.

## Standing rules

- Reference `project-brief.md` for client-specific details before any client-facing output.
- If the task involves client communication, check `how-we-communicate.md` for tone and `how-i-talk.md` for personal style.
- Do not invent client details, deadlines, or contact information not in `project-brief.md`.
- Ask for clarification before proceeding if the brief is insufficient for the task.
```

### Section always appended (even when using James's template)

```markdown
## This project

**Team member:** [User's full name] ([role])
**Client:** [client name]
**Engagement type:** [engagement type]
**Project brief:** See `project-brief.md` for full details.

## Session continuity

Before starting work in a new session, read `daily-log.md` if it exists. Start with the most recent entry's **Pick up tomorrow** section to orient. If the user doesn't specify what to work on, reference that list.
```

### Variable resolution

- `[User's full name]`, `[role]`, `[company name]` — from Phase 1 context fetch
- `[engagement type]`, `[client name]` — from Phase 3 project questions (write CLAUDE.md after questions are answered)
- `[Format preference]` — from the user's profile `## How I want output structured`. Map: "document I can send directly" becomes "Create polished documents ready to send." / "draft I'll edit" becomes "Provide editable drafts, not final copy." / "bullet points" becomes "Use structured bullet points unless the task calls for prose."
- `what-is-[company].md` filename — normalized company name

### Important: Write CLAUDE.md after Phase 3

CLAUDE.md references the client name and engagement type from the project questions. Write it after Phase 3 completes, not during Phase 2. During Phase 2, write files 1-6 only. Write CLAUDE.md as the final step after `project-brief.md`.

## Phase 3: Project Questions

This is the first thing the user sees.

Read `references/project-setup-questions.md` for the full question templates and vertical-specific phrasing.

**Welcome message:**
> "Setting up your project. I've loaded your team's context and your profile. A few quick questions about this specific [engagement/matter/project]:"

Use the company's client terminology inferred from Phase 1. Agencies say "engagement" or "project." Law firms say "matter." Real estate says "listing" or "transaction." If unclear, use "project."

### Questions (ask one at a time)

Adapt question language to the company's vertical. Use the client terminology inferred from Phase 1 throughout — not just in the welcome message.

**Q1 — Client name** (always asked)
Adapt phrasing to vertical:

| Vertical | Phrasing |
|---|---|
| Agency | "Who's the client?" |
| Law firm | "Who's the client? And if this is litigation, who's on the other side?" |
| Consultancy | "Who's the client?" |
| Real estate | "Who's the buyer or seller?" (or "What's the property?" for listings) |
| Generic | "Who's the client or stakeholder?" |

**Q2 — Engagement type** *(always asked, use AskUserQuestion tool)*
Present using the AskUserQuestion tool. Options adapt based on the company vertical inferred during Phase 1 context fetch:

**Agency:**
- Question: "What kind of engagement is this?"
- Options:
  - **New client engagement** — "First project with this client"
  - **Existing client — new project** — "Additional work for a current client"
  - **Internal initiative** — "Not client-facing, internal team project"

**Law firm:**
- Question: "What kind of matter is this?"
- Options:
  - **New matter** — "New client or new legal issue"
  - **Existing matter — new phase** — "Next stage of an active matter"
  - **Internal project** — "Firm operations, not client work"

**Consultancy:**
- Question: "What kind of engagement is this?"
- Options:
  - **New client engagement** — "First project with this client"
  - **Ongoing retainer work** — "Continuing work under an existing agreement"
  - **Internal initiative** — "Not client-facing, internal team project"

**Real estate:**
- Question: "What kind of work is this?"
- Options:
  - **New listing** — "Property coming to market"
  - **New buyer** — "Buyer representation starting"
  - **Transaction in progress** — "Active deal, under contract or closing"

**Generic (vertical unclear):**
- Question: "What kind of project is this?"
- Options:
  - **New client project** — "First engagement with this client"
  - **Continuation of existing work** — "Next phase or additional scope"
  - **Internal project** — "Not client-facing"

The selection shapes the project-brief.md structure. New engagements get onboarding-oriented fields (relationship context, first impressions). Continuations reference prior work. Internal projects skip client-facing fields.

**Q3 — Key contacts** (always asked)
Adapt phrasing to vertical:

| Vertical | Phrasing |
|---|---|
| Agency | "Who are the key people — on their side and yours? Who's the day-to-day contact, and who signs off?" |
| Law firm | "Who's involved? On their side — client contact, opposing counsel if relevant. On yours — lead attorney and anyone else staffed." |
| Consultancy | "Who are the key stakeholders — on their side and yours? Who's the decision-maker, and who's your day-to-day?" |
| Real estate | "Who's involved? Buyer, seller, agents on the other side, lender, title company — whoever's in play." |
| Generic | "Who are the key people — on their side and yours?" |

**Q4 — Timeline** (always asked)
Adapt phrasing to vertical:

| Vertical | Phrasing |
|---|---|
| Agency | "What's the timeline? Any hard launch dates or client deadlines?" |
| Law firm | "Any court dates, filing deadlines, or statute of limitations I should know about?" |
| Consultancy | "What's the timeline? When does the client expect deliverables?" |
| Real estate | "What's the timeline? Listing date, offer deadline, closing date — whatever's set." |
| Generic | "Any deadlines or timeline I should know about?" |

**Q5 — Constraints** *(always asked, use AskUserQuestion tool, multiSelect: true)*
Present using the AskUserQuestion tool with multiSelect enabled:
- Question: "Any constraints I should know about? Pick all that apply."
- Options:
  - **Compliance / privacy restrictions** — "Limits on what data or content can be used"
  - **Hard deadline** — "Non-negotiable date that can't move"
  - **Budget cap** — "Not-to-exceed or fixed fee in play"
  - **Specific tool or format required** — "Must use a particular platform or file type"

Multiple selections allowed. "Other" captures anything unusual. If the user selects nothing, record "No specific constraints noted" in the brief.

**Q6 — Catch-all** (asked only if prior answers were very brief)
> "Anything else that would be useful context for this project?"

Skip Q6 if answers have been substantive.

### After questions

1. Write `project-brief.md` using the schema in `references/project-setup-questions.md`
2. Write `CLAUDE.md` using the assembly logic above (now that client name and engagement type are known)
3. Display the confirmation message

### Confirmation message

> "You're set. Here's what's loaded for this project:"
> - [Company name] company context
> - [User's name] personal profile *(or "Personal profile unavailable — using company context only" if degraded mode)*
> - Project brief: [client name] — [engagement type]
>
> "You can start delegating. Your team's skills are ready — try describing what you need, or check your delegation guide for sample briefs."

## Re-run Behavior

If `/new-project` is run in a folder that already contains context files:

> "This project already has context loaded. What would you like to do?"
> 1. Start a new project (replaces all context files)
> 2. Refresh context from Drive (keeps project brief, updates company and personal context)
> 3. Cancel

**Option 1:** Overwrite all files. Re-ask project questions. Write new project-brief.md and CLAUDE.md.
**Option 2:** Re-fetch from Drive. Overwrite company and individual context files. Regenerate CLAUDE.md. Keep existing project-brief.md.
**Option 3:** Do nothing.

## Degraded Mode

If the individual profile Google Doc is not found in Drive:

> "I couldn't find your personal profile in Google Drive. This project will use company context only — your personal voice and preferences won't be loaded. To fix this, run `/onboard` and make sure your profile saves to the shared folder."

In degraded mode:
- Skip writing `who-i-am.md`, `how-i-talk.md`, `how-i-work.md`
- CLAUDE.md omits the "Your profile" section and the voice-matching output rule
- The project still works — output will match company voice, not personal voice

If both the profile identifier is missing from Global Instructions AND the profile doc is not found in Drive, the user likely hasn't run `/onboard`:

> "It looks like you haven't set up your profile yet. Run `/onboard` first — it takes about 10 minutes and sets up your personal context. Then come back and run `/new-project`."

Stop.

## Error Handling

### Google Drive not connected
If `google_drive_search` fails on the first call:
> "Google Drive isn't connected yet. You can enable it in the sidebar under Customize, then Connectors, then Google Drive. Once it's connected, run `/new-project` again."

Stop. Do not guide full connector setup — that is `/onboard`'s job.

### Company context not found
Stop. Do not proceed without company context.

### File write failure
1. Retry once
2. If company context file or CLAUDE.md fails: "I had trouble writing some context files. Try creating a new project in a different folder and running `/new-project` again."
3. If individual context file fails: continue (degraded voice matching)
