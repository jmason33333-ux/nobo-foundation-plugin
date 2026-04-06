# Phase 4: Project Instructions Setup

After writing all files (including CLAUDE.md and project-brief.md), set up the project instructions so Claude loads context automatically in every session.

## Why This Matters

In Cowork, CLAUDE.md files in the project folder don't auto-load the way they do in Claude Code. The project instructions field in Cowork project settings IS the CLAUDE.md equivalent. Without this step, Claude starts every new session blank — no context, no voice matching, no standing rules.

## Generate the Instructions Block

Read the `CLAUDE.md` file you just wrote to the project folder. Use its **full content** as the project instructions block. Do not summarize or abbreviate.

**Prepend the profile identifier at the top:**

```
My Claude profile: [name-slug]
```

The full block should include:
- Profile identifier line (for future /new-project user identification)
- The identity line ("You are assisting [name], [role] at [company]")
- The project section (team member, client, engagement type)
- Context files section with all file references
- Session start / session continuity instructions
- Output rules (voice matching, format preferences, file output)
- Standing rules (project brief reference, no fabrication, clarification policy)
- Any custom behavioral rules from the company CLAUDE.md template

If degraded mode (no personal profile), the CLAUDE.md already omits the personal profile section and voice-matching rules. No additional stripping needed.

## Walk the User Through Pasting

**Script:**

> "One more step that makes a big difference — this tells Claude to load your context automatically every time you open this project."

Display the generated instructions block in a code fence so it's easy to copy.

> "Here's how to add it:"
>
> **Step 1:** Click the **Project settings** icon (gear icon next to the project name in the sidebar)
>
> **Step 2:** Find the **Project Instructions** field
>
> **Step 3:** Paste the text above into that field
>
> **Step 4:** Click **Save**
>
> "Takes 10 seconds. After this, every session in this project starts with full context — you never have to remind Claude who you are or what this project is about."

Wait for confirmation before showing the final summary.

## If the User Skips

> "No problem — the context files are still in the folder either way. You can add project instructions anytime from the project settings."

Proceed to the confirmation message.

## Confirmation Message

> "You're set. Here's what's loaded:"
> - [Company name] context
> - [User's name]'s profile *(or "Using company context only — personal profile not found" if degraded)*
> - Project brief: [client name] — [engagement type]
> - Project instructions: configured *(or "skipped — add anytime from project settings")*
>
> "Start delegating. Try describing what you need, or check your delegation guide for ideas."

**If the project brief includes specific next steps** (like a kickoff date or a first deliverable), add a pointed recommendation:

> "Your first move is probably [specific action based on brief] — want me to help with that?"
