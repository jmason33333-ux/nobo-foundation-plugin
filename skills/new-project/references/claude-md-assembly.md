# CLAUDE.md Assembly

Use this reference when writing CLAUDE.md during /new-project. CLAUDE.md is always the last file written — it needs the client name and engagement type from the project questions.

## Path 1: Company template exists (preferred)

If `company_claude_template` was fetched from Google Drive:

1. Start with James's company CLAUDE.md as the base
2. Preserve all custom behavioral rules exactly as written
3. Append the project-specific section below
4. Do not duplicate any section that already exists in the template — if the template has `## Output rules`, keep the template version and merge any project-specific rules into it

## Path 2: No company template (fallback)

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
- [Format preference from profile]
- Save output as files in this project folder, not as inline chat responses.
- When context is thin, flag it explicitly rather than filling gaps with assumptions.

## Standing rules

- Reference `project-brief.md` for client-specific details before any client-facing output.
- If the task involves client communication, check `how-we-communicate.md` for tone and `how-i-talk.md` for personal style.
- Do not invent client details, deadlines, or contact information not in `project-brief.md`.
- Ask for clarification before proceeding if the brief is insufficient for the task.
```

## Section always appended (both paths)

Append this to the end of CLAUDE.md regardless of which path was used:

```markdown
## This project

**Team member:** [User's full name] ([role])
**Client:** [client name]
**Engagement type:** [engagement type]
**Project brief:** See `project-brief.md` for full details.

## Session continuity

Before starting work in a new session, read `daily-log.md` if it exists. Start with the most recent entry's **Pick up tomorrow** section to orient. If the user doesn't specify what to work on, reference that list.
```

## Variable Resolution

- `[User's full name]`, `[role]`, `[company name]` — from Phase 1 context fetch
- `[engagement type]`, `[client name]` — from Phase 3 project questions
- `[Format preference]` — from the user's profile `## How I want output structured`:
  - "document I can send directly" → "Create polished documents ready to send."
  - "draft I'll edit" → "Provide editable drafts, not final copy."
  - "bullet points" → "Use structured bullet points unless the task calls for prose."
- `what-is-[company].md` filename — normalized company name (lowercase, hyphens)

## Important

Write CLAUDE.md AFTER Phase 3 completes (it needs client name and engagement type). During Phase 2, write files 1–6 only. CLAUDE.md is the final file write, after project-brief.md.
