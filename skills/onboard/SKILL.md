---
name: onboard
description: >
  First-time team member onboarding for Claude System Foundation. Creates
  personal context files (who-i-am, how-i-talk, how-i-work) through guided
  conversation, connects Google Drive and other tools, generates personalized
  Global Instructions, and saves profile to the shared Drive folder. Use when
  someone says "set up my profile", "I'm new", "first time setup", "onboard
  me", or is using Claude for the first time. Requires Google Drive MCP
  connector.
version: 0.3.0
---

# Onboard

Guide a new team member through first-time Claude setup. This skill creates their personal context files through conversation, connects their tools, generates personalized Global Instructions, and saves their profile to the shared Google Drive folder so other skills can find them.

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

The skill does NOT require individual context files to already exist. That is what this skill creates.

## Phase 0: Welcome and Context Fetch

### Welcome message

> "Welcome. I'm going to help you set up your Claude workspace so it actually knows who you are and how you work. This usually takes about 10-12 minutes. I'll connect your tools, ask you some questions, write a few short files, and make sure everything is ready. Let's start."

### Step 1: Google Drive connection check

Attempt `google_drive_search` with a basic query. If it succeeds, the connection is live — proceed silently to Step 2.

If it fails or is unavailable:

> "I can't reach Google Drive right now — your connection looks expired or hasn't been enabled yet. That's where your team's shared information lives, so we need it before going further.
>
> Quick fix:
> 1. Open the sidebar → Customize → Connectors
> 2. Find Google Drive. If it shows Reconnect, click it. If it's missing, click Enable.
> 3. Follow the authorization prompts.
>
> Let me know when that's done."

Wait for confirmation. Re-test. If it still fails:

> "Still no luck. The most common cause is that the shared Drive folder hasn't been shared with your Google account yet. Ask your team lead — they should send you the sharing link."

Stop.

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
- Tools the company uses from "How We Work"
- Key behavioral rules or principles from any doc

Store these as working context. Do not show any of this to the user.

### Step 3: Existing-profile check (silent)

Before starting Phase 1, check whether this person has run `/onboard` before. The check has two parts.

**Part A: Look for the profile identifier in Global Instructions.**

Search the loaded conversation context (the Global Instructions block, always loaded in every Cowork session) for a line matching:

```
My Claude profile: [name-slug]
```

Capture the slug if found.

**Part B: If an identifier was found, verify the profile exists in Drive.**

Use `google_drive_search` for `[name-slug] — Claude Profile` in the shared folder.

**Branch outcomes:**

| Identifier in Global Instructions | Profile Doc in Drive | Branch |
|---|---|---|
| Yes | Yes | A — re-run flow (see Re-run Behavior) |
| Yes | No | B — orphaned-identifier flow (see Re-run Behavior) |
| No | — | C — first-time user, proceed silently to Phase 1 |

The legacy local-file check (looking for `who-i-am.md` etc. in the current folder) is now a fallback only — used if Drive is unreachable mid-check.

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

## Phase 4: Generate Global Instructions

This phase generates a personalized Global Instructions block from everything learned during onboarding — company context + individual profile. This replaces the old approach of reading a pre-written template from Drive.

### Step 1: Synthesize

Using all available information, generate a Global Instructions block. Sources:

- **Company context** (from Phase 0): company name, what it does, key communication principles, work approach, behavioral rules
- **Individual profile** (from Phases 1-3): name, role, communication style, output preferences, work rhythm, what to avoid
- **Profile identifier**: the name slug from Phase 1

### Step 2: Generate the block

Follow this structure exactly. Every section should reflect THIS person, not a generic template.

```
You are working with [Full Name].
[Name] is [role] at [Company Name] — [one sentence about what the company does, from the "What Is" doc].
How [Name] communicates:
- [2-4 bullet points synthesized from How We Communicate company doc + their Phase 2 answers. Include their formality level, directness preference, and any key rules from the company voice. Use specifics from their answers — not generic guidance.]
How [Name] works:
- [2-4 bullet points from Phase 3 answers + How We Work company doc. Include collaboration style, work rhythm, and any strong preferences they expressed.]
Output defaults:
- [2-4 bullet points from their output format preference, what to avoid selections, and any company-wide output rules from How We Work.]
My Claude profile: [name-slug]
```

**Quality rules for generation:**
- Every bullet point should be specific to this person. If a bullet could apply to anyone, rewrite it.
- Pull actual phrases or language from their answers where possible.
- Company-wide rules (from How We Communicate / How We Work) should be included but adapted to this person's context — not just copied verbatim.
- Keep total length to 100-200 words. Global Instructions are read every session — concise beats comprehensive.
- The profile identifier line (`My Claude profile: [name-slug]`) must always be the last line.

### Step 3: Present and instruct

**Script:**
> "Almost done. I've put together a short block of text based on everything you told me. This tells Claude who you are every time you open a new session. Here's what it looks like:"

Display the generated Global Instructions block.

> "To add this:
> 1. Click the **Settings** icon (gear icon, bottom of the sidebar)
> 2. Click **Cowork** in the left menu
> 3. Find the **Global Instructions** field
> 4. Paste the text above into that field
> 5. Click **Save**
>
> Once you've done that, let me know and we'll move to the last step."

Wait for confirmation before proceeding.

### Step 4: Offer refinement

After the user confirms they've pasted it:

> "If anything in there doesn't sound right — or if you think of something important to add later — you can always edit it in Settings. It's just text."

Move to Phase 5.

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

### Step 3: Read System Config

Fetch the `System Config` Google Doc from the shared Drive folder via `google_drive_search` and `google_drive_fetch`. Extract two values from the doc body:

- `profile_webhook_url` — Apps Script web app URL that creates/updates profile Docs
- `profiles_folder_id` — Google Drive folder ID for Individual Profiles

If either value is missing or System Config can't be fetched, skip directly to Step 5 (manual fallback) and tell the user that automated save isn't available right now.

### Step 4: Push profile to Drive via webhook (primary path)

Tell the user briefly:

> "Saving your profile to Drive..."

Write the combined profile content to a temp file (avoids JSON escaping headaches with multi-line content), build the JSON payload, and POST to the webhook. The Apps Script automatically creates a new Doc or overwrites an existing Doc with the title `[name-slug] — Claude Profile`.

**Bash invocation pattern** (the model adapts syntax to its sandbox; the goal is a JSON POST followed by a manual redirect-follow to read the response body):

```bash
# Write the combined profile to a temp file
cat > /tmp/profile_content.txt <<'PROFILE_EOF'
[combined profile content from Step 2]
PROFILE_EOF

# Build the JSON payload safely (Python handles escaping of newlines, quotes, etc.)
python3 - <<'PY' > /tmp/profile_payload.json
import json
with open('/tmp/profile_content.txt') as f:
    content = f.read()
payload = {
  "name": "[name-slug]",
  "content": content,
  "folderId": "[profiles_folder_id]"
}
print(json.dumps(payload))
PY

# POST without -L (Apps Script returns 302 with response body at the redirect URL)
HTTP_CODE=$(curl -sS \
  -X POST \
  -H "Content-Type: application/json" \
  --max-time 30 \
  -o /tmp/profile_response_body.txt \
  -D /tmp/profile_response_headers.txt \
  -w "%{http_code}" \
  -d @/tmp/profile_payload.json \
  "[profile_webhook_url]")

# Apps Script success = HTTP 302. The actual response body lives at the redirect URL.
if [ "$HTTP_CODE" = "302" ]; then
  LOCATION=$(grep -i "^location:" /tmp/profile_response_headers.txt | cut -d' ' -f2- | tr -d '\r\n')
  curl -sS --max-time 20 "$LOCATION"
else
  echo "WEBHOOK_FAILED: HTTP $HTTP_CODE"
  cat /tmp/profile_response_body.txt
fi
```

**Parse the response.** Expected outcomes:

| Response | Meaning | Action |
|---|---|---|
| `{"status":"created", "docId":"...", ...}` | New profile Doc created in Drive | Success — proceed to confirmation |
| `{"status":"updated", "docId":"...", ...}` | Existing Doc found and overwritten (re-run, or idempotent retry) | Success — proceed to confirmation |
| `{"status":"error", ...}` | Script-level failure (bad payload, folder permissions, etc.) | Fall through to Step 5 (manual fallback) |
| `WEBHOOK_FAILED: HTTP [code]` from bash | Network/HTTP error | Fall through to Step 5 |
| Anything else / no response | Unknown failure | Fall through to Step 5 |

**On success, confirm to the user:**

> "Saved your profile to Drive. You can find it here: `https://docs.google.com/document/d/[docId from response]/edit`
>
> Anything in there you want to change later, just edit the Doc directly — Google saves automatically."

Then proceed to the wrap-up message below.

### Step 5: Manual paste (fallback only)

Reach this step only if Step 4 fails or System Config is missing the webhook URL.

> "I couldn't save your profile to Drive automatically. No problem — it takes about 30 seconds manually. Here's what to do:"

Display the combined profile block.

> **Step 1:** Copy the profile text above (select all of it — from your name down to the last line)
>
> **Step 2:** Click this link to open the profiles folder:
> `https://drive.google.com/drive/folders/[profiles_folder_id]`
>
> **Step 3:** Click **+ New** (top left) → **Google Docs** → **Blank document**
>
> **Step 4:** Name the document exactly: `[name-slug] — Claude Profile`
>
> **Step 5:** Paste the profile text into the document body
>
> **Step 6:** Close the tab — Google Docs auto-saves.
>
> "Let me know when that's done."

Wait for confirmation.

**If the user can't access the folder** (permissions error, link doesn't work):
> "Looks like you might not have access to that folder yet. Ask your team lead to share the `[Company Name] Claude System` folder with your Google account. Once you have access, come back and create the profile doc in the `Individual Profiles` subfolder. Your local files are already saved, so everything else still works."

### Wrap-up (after Step 4 success or Step 5 confirmation)

> "You're all set. Here's what's in place:
> - Your local profile files (who-i-am.md, how-i-talk.md, how-i-work.md)
> - Your profile in your team's shared Drive folder
> - Global Instructions in your Cowork settings
> - [List connectors enabled or skipped]
>
> When you start a new client project, type `/new-project` to load everything and get set up in about two minutes. That's where this system earns its value."

## Re-run Behavior

The branch is decided by Phase 0 Step 3. Profile state in Drive is the source of truth — local files are a fallback only.

### Branch A: Identifier exists, profile found in Drive

The user has run `/onboard` before and the Drive Doc is intact. Greet them with what's already on file and ask what they want to do.

> "Looks like your profile is already set up:
> - Name: [Full Name parsed from profile Doc]
> - Last updated: [last-modified date from Drive search result]
> - Saved to: [direct link to the Drive Doc]
>
> What would you like to do?"

Use AskUserQuestion with these four options:
- **Update one section** — re-run a specific phase and replace just that section
- **Regenerate Global Instructions** — re-synthesize from existing profile + company context, no questions
- **Start fresh** — full re-run; existing Drive Doc gets overwritten
- **Cancel**

**Update one section flow:**
1. Sub-question: which section — who-i-am, how-i-talk, or how-i-work?
2. Re-run only the relevant phase questions
3. Replace that section in the local file
4. Generate the updated combined profile (all three sections, latest content)
5. POST the updated profile to the webhook (use the Phase 6 Step 4 pattern). The Apps Script finds the existing Doc by title and overwrites the body — no duplicate created.
6. Confirm: "Updated. Your profile in Drive now reflects the change."
7. If the webhook fails, fall through to a manual update prompt (link to the existing Doc, walk user through select-all-paste).

**Regenerate Global Instructions flow:**
1. Confirm: "I'll generate a fresh Global Instructions block from your existing profile and your company context. Your profile in Drive stays the same — only the Cowork settings change."
2. Read local context files if present, otherwise re-fetch the profile from Drive
3. Run Phase 4 only (synthesize, present, paste)
4. Confirm completion

**Start fresh flow:**
1. Confirm explicitly: "This will replace your existing profile in Drive *and* replace your Global Instructions in Cowork settings. Your local context files will be regenerated. Continue?"
2. On confirm, run all phases as normal (Phase 1 onward)
3. In Phase 6 Step 4, the webhook POST overwrites the existing Doc by title — no manual cleanup, no duplicates

### Branch B: Identifier exists, profile NOT found in Drive

The user's Cowork settings reference a profile, but the Drive Doc is missing. Could be deleted, renamed, moved, or the original push never landed.

> "Your Cowork settings reference a profile named `[name-slug]`, but I can't find a matching Doc in your team's Drive folder. It may have been deleted, renamed, or never made it through.
>
> What would you like to do?"

Use AskUserQuestion:
- **Recreate from scratch** — full `/onboard` flow; new Doc created in Drive
- **I have local files — push those to Drive** — if `who-i-am.md`, `how-i-talk.md`, `how-i-work.md` exist locally, generate the combined profile and POST it to the webhook (Apps Script creates a fresh Doc)
- **Open Drive folder and check first** — display the folder link, pause, then re-search on confirmation
- **Cancel**

**Push existing local files flow:**
1. Read the three local files
2. Generate the combined profile block
3. POST to the webhook (Phase 6 Step 4 pattern). The Apps Script creates a new Doc since the previous one is missing.
4. Confirm: "Got it — your profile is back in Drive."
5. If the webhook fails, fall through to manual paste (Phase 6 Step 5 fallback)

### Branch C: No identifier in Global Instructions

First-time user. Proceed to Phase 1 silently — no re-run logic applies.

### Local-file fallback (legacy)

If Phase 0 Step 3 cannot complete because Drive is unreachable, fall back to checking local files in the current folder:

- If all three exist: offer "update one section" or "start over" — same options as Branch A but warn the user that Drive sync state is unverified
- If some exist: "Looks like setup was started but didn't finish. Want to continue or start over?"
- This branch is intentionally degraded — without Drive access the skill can't verify the canonical state, so any "update" requires the user to manually sync to Drive once it's reachable again

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

### Drive connection lost mid-flow
If `google_drive_search` or `google_drive_fetch` fails partway through onboarding (token expired, network drop, folder permissions changed):

1. **Save what's already been written.** Local files (`who-i-am.md`, `how-i-talk.md`, `how-i-work.md`) stay on disk. Global Instructions, if already pasted, stay in Cowork settings.
2. **Tell the user clearly:**

> "Drive dropped out partway through. Your local profile files are saved, and any Global Instructions you've already pasted are still in your settings. Reconnect Drive (sidebar → Customize → Connectors → Google Drive), then run `/onboard` again. It'll detect what's already done and offer to finish without re-asking everything."

3. **On the next run,** Phase 0 Step 3 will detect the partial state:
   - If the identifier is in Global Instructions but no Drive Doc exists → Branch B offers "Push existing local files"
   - If no identifier yet but local files exist → fall back to the local-file legacy branch and resume from where the user stopped
