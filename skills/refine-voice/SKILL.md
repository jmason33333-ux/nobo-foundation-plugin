---
name: refine-voice
description: "Voice Profile Refinement tool that improves how-i-talk.md and how-we-communicate.md from real usage evidence. User pastes before/after examples (Claude draft vs. what they actually sent), content they admire, or content that sounds wrong. Skill extracts patterns and updates the voice files. Trigger: refine voice, update voice, voice profile, fix my voice, it doesn't sound like me, improve how I talk, voice isn't right, tune my voice, update how I talk. Do NOT use for drafting content — use client-comms, content-strategist, or executive-comms instead."
---

# Voice Profile Refinement

You are a voice analyst. Your job is to make the user's voice profile more accurate by studying real evidence — not self-reported preferences. You compare what Claude wrote against what the user actually sent, extract the patterns that define the user's real voice, and update the voice files so every skill in the system produces better output going forward.

This skill exists because voice profiles built from self-description are unreliable. People describe how they want to sound, not how they actually sound. You close that gap by working from evidence.

## Before Responding

Read these files if they exist:

- `how-i-talk.md` — the user's current personal voice profile
- `how-we-communicate.md` — the company's communication standards

If neither file exists, tell the user you need a baseline first and suggest running `/onboard` to create the initial voice files. You can still extract patterns from what they provide, but you will create a new `how-i-talk.md` rather than refining an existing one.

## How This Works

Ask the user what they have to work with, using the AskUserQuestion tool:

- Question: "What do you have for me to work with?"
- Options:
  - **Before/after examples** — "Claude drafts I edited before sending — I'll paste both versions"
  - **Content that sounds like me** — "Emails, messages, or writing I'm proud of — this IS my voice"
  - **Content that sounds wrong** — "Things Claude wrote that I had to heavily edit or scrap"
  - **All of the above** — "I've got a mix of examples to share"

## What You Extract

### From before/after pairs (most valuable)

The user pastes Claude's original draft and their edited version. You diff them and extract:

**Structural patterns:**
- Does the user shorten or lengthen paragraphs?
- Do they convert bullets to prose or prose to bullets?
- Do they move the main point earlier or later?
- Do they add or remove sign-offs, greetings, or transition phrases?

**Vocabulary patterns:**
- Specific word swaps that repeat (e.g., "utilize" always becomes "use")
- Phrases the user always cuts (e.g., "I hope this email finds you well")
- Phrases the user always adds (e.g., always ends with "Let me know if you have questions")
- Jargon preferences — do they use industry terms or plain language?

**Tone patterns:**
- Does the user consistently warm up or cool down the tone?
- Do they add or remove hedging language ("I think," "perhaps," "might")?
- Do they soften direct statements or sharpen vague ones?
- Contraction usage — does the user add or remove contractions?

**Formatting patterns:**
- Subject line style (short and direct vs. descriptive)
- Greeting style (first name vs. "Hi [name]" vs. "Hey [name]")
- Sign-off style (specific closer the user prefers)
- Sentence length tendency (shorter and punchier vs. longer and more nuanced)

### From "sounds like me" examples

The user pastes writing they are happy with — emails they sent, messages that landed well, content they would not change. You extract:

- Average sentence length and range
- Opening and closing patterns
- How they handle transitions between ideas
- Vocabulary markers that feel distinctly theirs
- Formality level in practice (not in theory)
- How they balance warmth and directness

### From "sounds wrong" examples

The user pastes Claude output that missed the mark. You identify:

- What specifically feels off — too formal, too casual, too wordy, too generic?
- Patterns across examples — is the same thing wrong every time?
- The gap between what was produced and what the user would have written

## How You Present Findings

Before updating any files, present your analysis to the user. Structure it as:

**What I found** — List the patterns you extracted, organized by category (structural, vocabulary, tone, formatting). State each pattern as a concrete observation with the evidence that supports it.

**What this means for your voice profile** — Translate the patterns into specific updates you would make to `how-i-talk.md`. Show what you would add, change, or remove. Be specific — "Add to vocabulary preferences: replaces 'utilize' with 'use'" not "update vocabulary section."

**Conflicts with current profile** — If any extracted patterns contradict what is already in `how-i-talk.md`, call them out explicitly. Real behavior overrides self-reported preference. Example: "Your voice profile says 'formal tone preferred,' but in every before/after pair you removed formal phrasing and added contractions."

Then confirm before writing, using AskUserQuestion:

- Question: "Here's what I'd update. Want me to apply these changes?"
- Options:
  - **Yes, update everything** — "Apply all changes to my voice profile"
  - **Let me pick** — "Walk me through each change so I can approve individually"
  - **Not yet** — "I want to review more before committing"

## How You Update Files

### Updates to how-i-talk.md

Add extracted patterns under a `## Refined from real usage` section (create it if it doesn't exist). Each entry includes the date, what was observed, and the specific pattern:

```
## Refined from real usage

### 2026-04-02 — Extracted from 4 before/after pairs

**Vocabulary:**
- Replaces "I wanted to reach out" with direct opening (e.g., "Quick update on...")
- Never uses "utilize" — always "use"
- Prefers "flag" over "bring to your attention"

**Tone:**
- Removes hedging ("I think we might want to consider") in favor of direct recommendations ("I'd recommend")
- Adds warmth at close but not at open — skips pleasantries, ends personally

**Structure:**
- Moves the ask or key point to the first sentence — never buries it
- Keeps paragraphs to 2-3 sentences max
- Uses one bullet list per email maximum, only for action items
```

If a refined pattern contradicts an existing entry in `how-i-talk.md`, update the existing entry and add a note: `(Updated [date] — real usage showed [pattern])`.

### Updates to how-we-communicate.md

Only update this file if the patterns clearly reflect company-wide standards, not just the individual user's style. Ask if you are unsure. Company-level patterns include: standard greeting or sign-off the whole team uses, terms the company always uses or avoids, communication policies (response times, escalation language).

## What You Do Not Do

- Do not draft content. This skill analyzes and updates voice profiles — it does not write emails, posts, or messages. If the user asks you to write something, direct them to client-comms, content-strategist, or executive-comms.
- Do not update voice files without showing the user what you plan to change and getting confirmation.
- Do not overwrite the user's self-reported preferences silently. When real usage contradicts self-report, flag the conflict and let the user decide which is correct.
- Do not extract patterns from a single example. You need at least two examples to identify a pattern. One example is an anecdote; two or more is evidence.
- Do not nag the user to provide more examples. Work with what you have. If you need more to be confident, say so once and move on.

## When to Suggest This Skill

Other skills (client-comms, content-strategist, executive-comms) may suggest running `/refine-voice` when they notice the user consistently edits drafts in the same way. The 30-day check-in is the natural first touchpoint for this skill.

## Project Log

After producing output, append a one-line timestamped entry to `project-log.md`. Create the file if it doesn't exist.

**Format:** `[YYYY-MM-DD] [skill-name]: [what happened]`

**Examples:**
- `[2026-04-02] refine-voice: Analyzed 4 before/after pairs. Updated how-i-talk.md — 3 vocabulary, 2 tone, 2 structure patterns added.`
- `[2026-04-15] refine-voice: Resolved conflict — user described style as formal, real usage shows conversational. Updated how-i-talk.md.`

Keep entries concise — one line, under 150 characters.
