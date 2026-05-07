---
name: refresh-context
description: >
  Pulls the latest company and personal context from the team's shared Google
  Drive folder into the current project, with per-file conflict handling.
  Slim maintenance counterpart to /new-project — refreshes the six context
  files only, never touches project-brief, project-log, or CLAUDE.md. Use
  when someone says "refresh context", "pull latest from Drive", "sync
  context", "my voice changed", "update context from Drive", or after running
  /refine-voice in a different project. Also runs cleanly as a scheduled
  weekly task. Requires Google Drive MCP connector and completed /onboard.
version: 0.1.0
---

# Refresh Context

Pull the latest company and personal context from Drive into this project. This is the maintenance command for an already-set-up project — `/new-project`'s Phase 1 + Phase 2 only, no questions, no project files written, no CLAUDE.md changes. Run it manually after `/refine-voice` updates the canonical profile, or schedule it weekly so context drift never accumulates.

## Critical Rules

- **Tone:** Confident, fast, low-ceremony. This is a maintenance command, not a flow. No narrating, no padding, no welcome paragraph. Speak only on conflicts or completion.
- **Never use these words:** "prompt", "context window", "token", "MCP"
- **Silent on no-op refreshes.** If every file already matches Drive, the only output is a one-line confirmation. Do not summarize what didn't change.
- **Never touch project-brief.md or project-log.md.** These are user-specific working files, written and maintained per-project. They are out of scope for this skill — even if Drive somehow has versions of them.
- **Never write CLAUDE.md.** Only `/new-project` writes that file. This skill assumes it already exists.
- **Six files only.** The scope is the three company context files and the three personal context files. Anything else in the project folder (`theme/`, `notes/`, `communications/`, `calendar/`, etc.) is untouched.
- **Conflicts are the user's call.** When local and Drive differ, prompt — do not silently overwrite. The default option is "overwrite with Drive" because that's what the user usually wants when they explicitly trigger a refresh, but the user always gets to decide per file.

## Phase Overview

| Phase | What happens | User sees |
|---|---|---|
| 0 | Identify user from Global Instructions or ask | Nothing (unless asking name) |
| 1 | Fetch company context + profile from Drive | Nothing |
| 2 | Compare each file against local copy and write/prompt/skip | Conflict prompts (only if any) + one-line completion |

## Phase 0: User Identification (silent)

Search the current conversation context for:

```
My Claude profile: [name-slug]
```

**If found:** Extract `[name-slug]`. Store it. Proceed to Phase 1 silently.

**If not found:** Ask one question:
> "Quick — what's your name? I need it to find your profile in Drive."

Normalize to slug: lowercase, hyphens for spaces, remove apostrophes. "Sarah Chen" → `sarah-chen`.

## Phase 1: Context Fetch from Drive (silent)

### Step 1: Fetch company context (3 calls)

Use `google_drive_search` then `google_drive_fetch`:

1. `What Is [Company]` → store as `company_identity`
2. `How We Communicate` → store as `company_voice`
3. `How We Work` → store as `company_operations`

**If any not found:** "I can't find your team's shared context in Google Drive. The shared folder may not be shared with your Google account, or the connection may have expired. Reconnect Drive (sidebar → Customize → Connectors → Google Drive) and try again." Stop.

Extract the company name from `What Is` so the local company-context filename matches the existing convention (`what-is-[company-slug].md`, lowercase with hyphens).

### Step 2: Fetch individual profile (1 call)

Search Drive for `[name-slug] — Claude Profile` using `google_drive_search`.

**Result handling (same defensive pattern as `/new-project`):**

| Matches returned | Action |
|---|---|
| 0 results | Skip personal-file refresh; report at end ("Profile not found in Drive — run `/onboard` if this is unexpected") |
| 1 result | Fetch content, parse into three sections |
| Multiple, with one exact title match | Fetch the exact match. Note duplicates at completion. |
| Multiple, no exact match | Stop and disambiguate via AskUserQuestion (same prompt as `/new-project` Phase 1 Step 2) |

**If found and content fetched, parse the combined profile:**
1. Strip doc title if first line matches `[Full Name] — Claude Profile`
2. Split on `---` separators
3. Section 1 → `who_i_am`, Section 2 → `how_i_talk`, Section 3 → `how_i_work`

If the Doc isn't in the expected three-section format, skip personal-file refresh and surface the issue at completion. Don't try to repair it inline — that's an `/onboard` re-run concern.

## Phase 2: Compare and Write

For each of the six target files, run the per-file comparison below. Track outcomes per file so the completion summary is accurate.

| Order | File | Source |
|---|---|---|
| 1 | `what-is-[company-slug].md` | `company_identity` |
| 2 | `how-we-communicate.md` | `company_voice` |
| 3 | `how-we-work.md` | `company_operations` |
| 4 | `who-i-am.md` | `who_i_am` from profile (skip if profile not found) |
| 5 | `how-i-talk.md` | `how_i_talk` from profile (skip if profile not found) |
| 6 | `how-i-work.md` | `how_i_work` from profile (skip if profile not found) |

### Per-file comparison

For each file, do exactly this:

1. **No local copy?** Write the Drive content. Track as `written` (no prompt — there's nothing to overwrite).
2. **Local exists and matches Drive byte-for-byte?** Skip silently. Track as `unchanged`. (Compare after normalizing trailing whitespace and final newline — those drift harmlessly between editors and aren't worth a prompt.)
3. **Local exists and differs from Drive?** Prompt the user.

### The conflict prompt

Use AskUserQuestion. Show the file name and a one-line indication that they differ. Do not dump diffs into the prompt — the user can run their own diff if they want detail.

- Question: "`[file]` differs from Drive. What do you want to do?"
- Options:
  - **Overwrite with Drive** — "Replace the local file with the Drive version"
  - **Keep local** — "Leave the local file as-is, ignore Drive"
  - **Skip for now** — "Don't touch this file, ask again next refresh"

"Overwrite with Drive" is the recommended default because the user explicitly triggered a refresh. But the user always gets to choose per file — ask once per conflicting file, don't batch.

Track each outcome (`overwritten`, `kept`, `skipped`).

### Filename convention for company file

The company file name uses the company slug from `What Is [Company]` — lowercase with hyphens, matching `/new-project`'s convention. Example: "Monumental Agency" → `what-is-monumental-agency.md`.

If the local project has a company file with a different slug pattern (e.g., the company was renamed since project setup), prefer the existing local filename — overwriting under a new name would orphan the old file and confuse downstream skills. Surface the mismatch at completion: "Note: local company file is `what-is-monumental.md`; Drive context is for `Monumental Agency` — verify the company name didn't change."

## Phase 3: Report

One concise summary at the end. Format:

```
Context refresh complete.
- Updated: [list of files written or overwritten]
- Kept local: [list]
- Skipped: [list]
- Unchanged: [count] file[s]
```

If everything was unchanged:

> "Context already current — nothing to update."

If the personal profile was missing or malformed, append a note:

> "Profile not found in Drive — run `/onboard` if this is unexpected."

If duplicate profile Docs were found in Phase 1 (multiple-with-exact-match case), append:

> "FYI — extra profile Docs matching your name in Drive. Worth cleaning up."

Do not list files that didn't need to change. The point is to be quiet when there's nothing to say.

## Re-run Behavior

This skill is designed to be re-run safely — that's the whole point. Each run is idempotent: matching files are skipped, divergent files prompt, missing files are written. No state to clean up between runs.

If the user runs `/refresh-context` and then immediately runs it again, the second run should produce "Context already current — nothing to update."

## Scheduled Task Pattern

`/refresh-context` is designed to run as a scheduled task in Cowork. The `/new-project` skill (Phase 5) offers to set this up at project creation time — typically weekly, Monday morning.

When running as a scheduled task:
- Phase 0 is silent (the user has been identified before; the scheduled run inherits that context).
- Phase 1 fetches normally.
- Phase 2 conflict prompts still apply — the scheduled run cannot bypass them. If a conflict surfaces during a background run, it surfaces in the project on next open. Treat this as expected behavior, not a failure.
- Phase 3 reports the same way.

## Error Handling

**Drive not connected or connection lost:**

> "I can't reach Google Drive right now — your connection looks expired or disconnected. I need it to pull the latest context.
>
> Quick fix:
> 1. Open the sidebar → Customize → Connectors
> 2. Find Google Drive. If it shows Reconnect, click it. If it's missing, click Enable.
> 3. Once it's back, run `/refresh-context` again."

Stop. No partial writes — if Drive dropped before company context was fully fetched, don't proceed with personal-file refresh either. The next run will pick up where this one stopped.

**File write failure:**

Retry once. If it fails again, surface the file name and the Drive content inline so the user can save manually. Continue with remaining files — one failed write shouldn't abort the rest of the refresh.

**Profile Doc missing or malformed:**

Skip the three personal files (4–6). Continue with the company files (1–3). Report the gap at completion. Do not stop the refresh — most refreshes care about company context too, and stopping on a personal-file issue would block the whole flow.

## What This Skill Does NOT Do

- Does not ask project-setup questions. That's `/new-project`.
- Does not write `CLAUDE.md`. That's `/new-project`.
- Does not write or modify `project-brief.md` or `project-log.md`. Those are user-specific.
- Does not push anything to Drive. That's `/refine-voice` (personal profile only) or manual editing of the Drive Docs (company context).
- Does not run on first project setup. If the project doesn't have CLAUDE.md and the six context files, run `/new-project` instead — `/refresh-context` assumes setup is already done.
- Does not extend to skills, theme, calendar, or any non-context files. The scope is exactly the six context files.
