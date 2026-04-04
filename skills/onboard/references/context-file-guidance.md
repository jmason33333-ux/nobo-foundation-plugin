# Context File Guidance

Use this reference when writing the three individual context files during /onboard. Each file has a specific schema and quality bar.

## Writing Rules

- Write in **third person** for who-i-am.md ("Sarah manages..." not "I manage...")
- Be **specific, not generic**. "Manages a portfolio of 12 active DTC brand accounts" beats "Manages client accounts"
- Use the person's **actual words** where possible, lightly edited for clarity
- If a section has thin content because the person gave a short answer, add an inline note rather than padding with generic filler:
  ```
  *(You can add specifics here as you notice them — just edit this file.)*
  ```
- Never invent details. If they didn't mention it, don't include it.

---

## who-i-am.md Schema

```markdown
# Who I Am

**Name:** [Full name]
**Role:** [Job title at company]

## What I do

[2-4 sentences describing their responsibilities, written in third person. Be specific to what they said.]

## What I own

[Bulleted list of primary responsibilities. 3-6 items. Specific, not generic.]

## Current priorities

[2-3 items they mentioned as current focus areas. Their words, specific.]

## Key relationships

**Internal:** [Names/roles they work closely with on the team]
**External:** [Clients, partners, vendors — as specific as they could be]

## What I want Claude to help with

[What they said in Q6. Their words, lightly edited for clarity. 1-3 sentences.]
```

### Minimum quality bar
- Non-generic role description
- At least 3 items under "What I own"
- At least 1 item under "What I want Claude to help with"

---

## how-i-talk.md Schema

```markdown
# How I Talk

## My communication style

[1-2 sentences describing their style — formality level, warmth, directness — in relation to the company voice if relevant. Example: "Sarah is slightly more conversational than the company's formal brand voice, especially in internal communication."]

## What sounds like me

[3-5 bullet points. Phrases they use, patterns they mentioned, examples they gave. Specific language where possible.]

## What doesn't sound like me

[2-4 bullet points. Phrases, formats, or patterns they called out. Include anything from Q4.]

## Who I'm usually writing to

[Summary of recipient context from Q3. 1-2 sentences.]

## Format preferences

[How they want output structured — document vs. draft vs. bullets, level of polish expected, what they'd change most often.]
```

### Minimum quality bar
- At least 2 items in "What sounds like me"
- At least 1 item in "What doesn't sound like me"

---

## how-i-work.md Schema

```markdown
# How I Work

## Tools I use daily

[Bulleted list of tools they mentioned. If they confirmed company tools from how-we-work.md, include those. Add any personal tools they added.]

## How I want output structured

[What they said about format preference — document to send, draft to edit, bullets to pull from, etc. 1-2 sentences.]

## How I work best

[If they gave a useful answer about work rhythm, include it. If not, omit this section entirely — do not pad with generic content.]

## What to avoid

[List from Q4 — specific things they don't want in output. 2-5 bullet points. If they said "not sure," omit this section.]
```

### Minimum quality bar
- At least 3 tools listed
- A stated format preference

---

## Combined Profile Format (for Google Drive upload)

When pushing the profile to Drive in Phase 6, combine all three files into this format:

```
[Full Name] — Claude Profile

[Full contents of who-i-am.md]

---

[Full contents of how-i-talk.md]

---

[Full contents of how-i-work.md]
```

The `---` separators are the parsing boundaries. `/new-project` splits the combined profile on these to extract the three sections. Do NOT use `---` within any individual section — only between the three main sections.

## Name and Role Parsing Contract

The `**Name:**` and `**Role:**` fields must be on their own lines with the bold label format shown above. `/new-project` parses them by pattern matching. Do not change this format.
