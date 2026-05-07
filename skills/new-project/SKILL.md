---
name: new-project
description: >
  Sets up a new Cowork project with full team and personal context. Fetches
  company context and personal profile from Google Drive, writes local context
  files, and asks project-specific questions to create a project brief. Use
  when someone says "new client", "start a project", "new engagement", "new
  matter", "new listing", or is beginning work on a new client. Requires
  Google Drive MCP connector and completed /onboard.
version: 0.3.0
---

# New Project

Set up a Cowork project with full context in under two minutes. Fetch company and personal context from Google Drive, write it locally, ask a few project-specific questions, and generate a project brief. After this runs, every specialist skill works without any additional setup.

## Critical Rules

- **Tone:** Confident warmth. You're direct and move fast, but you're not cold — this person just set up their whole system and now they're starting real work. Be the sharp colleague who makes them feel like the system is already working for them. No narrating. No filler. Every sentence earns its place.
- **Never use these words:** "prompt", "context window", "token", "MCP"
- **Complete all file writes.** Do not skip any file. Write all 7 context files plus the project brief plus CLAUDE.md.
- **Complete Phases 0–4.** Do not stop after Phase 3. Phase 4 (project instructions setup) is required. Phase 5 (the scheduled-refresh offer) always runs but the user is free to decline.
- **Silent phases are silent.** Phases 0–2 happen without visible output.
- **Speed matters.** Every second before they can start delegating is friction.
- **Reference files are authoritative.** Read and follow the reference files in `references/` — they contain the exact phrasing, templates, and logic for each phase.

### Tone Examples

**Wrong (assistant narrating — cold, mechanical):**
> "Setting up your project. I've loaded your team's context and your profile. A few quick questions about this engagement. Who's the client?"

**Right (confident warmth — direct, acknowledges what you know, moves):**
> "TechFab, new ops strategy — got it. Your Acme context and profile are loaded. What I still need:"

**Wrong (re-asking what was already said — feels like a form):**
> "Who's the client?" *(when the user just said "client called TechFab Inc.")*

**Right (skips what's known, stays warm):**
> "TechFab, got it. Who are the key people on their side and yours?"

**Wrong (wrapping up like a receipt):**
> "You're set. Here's what's loaded for this engagement: Acme Consulting company context, Sarah Chen personal profile, Project brief: TechFab Inc. — New client engagement, ops strategy."

**Right (wrapping up like a colleague who's already thinking ahead):**
> "You're loaded — Acme context, your profile, TechFab brief. Your first move is probably that kickoff prep with Zach — want me to help with that?"

## Phase Overview

| Phase | What happens | User sees | Reference file |
|---|---|---|---|
| 0 | Identify user from Global Instructions or ask | Nothing (unless asking name) | — |
| 1 | Fetch company context + profile from Drive | Nothing | — |
| 2 | Write 6 context files locally | Nothing | — |
| 3 | Ask project questions, write brief + CLAUDE.md | Questions + confirmation | `references/project-setup-questions.md` |
| 4 | Generate project instructions, walk user through paste | Instructions + paste steps | `references/project-instructions-setup.md` |
| 5 | Offer to schedule weekly `/refresh-context` task | Schedule prompt + setup walkthrough or skip note | — |

**Phases 0–4 must complete.** Phase 5 always runs but the user can decline the schedule offer.

## Phase 0: User Identification (silent)

Search the current conversation context for:

```
My Claude profile: [name-slug]
```

**If found:** Extract `[name-slug]`. Store it. Proceed to Phase 1 silently.

**If not found:** Ask one question:
> "Quick question before we start — what's your name?"

Normalize to slug: lowercase, hyphens for spaces, remove apostrophes. "Sarah Chen" → `sarah-chen`.

## Phase 1: Context Fetch from Drive (silent)

### Step 1: Fetch company context (3 calls)

Use `google_drive_search` then `google_drive_fetch`:

1. `What Is [Company]` → store as `company_identity`
2. `How We Communicate` → store as `company_voice`
3. `How We Work` → store as `company_operations`

**If any not found:** "I can't find your team's shared context in Google Drive. This usually means the shared folder hasn't been shared with your Google account yet. Ask your team lead to send you the Drive sharing link, then try `/new-project` again." Stop.

**If all fetched, extract silently:**
- Company name (from `What Is` doc)
- Company vertical (agency, law firm, consultancy, real estate — infer from content)
- Team tools (from `How We Work`)
- Client terminology ("clients", "matters", "accounts", "listings", "engagements")

### Step 2: Fetch individual profile (1 call, with defensive check)

Search Drive for `[name-slug] — Claude Profile` using `google_drive_search`.

**Result handling (defensive — handle all four cases explicitly):**

| Matches returned | Action |
|---|---|
| 0 results | Enter degraded mode (see below) |
| 1 result | Fetch content, parse, proceed |
| Multiple, with one exact title match | Fetch the exact match. Mention duplicates in the wrap-up at end of Phase 4 ("FYI — I noticed extra profile Docs matching your name. Worth cleaning up.") |
| Multiple, no exact match | Stop and disambiguate (see below) |

**Disambiguation prompt (when multiple non-exact matches exist):**

> "I found a few profiles in your Drive folder that match `[name-slug]`:
> 1. `[Title 1]` — last updated [date]
> 2. `[Title 2]` — last updated [date]
> 3. `[Title 3]` — last updated [date]
>
> Which one is yours? Or cancel so you can clean these up first."

Use AskUserQuestion. Once the user picks, fetch and proceed with that Doc.

**Why stop instead of guess:** Loading the wrong profile silently corrupts every downstream specialist skill — wrong voice, wrong role context, wrong tools. Thirty seconds of friction here saves hours of debugging unexplained output later.

**If found and content fetched, parse the combined profile:**
1. Strip doc title if first line matches `[name] — Claude Profile`
2. Split on `---` separators
3. Section 1 = `who_i_am`, Section 2 = `how_i_talk`, Section 3 = `how_i_work`

Extract: full name, role, format preferences, tools.

### Step 3: Fetch company CLAUDE.md template (1 call)

Search for `CLAUDE` or `CLAUDE.md` in Drive.

**If found:** Store as `company_claude_template`.
**If not found:** No error — fallback schema will be used.

## Phase 2: Local File Write (silent)

Write files 1–6 to the project folder. CLAUDE.md is written later (after Phase 3).

| Order | File | Source |
|---|---|---|
| 1 | `what-is-[company].md` | `company_identity` — passthrough |
| 2 | `how-we-communicate.md` | `company_voice` — passthrough |
| 3 | `how-we-work.md` | `company_operations` — passthrough |
| 4 | `who-i-am.md` | `who_i_am` from profile — skip if degraded |
| 5 | `how-i-talk.md` | `how_i_talk` from profile — skip if degraded |
| 6 | `how-i-work.md` | `how_i_work` from profile — skip if degraded |

Company name in filename: lowercase with hyphens. "Monumental Agency" → `what-is-monumental-agency.md`.

## Phase 3: Project Questions + File Write

**Read `references/project-setup-questions.md` now.** It contains the pre-parse logic, exact question phrasing by vertical, connector-aware timeline handling, and the project brief schema.

Follow that reference file for:
1. Pre-parsing the user's opening message (and any linked docs/boards)
2. Welcome message (adapted to what's already known)
3. Questions Q1–Q8 (skip any already answered — typically 4–6 questions after pre-parse)
   - Q1: Client name
   - Q2: Engagement type
   - Q3: Objective — what does success look like
   - Q4: Your deliverables — what are you personally responsible for
   - Q5: Key contacts
   - Q6: Timeline + milestones
   - Q7: Constraints + scope boundaries
   - Q8: Catch-all (conditional)
4. Writing `project-brief.md`

**Then write CLAUDE.md** using the assembly logic in `references/claude-md-assembly.md`.

## Phase 4: Project Instructions Setup

**Read `references/project-instructions-setup.md` now.** It contains the full instructions block generation, walkthrough script, and confirmation message.

**Do not skip this phase.** This is what makes Claude load context automatically in every future session. Without it, the context files exist but Claude doesn't know to read them.

## Phase 5: Schedule Offer for `/refresh-context`

After Phase 4's confirmation message, offer to set up a weekly refresh task. This keeps the project's context files in sync with the canonical Drive copies as voice and process drift over time — without the user having to remember to run `/refresh-context` manually.

Use AskUserQuestion:

- Question: "Want me to set up a weekly refresh task so this project pulls the latest Drive context every Monday morning?"
- Options:
  - **Yes, weekly Monday morning** — "Set up `/refresh-context` to run every Monday at 7am"
  - **Yes, but pick the day/time** — "I'll tell you when to run it"
  - **No, I'll run it manually** — "Skip the scheduled task; I'll trigger refresh when I need it"

### If "Yes, weekly Monday morning"

Cowork's scheduled-task feature is user-driven, not skill-programmable, so this skill outputs step-by-step instructions for setting it up. Walk the user through:

> "Quick setup — about 30 seconds:
>
> **Step 1:** Open this project's settings (gear icon in the sidebar)
>
> **Step 2:** Find **Scheduled Tasks** → click **New**
>
> **Step 3:** Skill: select `/refresh-context` from the list
>
> **Step 4:** Schedule: every **Monday at 7:00 AM**
>
> **Step 5:** Save.
>
> Done. Every Monday morning, this project will pull the latest company and personal context from Drive. If anything diverges from your local edits, it'll prompt you when you next open the project."

### If "Yes, but pick the day/time"

Ask follow-up questions for day-of-week and time, then walk through the same UI steps with the user's chosen values:

> "Got it. What day of the week works?"

After they answer, ask:

> "And what time?"

Then output the same step-by-step setup, substituting the user's day and time at Step 4.

### If "No, I'll run it manually"

Skip the setup. Add this line to the wrap-up:

> "You can run `/refresh-context` anytime context drifts — voice updates from `/refine-voice`, company doc edits, anything. The 30-day check-in is the natural prompt."

### Wrap-up

After Phase 5 completes (whether the user set up a schedule or declined), Phase 4's confirmation message has already been shown. Don't repeat it. If the schedule was set up, append:

> "Schedule set. Project's good to go."

If skipped, no extra line — the wrap-up from Phase 4 already covered it.

## Re-run Behavior

If `/new-project` is run in a folder that already has context files:

> "This project already has context loaded. What would you like to do?"

Use AskUserQuestion:

- **Start fresh** — "Replace all context files (company + personal). I'll re-run the project questions too."
- **Refresh context from Drive** — "Pull the latest company and personal context from Drive. Keep the project brief and CLAUDE.md as-is. Prompt before overwriting any local file that differs from Drive."
- **Cancel** — "Don't change anything"

### "Start fresh" flow

Run all phases as normal — Phase 1 fetches, Phase 2 writes (silent overwrite of context files is intentional in this branch — the user explicitly asked to start over), Phase 3 re-runs project questions, Phase 4 regenerates project instructions, Phase 5 re-offers the schedule.

### "Refresh context from Drive" flow

Run only Phase 1 (fetch from Drive) and a conflict-aware version of Phase 2. Do not re-run project questions, do not regenerate CLAUDE.md, do not re-prompt for the schedule.

For each of the six context files (3 company, 3 personal):

1. **No local copy?** Write the Drive content. No prompt.
2. **Local exists and matches Drive byte-for-byte?** Skip silently.
3. **Local exists and differs from Drive?** Prompt via AskUserQuestion:
   - Question: "`[file]` differs from Drive. What do you want to do?"
   - Options:
     - **Overwrite with Drive** — "Replace the local file with the Drive version"
     - **Keep local** — "Leave the local file as-is, ignore Drive"
     - **Skip for now** — "Don't touch this file, ask again next refresh"

This is the same conflict-handling pattern `/refresh-context` uses — by design. The two skills should behave identically when refreshing the same six files. Mention `/refresh-context` at the end as a lighter-weight alternative for future runs:

> "For routine refreshes, `/refresh-context` does this same per-file refresh without going through `/new-project`'s re-run prompt."

## Degraded Mode

If individual profile not found in Drive:

> "I couldn't find your personal profile in Google Drive. This project will use company context only — your personal voice and preferences won't be loaded. To fix this, run `/onboard` and make sure your profile saves to the shared folder."

In degraded mode: skip personal files (4–6), CLAUDE.md omits profile section and voice-matching rule.

If BOTH profile identifier missing from Global Instructions AND profile doc not found:

> "It looks like you haven't set up your profile yet. Run `/onboard` first — takes about 10 minutes. Then come back and run `/new-project`."

Stop.

## Error Handling

**Drive not connected or connection lost:**

> "I can't reach Google Drive right now — your connection looks expired or disconnected. I need it to load your team's context and your profile.
>
> Quick fix:
> 1. Open the sidebar → Customize → Connectors
> 2. Find Google Drive. If it shows Reconnect, click it. If it's missing, click Enable.
> 3. Once it's back, run `/new-project` again. Your work so far is saved.
>
> If reconnecting doesn't fix it, ask your team lead — the shared folder may need to be re-shared with your Google account."

Stop.

**Company context not found:** Stop. Do not proceed — the project setup depends on the company context layer.

**File write failure:** Retry once. Company context + CLAUDE.md are critical (tell the user if they fail). Individual files are non-critical in degraded mode.
