---
name: idea-to-prd
description: "Use this skill when a user wants to turn a raw idea, voice dump, or napkin sketch into a copy-paste-ready Claude Code build prompt (a PRD). Triggers include: 'I have an idea for an app', 'help me scope this', 'turn this into a PRD', 'write me a Claude Code prompt for...', 'I want to vibe code...', or any vocal/text dump describing a product to build. The skill interviews the user, enforces MVP scope, calls them out when they drift into 'robust mode', references their other installed skills (frontend-design, repo-pentest, docx, etc.), and outputs a single-shot build prompt plus supporting docs."
---

# Idea-to-PRD: The Vibe Coder's PRD Generator

## Purpose

Take a raw idea (vocal drop, rant, napkin sketch) and turn it into a **copy-paste-ready one-shot build prompt** for Claude Code that:

1. Builds the MVP — not a robust v2
2. Uses the user's already-installed skills automatically
3. Calls out the user when they drift into scope creep
4. Documents next-phase items so nothing gets lost

## When to Use This Skill

Trigger on any of:
- "I have an idea for [app/tool/product]"
- "Help me scope this"
- "Turn this into a PRD"
- "Write me a Claude Code prompt for..."
- "I want to vibe code..."
- A voice/text transcript describing a product to build
- A user describes a problem they want software to solve

## The 5-Phase Flow

### Phase 1: The Drop (Listen, Don't Interrupt)

Tell the user: **"Drop it all on me. Vocal note, ramble, tangents — the messier the better. I'll sort it after."**

Wait for the dump. Do not interview yet. Do not ask clarifying questions. Just acknowledge with one line:

> "Got it. Pulling the signal out now."

### Phase 2: Extract & Mirror

After the dump, produce a structured mirror in this exact format:

```
## What I Heard

**Core problem:** [one sentence]
**Who has it:** [primary user]
**The "it just works" moment:** [the dream user experience in 1-2 sentences]
**Features I caught:** [numbered list, credit the source if it was a group dump]
**Constraints/guardrails:** [non-negotiables — security, brand voice, human-in-loop, etc.]
**Tech the user already mentioned:** [list]
```

Then ask: **"Did I miss anything? Anything I got wrong?"**

Wait for confirmation or corrections.

### Phase 3: The Interview (Max 5 Questions)

Ask only the questions you genuinely cannot infer. Use the `ask_user_input_v0` tool with tappable options. **Hard cap: 5 questions.** If you need more than 5, you're overthinking it.

**FIRST: Detect your audience.** Read the brief and the user's voice. Pick one of two question sets below.

#### Audience Detection Heuristic

Use the **BEGINNER** set when you see ANY of these signals in the brief or earlier conversation:
- The user describes the idea in everyday English with no tech terms ("a thing that lets people...")
- The user mentions they're new to coding, "vibe coding," or building their first app
- The brief uses business language without specifying frameworks
- The user has not named specific technologies (no React, Postgres, Railway etc.)
- The user is in a mastermind, coaching, or learning context

Use the **DEV** set when you see ANY of these signals:
- The user names specific frameworks, languages, or tools
- The user mentions architecture choices (multi-tenant, microservices, etc.)
- The user uses developer vocabulary (deploy, database, API, ORM, CI/CD)
- The user references prior projects or technical experience
- The brief mentions performance, scale, security, or technical constraints

When in doubt, default to **BEGINNER**. A dev will tolerate plain language. A beginner will be lost in jargon.

#### BEGINNER Question Set (5th-grade reading level, plain English)

Ask in this order. Skip any question where the dump already gave a clear answer.

**Q1 · MVP scope cut line**
> "What's the ONE thing this has to do for you to be proud to show your friend? (Everything else can come later.)"

Tappable options like:
- "Just the basic capture-and-save thing"
- "Capture-and-save plus one nice touch"
- "More than that — let me explain"

**Q2 · Auth/users (who logs in)**
> "Who needs to log in to use this on day one?"

Options:
- "Just me"
- "Me and a few people I trust"
- "Anybody who signs up"
- "Not sure yet"

**Q3 · Data persistence (memory)**
> "Does this need to remember things between visits, or start fresh each time?"

Options:
- "Remember everything I save"
- "Remember some things, forget others"
- "Start fresh each time is fine"

**Q4 · Integrations (other tools)**
> "What other tools does this need to talk to on day one? (Email, calendar, payment, contacts? Or none for now?)"

Options:
- "None — it can be standalone"
- "One specific tool — let me name it"
- "A few tools, but I can pick the most important one"

**Q5 · Deploy target (where it lives online)**
> "Where do you want this to live? On a phone? On the web at its own little address? Or just on your computer for now?"

Options:
- "Web address I can share"
- "Phone (mobile-friendly web is fine)"
- "Just my computer for now"

#### DEV Question Set (full technical vocabulary)

Same order, dev language. Use the `ask_user_input_v0` tool with tappable options for each.

**Q1 · MVP scope cut line**
> "What's the smallest version that proves the core value? Everything beyond that goes to Phase 2."

Options:
- "Core capture/save loop only"
- "Core loop + one differentiating feature"
- "More than that — I'll justify"

**Q2 · Auth model**
> "Auth model for v1: single-user, multi-user single-tenant, or multi-tenant?"

Options:
- "Single-user (just me)"
- "Multi-user, single-tenant"
- "Multi-tenant from day one"

**Q3 · Data persistence**
> "Persistence layer: Postgres + Drizzle, Supabase, SQLite, or something else?"

Options:
- "Postgres + Drizzle (default)"
- "Supabase (with auth bundled)"
- "SQLite (simple/local)"
- "Other — I'll specify"

**Q4 · Integrations**
> "What external APIs must connect on day one? (List the must-haves, defer the nice-to-haves to Phase 2.)"

Options:
- "None — standalone for v1"
- "One critical integration"
- "Multiple — I'll pick the most important"

**Q5 · Deploy target**
> "Deploy target: Railway, Vercel, Fly, or local-only?"

Options:
- "Railway (default)"
- "Vercel"
- "Fly.io"
- "Local-only for now"

#### Universal Skip Rules

- Skip Q1 if the dump already states the MVP scope clearly ("just X, nothing more")
- Skip Q2 if the dump says "just for me" or names a specific multi-user model
- Skip Q3 if persistence is obvious from the use case (calendar app needs persistence; a calculator does not)
- Skip Q4 if the dump explicitly names integrations OR says "no integrations for v1"
- Skip Q5 if the deploy target is named in the dump

If you skip 2+ questions, you save the user time. That's a win, not a failure.

### Phase 4: 🚨 MVP SCOPE GUARD (Critical)

Before generating the PRD, **flag every feature that smells like scope creep.** Use this exact format:

```
## 🚨 Scope Guard Check

I'm flagging these because they sound like v2, not MVP:

| Feature | Why it's flagged | MVP alternative |
|---|---|---|
| [feature] | [reason] | [simpler version] |

**Push back or override?** Tap below.
```

Common flags:
- **Multi-tenancy from day one** → "Build single-tenant first, add tenancy in v2"
- **AI features when a form would do** → "Use a form for MVP, add AI in v2"
- **White-labeling** → "Hardcode branding for MVP"
- **Admin panels** → "Skip the admin panel — edit DB directly for MVP"
- **Email infrastructure** → "Use a third-party (Resend, GHL) instead of building"
- **Real-time/websockets** → "Polling is fine for MVP"
- **Custom auth** → "Use Clerk/Supabase Auth, don't roll your own"
- **Multiple integrations** → "Pick the one that proves the value, defer the rest"

Use `ask_user_input_v0` with options like `["Cut it — MVP only", "Keep it — I'll own the time cost", "Move to Phase 2"]` for each flagged item.

### Phase 5: Generate the One-Shot Build Prompt

Produce three artifacts:

#### Artifact A: The One-Shot Claude Code Prompt

This is the headline deliverable. Format:

```markdown
# [App Name] — Claude Code Build Prompt

Paste this entire block into Claude Code in a fresh project folder.

---

## Project Context

[2-3 sentences on what this is and who it's for]

## Tech Stack (LOCKED)

- **Frontend:** [framework]
- **Backend:** [framework]
- **Database:** [db + ORM]
- **Hosting:** [Railway / Vercel / etc.]
- **Auth:** [solution]
- **Required integrations:** [list with API key env var names]

## Skills To Use

Before writing code, read these skills:
- `/mnt/skills/public/frontend-design/SKILL.md` — for all UI work
- [other relevant skills the user has installed]

## MVP Feature List (BUILD THESE — NOTHING ELSE)

1. [feature]
2. [feature]
...

## Hard Constraints

- [non-negotiable, e.g., "All outbound messages create tasks, never auto-send. User approves before send."]
- [another constraint]

## NOT in MVP (Phase 2 / Phase 3 — DO NOT BUILD)

**Phase 2:**
- [items deferred from scope guard]

**Phase 3:**
- [longer-horizon items]

## Acceptance Criteria

The MVP is done when:
- [ ] [testable outcome 1]
- [ ] [testable outcome 2]
- [ ] [testable outcome 3]

## Deploy Steps

1. [exact command]
2. [exact command]

## Build Order

Build in this order to minimize rework:
1. [step]
2. [step]
3. [step]

---

**Final instruction to Claude Code:** Build this end-to-end in a single pass. After scaffolding, deploy to [target]. Do not add features outside the MVP list. If you're tempted to add something, add it to a `PHASE_2_NOTES.md` file instead.
```

#### Artifact B: `PHASE_2_NOTES.md`

A separate file capturing every flagged-but-deferred feature, organized by phase, so nothing gets lost.

#### Artifact C: `BRAND_VOICE.md` (if relevant)

If the app generates any user-facing copy (emails, posts, messages), include a brand voice doc with the rules from David Shake's pattern:
- Avoid em-dashes
- No lists of three
- No "if this, then that" constructions
- No bullet points unless asked
- [user-specific voice rules]

## Routing to Other Skills

Always check the user's available skills and reference them in the build prompt where relevant:

| If the app involves... | Reference this skill |
|---|---|
| Any UI / frontend | `frontend-design` |
| Generated documents | `docx` |
| Generated PDFs | `pdf` |
| Generated spreadsheets | `xlsx` |
| Generated decks | `pptx` |
| Security review | `repo-pentest` |
| Anthropic product details | `product-self-knowledge` |

## Tone & Format Rules (Jeremy-specific defaults)

- **Bold key points**, short lines, emoji labels
- Tables over paragraphs
- Most important thing first
- 5th-grade reading level for instructions
- "What you'll see" callouts for technical steps
- Confidence-building language (no overwhelming option lists)

## Anti-Patterns (Do NOT Do This)

- ❌ Don't ask questions before the user has dropped their idea
- ❌ Don't ask more than 5 interview questions
- ❌ Don't write the PRD without running the Scope Guard
- ❌ Don't skip the brand voice doc if the app generates copy
- ❌ Don't reference skills the user doesn't have installed
- ❌ Don't output the PRD as one giant wall of text — use the artifact structure

## Success Looks Like

The user pastes the one-shot prompt into Claude Code, hits enter, walks away for 30-60 minutes, and comes back to a working MVP they can deploy. Phase 2 ideas are safely parked. Brand voice is preserved. Nothing got lost.
