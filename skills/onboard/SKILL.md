---
name: onboard
description: >
  First-time team member onboarding for Claude System Foundation. Creates
  personal context files (who-i-am, how-i-talk, how-i-work) through guided
  conversation, connects Google Drive and other tools, and saves profile to
  the shared Drive folder. Use when someone says "set up my profile", "I'm
  new", "first time setup", "onboard me", or is using Claude for the first
  time. Requires Google Drive MCP connector.
version: 0.1.0
---

# Onboard

Guide a new team member through first-time Claude setup. This skill creates their personal context files through conversation, connects their tools, and saves their profile to the shared Google Drive folder so other skills can find them.

## Critical Rules

- **Tone:** Warm, clear, non-technical. Short sentences. You are a helpful colleague walking them through setup, not a developer writing a config file.
- **Never use these words:** "prompt", "context window", "token", "MCP", "connector" (say "connect" or "link" instead when talking to the user)
- **One question at a time.** Never present questions as a numbered list. Ask one, wait for the answer, then ask the next. Use their previous answers to make the next question feel natural.
- **Never show extraction to the user.** When you fetch company context from Drive and extract signals (vertical, roles, tools), do that silently. Just start asking questions.
- **Write files immediately** after each Q&A phase completes. Do not ask for approval — just write and confirm briefly.
- **Take your time.** Complete every phase in order. Do not skip steps or combine phases. Quality matters more than speed.

## Prerequisites

Before starting the Q&A, check these in order. Stop at the first failure.

1. **Google Drive connection** — attempt `google_drive_search` with a basic query. If it fails, guide setup (see Phase 0 Step 1). If it succeeds, proceed silently.
2. **Company context docs** — search Drive for the shared company folder, then fetch `What Is [Company]`, `How We Communicate`, and `How We Work` Google Docs. If any are missing, tell the user: "I can't find your company's shared Claude folder in Google Drive. Make sure the folder has been shared with your Google account. Ask your team lead to send you the sharing link." Stop.
3. **Global Instructions doc** — search Drive for the `Global Instructions` Google Doc. If missing, continue onboarding but skip Phase 4. Note at the end: "One step was skipped — your Global Instructions document isn't in the shared folder yet. Ask your team lead to add it."

The skill does NOT require individual context files to already exist. That is what this skill creates.

## Phase 0: Welcome and Context Fetch

### Welcome message

> "Welcome. I'm going to help you set up your Claude workspace so it actually knows who you are and how you work. This usually takes about 10-12 minutes. I'll connect your tools, ask you some questions, write a few short files, and make sure everything is ready. Let's start."

### Step 1: Google Drive connection check

Attempt `google_drive_search` with a basic query. If it succeeds, the connection is live — proceed silently to Step 2.

If it fails or is unavailable:

> "First, we need to connect Google Drive — that's where your team's shared information lives. Here's how:
> 1. Open the sidebar
> 2. Go to Customize, then Connectors
> 3. Find Google Drive and click Enable
> 4. Follow the authorization prompts
>
> Let me know when that's done."

Wait for confirmation. Re-test. If it still fails: "I still can't access Google Drive. Make sure you authorized access and try again. If this keeps happening, ask your team lead for help." Stop.

### Step 2: Fetch company context (silent)

Use `google_drive_search` to find the shared company folder. Then fetch these Google Docs using `google_drive_fetch`:

- `What Is [Company]`
- `How We Communicate`
- `How We Work`

If any doc is not found, show the prerequisite #2 error message and stop.

If all docs are fetched, silently extract:
- Company name
- Company vertical (agency, law firm, consultancy, real estate, etc.) — infer from the "What Is" doc
- Team roles mentioned in "How We Work"
- Communication style signals from "How We Communicate"

Store these as working context. Do not show any of this to the user.

## Phase 1: Who I Am

Before starting questions, read `references/context-file-guidance.md` for the output file schemas and writing guidance.

**Intro line:**
> "First, let me get to know you a bit. This helps Claude understand your role so it can give you more relevant output."

### Questions (ask one at a time, wait for each answer)

**Q1 — Name**
> "What's your name?"

**Q2 — Role**
> "What's your role at [company name]?"

**Q3 — Responsibilities** (adapt phrasing based on vertical and role)

| Vertical | Role example | Phrasing |
|---|---|---|
| Agency | Creative Director | "What kind of work are you responsible for delivering?" |
| Law firm | Paralegal | "What kinds of tasks do you handle most — research, drafting, client communication, case management?" |
| Consultancy | Senior Consultant | "What does a typical engagement look like for you? What are you usually responsible for?" |
| Real estate | Agent | "Are you mostly focused on listings, buyer representation, transactions, or a mix?" |
| Generic | Any | "Walk me through what you own day-to-day — what's actually on your plate?" |

**Q4 — Current priorities**
> "What are the two or three things you're most focused on right now?"

Adapt: law firms → "What kinds of matters are you working on?" / Agencies → "Any live client engagements or projects I should know about?"

**Q5 — Who they work with**
> "Who do you work closely with on your team — and who are the key people you're in contact with outside the team (clients, partners, vendors)?"

**Q6 — What they want help with**
> "Last one for this section: what's the kind of work you most want help with? What takes the most time right now that you'd love to hand off?"

### After Q6

Write `who-i-am.md` immediately using the schema in `references/context-file-guidance.md`. Do not ask for approval. Confirm briefly:

> "Got it — I've saved your role profile. Moving on to communication style."

### Name normalization

Convert their name to a slug for use in later phases: lowercase, hyphens for spaces, remove apostrophes. "Sarah Chen" becomes `sarah-chen`. "Mike O'Brien" becomes `mike-obrien`. Store this slug — it is used in Phase 4 (Global Instructions identifier) and Phase 6 (Drive profile name).

## Phase 2: How I Talk

**Intro line:**
> "Now a quick section on how you communicate. The company has a brand voice — but you probably have your own style on top of that. This helps me match you, not just the company."

Use the company voice from `How We Communicate` (fetched in Phase 0) to frame contrast questions. If the company voice is formal, ask how this person differs.

### Questions

**Q1 — Formality level** *(use AskUserQuestion tool)*
Present using the AskUserQuestion tool with this framing:
- Question: "How would you describe your communication style?"
- Options:
  - **Formal and precise** — "Measured language, no contractions, structured"
  - **Professional but warm** — "Clear and competent with a human touch"
  - **Conversational and direct** — "Sounds like talking, gets to the point"
  - **Casual and friendly** — "Relaxed, informal, personality-forward"
- "Other" is always available for nuance

Use their selection as the anchor for how-i-talk.md. If they pick "Other" and type something, use their words directly.

**Q2 — Phrases they use or avoid**
> "Are there any phrases you always use, or any words you'd never put in an email you sent? Even small things count."

If the answer is vague, prompt once: "For example, do you use words like 'regarding' and 'please advise,' or more like 'just checking in' and 'any thoughts'?"

**Q3 — Recipient context**
> "Who are most of your written communications going to — clients, your team, leadership, vendors? And what do they expect from you?"

**Q4 — What works well**
> "Has Claude ever written something that sounded exactly right — like you wouldn't change a word? What was it about it that worked?"

If they can't think of anything, that's fine — don't push. Move on. But if they give a concrete example, capture it as a positive voice signal in how-i-talk.md under a "What works" section.

**Q5 — What feels wrong**
> "When you're reading something Claude wrote for you, what makes it feel wrong? What's the most common thing you'd want to change?"

If stuck, prompt: "Too many bullet points? Sounds too formal? Starts with 'I hope this finds you well'?"

### Voice sample collection (after Q5, before file write)

This step is optional but high-impact. It turns self-reported voice into evidence-based voice.

**Script:**
> "One more thing that makes a real difference — can you paste 2-3 emails or messages you've actually sent recently? A client email, an internal Slack, a follow-up note — whatever's handy. I'll use these to learn how you actually write, not just how you describe it."

**If the user provides samples:**
Analyze each sample silently. Extract these patterns:
- **Average sentence length** — short and punchy, or longer and explanatory?
- **Opening style** — do they jump straight to the point, or open with a warm line?
- **Closing style** — clear next step, soft sign-off, or abrupt end?
- **Contraction usage** — frequent ("we're", "I'll") or avoided?
- **Vocabulary markers** — any recurring words, phrases, or constructions?
- **Formality markers** — does the actual writing match their self-reported formality from Q1?
- **Structure** — short paragraphs, long blocks, bullet-heavy, or flowing prose?

Write the extracted patterns as a `## Patterns from real communications` section in `how-i-talk.md`. Include 2-3 direct quotes from their samples as "sounds like me" examples.

If the self-reported style (Q1) conflicts with the actual patterns in the samples, trust the samples. Note the discrepancy in `how-i-talk.md` so skills can calibrate correctly. For example: "Self-described as formal, but actual emails use contractions frequently and open with casual warmth."

**If the user declines or skips:**
> "No problem — we can refine your voice profile later as you use the system. For now, I'll work from what you've told me."

Proceed to file write with Q1-Q5 answers only. Do not push or ask again.

### After voice sample step

Write `how-i-talk.md` immediately using the schema in `references/context-file-guidance.md`. If samples were provided, include the extracted patterns section. Confirm:

> "Saved your communication style. One more section."

## Phase 3: How I Work

**Intro line:**
> "Last section — just a few questions about how you like to work. This helps me structure output in a way that actually fits your workflow."

### Questions

**Q1 — Tools**
> "What tools do you use every day?"

If the company's `How We Work` doc lists tools, pre-suggest: "Your team uses [Slack, Asana, Google Workspace] — do you also use those, or are there others I should know about?"

**Q2 — Output format preference** *(use AskUserQuestion tool)*
Present using the AskUserQuestion tool:
- Question: "When Claude gives you something back, how do you want it?"
- Options:
  - **Ready to send** — "Polished and client-ready, minimal editing needed"
  - **Draft I'll edit** — "Good starting point, I'll refine before sending"
  - **Bullet points to pull from** — "Raw material I'll assemble myself"
  - **Depends on the task** — "Ask me each time"

Map the selection directly to how-i-work.md output preferences.

**Q3 — Work rhythm** (optional — accept short answers without pushing)
> "Is there anything about how you work that would be useful to know? For example — do you tend to batch tasks, or work through things one at a time?"

**Q4 — What to avoid** *(use AskUserQuestion tool, multiSelect: true)*
Present using the AskUserQuestion tool with multiSelect enabled:
- Question: "Anything I should avoid? Pick all that apply."
- Options:
  - **Too much preamble** — "Long intros before getting to the answer"
  - **Bullet points when I wanted prose** — "Lists when I need flowing text"
  - **Output that needs heavy editing** — "Rather get less that's more polished"
  - **Overly formal or stiff language** — "Sounds robotic or corporate"

Multiple selections allowed. "Other" available for anything not listed.

### After Q4

Write `how-i-work.md` immediately using the schema in `references/context-file-guidance.md`. Confirm:

> "Saved your work preferences. Now let's get your settings configured."

## Phase 4: Global Instructions

1. Fetch the `Global Instructions` Google Doc from the shared Drive folder via `google_drive_search` and `google_drive_fetch`
2. Append the profile identifier line: `My Claude profile: [name-slug]` (using the slug from Phase 1)
3. Display the complete text in the conversation

**Script:**
> "Almost done. There's one more thing to set up — a short block of text that tells Claude who you are every time you open a new session. You'll paste it into your settings. Here's what it looks like:"

Display the Global Instructions content with the profile identifier appended.

> "To add this:
> 1. Click the **Settings** icon (gear icon, bottom of the sidebar)
> 2. Click **Cowork** in the left menu
> 3. Find the **Global Instructions** field
> 4. Paste the text above into that field
> 5. Click **Save**
>
> Once you've done that, let me know and we'll move to the last step."

Wait for confirmation before proceeding.

If the Global Instructions doc was not found in Drive, skip this phase. Output the profile identifier line and tell the user to add it when Global Instructions become available:

> "Your profile identifier is: `My Claude profile: [name-slug]` — when your team lead sets up Global Instructions, add this line at the end."

## Phase 5: Additional Connectors

Read `references/connector-setup-guide.md` for connector-specific instructions.

Check the `How We Work` doc content (from Phase 0) for which tools the company uses. Only recommend connectors for tools the company actually uses.

**Script:**
> "One more thing — connecting your tools. This lets Claude pull live information from your apps when you need it. I'll walk you through the ones most useful for your setup."

Walk through each relevant connector (Gmail, Google Calendar, Slack) one at a time using the instructions in `references/connector-setup-guide.md`.

If a connector fails or is unavailable, record the skip and continue. Never block completion on connector setup.

## Phase 6: Profile Save and Verification

### Step 1: Verify local files (silent)

Read `who-i-am.md`, `how-i-talk.md`, and `how-i-work.md` to confirm they exist and have content. If any is missing, retry the write once. If it fails again, display the content inline and tell the user to save it manually.

### Step 2: Generate combined profile

Combine all three files into a single block:

```
[Full Name] — Claude Profile

[Full contents of who-i-am.md]

---

[Full contents of how-i-talk.md]

---

[Full contents of how-i-work.md]
```

### Step 3: Save profile to Google Drive

1. Fetch the `System Config` Google Doc from the shared Drive folder via `google_drive_search` and `google_drive_fetch`
2. Extract `profiles_folder_id` from the doc content
3. Build the direct link to the Individual Profiles folder: `https://drive.google.com/drive/folders/[profiles_folder_id]`

**Script:**

> "Last step — saving your profile to the team's shared Drive folder. This is the one manual step in the whole setup, and it takes about 30 seconds. I've got everything ready for you."

Display the combined profile block (from Step 2).

Then give these exact instructions:

> "Here's what to do:"
>
> **Step 1:** Copy the profile text above (select all of it — from your name down to the last line)
>
> **Step 2:** Click this link to open the profiles folder:
> `https://drive.google.com/drive/folders/[profiles_folder_id]`
>
> **Step 3:** In that folder, click the **+ New** button (top left), then **Google Docs**, then **Blank document**
>
> **Step 4:** Name the document: `[name-slug] — Claude Profile`
> (Replace the default "Untitled document" at the top of the page)
>
> **Step 5:** Paste the profile text into the document body
>
> **Step 6:** You're done — Google Docs saves automatically. Close the tab and come back here.
>
> "Let me know when that's saved."

Wait for confirmation.

**After confirmation:**
> "You're all set. Here's what was created:"
> - Your profile files saved locally (who-i-am.md, how-i-talk.md, how-i-work.md)
> - Your profile saved to the team's shared Google Drive folder
> - Global Instructions configured with your profile identifier
> - [List connectors enabled or skipped]
>
> "When you start a new client project, type `/new-project` to load everything and get set up in about two minutes. That's where this system earns its value."

**If the user can't access the folder** (permissions error, link doesn't work):
> "It looks like you might not have access to that folder yet. Ask your team lead to share the `[Company Name] Claude System` folder with your Google account. Once you have access, come back and create the profile doc in the `Individual Profiles` subfolder. Your local files are already saved, so everything else still works."

## Re-run Behavior

If `/onboard` is invoked and context files already exist, check which:

- If all three exist: "It looks like your profile is already set up. Would you like to update any part of it? I can walk you through any section again." Offer options: who-i-am, how-i-talk, how-i-work, or connectors.
- If some exist: "I see you already started setup — want to continue from where you left off or start over?" Skip completed phases by default.
- Global Instructions and connectors: always re-offer even if previously completed (they cannot be verified).

## Error Handling

### Incomplete answers
If a user gives a very short or vague answer, ask one follow-up for specifics. Do this once per question — not repeatedly. If the second answer is still thin, accept it and write the best file possible. Mark thin sections with an inline note in the file:

```markdown
## What to avoid
*(You can add specifics here as you notice them — just edit this file.)*
```

### Company context not found
Stop immediately. Never proceed to Q&A without company context — question adaptation depends on it.

### Local file write failure
1. Retry once automatically
2. If it fails again, display the content inline so the user can copy it manually
3. The critical output is the combined profile for the Drive save — if local files fail but the user saves to Drive, onboarding still succeeds

### User can't save to Google Drive
1. Show the error guidance from Phase 6 (ask team lead for folder access)
2. Note: `/new-project` will detect the missing profile and fall back to asking for the user's name — onboarding is still usable without the Drive profile
