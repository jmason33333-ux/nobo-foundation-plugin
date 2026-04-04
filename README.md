# Nobo Foundation Plugin

17 specialist skills, compound workflows, and onboarding flows that turn Claude into a context-aware work system for professional services teams. Install once, use everywhere.

> **Private distribution repo.** This plugin is delivered as part of the [Nobo Claude System Foundation](https://nobo.so) engagement.

---

## Table of Contents

- [How It Works](#how-it-works)
- [Install](#install)
- [Getting Started](#getting-started)
- [Skills Overview](#skills-overview)
  - [Onboarding (2)](#onboarding)
  - [Specialists (10)](#specialist-skills)
  - [Compound Workflows (4)](#compound-workflows)
  - [System Extension (1)](#system-extension)
- [Context Files](#context-files)
- [Learning System](#learning-system)
- [Updating](#updating)

---

## How It Works

The plugin connects to your team's shared Google Drive folder where your company context lives. Each team member runs onboarding once, and from that point forward every skill knows who they are, how they write, and how the company works.

1. **Install the plugin** — open the `.plugin` file in Claude. One click loads all 17 skills.
2. **Run `/onboard`** — a 10-minute guided conversation creates your personal profile (role, voice, work preferences). No forms.
3. **Start a project with `/new-project`** — answer a few questions about the client or engagement. Under 2 minutes. Every specialist now knows the context.
4. **Delegate** — ask naturally or invoke a skill by name. Claude picks the right specialist, reads your context, and produces output in your voice.

---

## Install

```
/plugin marketplace add jmason33333-ux/nobo-foundation-plugin
/plugin install nobo-foundation@nobo-foundation-plugin
```

---

## Getting Started

| Step | What to do | Time |
|------|-----------|------|
| 1 | Install the plugin (open `nobo-foundation.plugin`, click install) | 1 min |
| 2 | Type `/onboard` — Claude walks you through your profile: role, voice, how you work. Connects Google Drive and saves to the shared folder. | ~10 min |
| 3 | Create a new project in Claude, type `/new-project`, answer a few questions about the client or engagement. Context files are written automatically. | ~2 min |
| 4 | Start delegating — "draft a follow-up to Sarah," "project status update," "research this company before my call." Claude picks the right specialist. | ongoing |
| 5 | Refine over time — when output doesn't sound right, say so. Run `/refine-voice` at the 30-day check-in to sharpen your profile from real examples. | periodic |

---

## Skills Overview

### Onboarding

| Skill | Description |
|-------|-------------|
| `/onboard` | Guided conversation that creates your personal profile — role, voice, work preferences. Connects Google Drive. Run once per team member. |
| `/new-project` | Creates project context files for a client or engagement. Answer a few questions, and every specialist skill gets the context it needs. |

### Specialist Skills

**Client Operations**

| Skill | Role | What it does |
|-------|------|-------------|
| `/client-comms` | Communications | Drafts, reviews, and improves any client message — status updates, follow-ups, meeting summaries, difficult conversations, scope discussions. Writes in your voice and calibrates tone to the situation. |

**Content & Marketing**

| Skill | Role | What it does |
|-------|------|-------------|
| `/content-strategist` | Content | Writes brand-aligned content across every format — emails, proposals, blog posts, LinkedIn posts, case studies, newsletters, website copy. A LinkedIn post sounds different from a proposal, but both sound like you. |
| `/brand-strategist` | Brand | Reviews any draft for brand consistency — voice, tone, messaging, visual identity. Flags what's off, explains which principle it violates, shows the corrected version. |

**Project Management**

| Skill | Role | What it does |
|-------|------|-------------|
| `/project-manager` | PM | Tracks project status, surfaces risks, maps dependencies, keeps everyone aligned. Produces structured status reports (Progress / Risks / Next Actions) where every action has one named owner. |

**Research & Intelligence**

| Skill | Role | What it does |
|-------|------|-------------|
| `/research-specialist` | Research | Structured research and synthesis — prospect briefs, competitive analysis, market overviews, topic backgrounders. Evaluates source quality, rates confidence on key claims, tells you what's missing. |

**Reporting & Communication**

| Skill | Role | What it does |
|-------|------|-------------|
| `/executive-comms` | Executive | Turns complex information into sharp summaries for senior audiences. Bottom line up front, readable in under 3 minutes. Four formats: executive summary, briefing doc, condensed version, one-pager. |
| `/data-analyst` | Data | Makes sense of numbers — raw data, spreadsheets, metrics, reports into clear findings for non-technical audiences. Leads with what matters, highlights outliers, flags incomplete data. |

**Presentations**

| Skill | Role | What it does |
|-------|------|-------------|
| `/presentation-strategist` | Presentations | Structures ideas into persuasive slide narratives. One idea per slide, no generic titles. Three frameworks mapped to audience type: client pitch, internal update, executive briefing. |

**Business Operations**

| Skill | Role | What it does |
|-------|------|-------------|
| `/financial-analyst` | Finance | Fee proposals, cost estimates, budget tracking, profitability analysis, pricing comparisons. States every assumption, uses round numbers where precision doesn't matter, never hides bad news. |
| `/process-optimizer` | Process | Maps, documents, and improves how work gets done — SOPs in plain imperative language, process maps with owners, improvement recommendations graded by effort. Works from what exists before suggesting changes. |

### Compound Workflows

These run multi-step tasks in one pass and keep the system's memory current.

| Skill | What it does | Produces |
|-------|-------------|----------|
| `/prep-for-call` | Complete pre-meeting brief so you walk in informed and can send a follow-up within 5 minutes of hanging up. Works for prospects and existing clients. | Prospect/client brief with conversation hooks → structured talking points → pre-drafted follow-up email in your voice |
| `/weekly-review` | Consolidated end-of-week brief across every active engagement. Run once on Friday. | Multi-project status summary → leadership-ready brief → draft check-in email for each active client |
| `/daily-log` | End-of-day capture — what got done, what was decided (and why), what changed, what's open, what to pick up tomorrow. Next session starts where yesterday left off. | Structured daily entry → rolling 5-day log → automatic session continuity |
| `/refine-voice` | Makes your voice profile more accurate over time. Paste what Claude wrote vs. what you actually sent — the skill extracts the patterns and updates your profile. | Pattern analysis → updated voice profile → better output from every skill |

### System Extension

| Skill | What it does |
|-------|-------------|
| `/skill-creator-v2` | Build new specialist skills from scratch or improve existing ones. Describe what you want, and the builder walks you through intent capture, writing, test cases, evaluation, and iteration. New skills install alongside the rest of the plugin with the same trigger behavior and context loading. |

---

## Context Files

When you run `/onboard` and `/new-project`, the plugin creates context files that every specialist reads before responding. These are why output sounds like your company — not like generic AI.

| File | Purpose |
|------|---------|
| `how-we-communicate.md` | Brand voice, tone rules, preferred phrases, phrases to avoid. Primary reference for every writing skill. |
| `what-is-[company].md` | Company identity, positioning, values. Ensures content reflects who you are. |
| `how-we-work.md` | Team structure, tools, processes, decision-making. Used by PM and operations skills. |
| `who-i-am.md` | Your role, responsibilities, what you own, who you report to. Shapes how skills tailor output to your level. |
| `how-i-talk.md` | Your personal communication style — sentence structure, warmth, directness, vocabulary. |
| `how-i-work.md` | Your tools, schedule, output preferences, pet peeves. |
| `project-brief.md` | Client name, engagement type, key contacts, timeline, constraints. Created per-project by `/new-project`. |
| `project-log.md` | Running log of what's happened on the engagement. Skills read it to avoid repeating work. |

---

## Learning System

The plugin gets smarter with use through three mechanisms:

**Voice learning** — When you correct a draft's tone ("too formal," "I wouldn't say it that way"), the skill adjusts and offers to save the learning to your voice profile. Happens automatically in `/client-comms`, `/content-strategist`, and `/executive-comms`.

**Project memory** — Every specialist appends a one-line log entry to `project-log.md` after producing output. The next skill that responds on the same engagement reads the log first — it knows what's been communicated, decided, and flagged.

**Daily continuity** — `/daily-log` captures what got done, what was decided and why, what changed, and what to pick up tomorrow. Every new session reads this first. Decisions are logged with reasoning, so "why did we do it that way?" always has an answer.

**Voice profile refinement** — Run `/refine-voice` periodically with before/after examples (what Claude wrote vs. what you sent). Recommended at the 30-day check-in.

---

## Updating

Push changes to this repo and bump the version in `.claude-plugin/plugin.json`. Clients with auto-update enabled receive updates at next startup.

---

Built by [Nobo](https://nobo.so) — Boulder's AI studio.
