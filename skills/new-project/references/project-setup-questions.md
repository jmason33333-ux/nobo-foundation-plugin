# Project Setup Questions

Use this reference during Phase 3 of /new-project. Select 4-6 questions from the templates below based on the company vertical and context.

## Client Terminology Mapping

Use the correct term based on the company vertical inferred from the `What Is [Company]` doc:

| Vertical | Term for client | Term for engagement |
|---|---|---|
| Agency | client | engagement, project |
| Law firm | client | matter |
| Consultancy | client | engagement |
| Real estate | client, buyer, seller | listing, transaction |
| Financial services | client | account, engagement |
| Generic fallback | client | project |

Use this terminology in all questions and in the project brief output.

---

## Question Templates by Vertical

### Q1 — Client/matter name (always asked)

| Vertical | Phrasing |
|---|---|
| Agency | "Who's the client?" |
| Law firm | "What's the matter?" or "Who's the client?" |
| Real estate | "What's the listing?" or "Who's the client?" |
| Generic | "Who's the client?" |

### Q2 — Engagement type (always asked)

| Vertical | Phrasing |
|---|---|
| Agency | "What kind of work is this?" or "What are the deliverables?" |
| Law firm | "What type of matter?" |
| Real estate | "Is this a new listing or active transaction?" |
| Consultancy | "What kind of engagement is this?" |
| Generic | "What kind of work is this?" |

### Q3 — Key contacts (always asked)

> "Who are the key people — on their side and yours?"

Same across all verticals. Format the answer as a bulleted list if multiple people are mentioned.

### Q4 — Timeline (always asked)

> "Any deadlines or timeline I should know about?"

Same across all verticals. If the user says none, record "No specific deadline mentioned."

### Q5 — Special constraints (conditional)

Ask this question if the company vertical suggests domain-specific constraints. Skip if the vertical doesn't have obvious constraint categories.

| Vertical | When to ask | Phrasing |
|---|---|---|
| Law firm | Always | "Any jurisdictional or regulatory constraints?" |
| Agency | If deliverable-heavy | "Any brand guidelines or creative constraints from the client?" |
| Real estate | Always | "Any special conditions — inspection issues, financing contingencies?" |
| Consultancy | If SOW-based | "Any scope boundaries or things explicitly out of scope?" |
| Generic | Optional | "Anything else I should know before we start?" |

### Q6 — Catch-all (conditional)

> "Anything else that would be useful context for this project?"

**When to ask:** Only if Q1-Q5 answers were very brief (one-word or one-sentence answers across the board). If the user has given substantive answers, skip Q6.

**When to skip:** If any answer was more than 2 sentences, or if Q5 already served as a catch-all.

---

## project-brief.md Output Schema

Write this file immediately after the questions are complete.

```markdown
# Project Brief

**Client:** [answer to Q1]
**Type:** [answer to Q2]
**Team member:** [user's full name] ([role])

## Key contacts

[answer to Q3 — formatted as a bulleted list if multiple people were mentioned]

## Timeline

[answer to Q4 — or "No specific deadline mentioned" if the user said none]

## Constraints

[answer to Q5 — or omit this section entirely if Q5 was skipped or the answer was "nothing special"]

## Additional context

[answer to Q6 — or omit this section if Q6 was skipped]
```

### Quality bar
- Client name is present and specific
- Engagement type is present
- At least one key contact is listed
- Timeline section is present (even if "no deadline")
- No placeholder text remains — no `[bracketed variables]` in the final file
