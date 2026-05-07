# 🧠 Claude Projects Setup · Tonight's Prep

> **Goal:** Get a Claude Project ready so tomorrow's planning interview runs smoothly. The planning skill loaded as project knowledge, your project docs available, and you've test-driven the interview once before going live.
>
> **Time:** ~15 minutes.
> **Difficulty:** Easy. Mostly clicking and pasting.

---

## 🎯 What Claude Projects Is

**Claude Projects** is a feature inside claude.ai (and the Claude desktop app). It lets you create a "folder" with persistent files and custom instructions that every new chat in that project automatically references.

**Why we're using it for the demo:** The planning interview is a multi-turn conversation that needs your project docs (SKILL.md, BRAND_VOICE.md) available as background context. Loading them once into a project means they're available to every chat without you re-pasting them.

**Quick disambiguation:**
- **Claude Chat** = one-off conversations, no persistent files
- **Claude Projects** = a folder with persistent knowledge files (what we're using)
- **Claude Code** = Anthropic's coding agent, separate product, used in Phase 4
- **Claude Cowork** = Anthropic's desktop agent for autonomous file work, NOT what we're using

---

## ✅ Before You Start

You'll need three things ready:

- [ ] Your Claude account login (Pro, Max, Team, or Enterprise — Projects requires a paid plan)
- [ ] Access to **claude.ai**
- [ ] These files saved locally (use the "Take These With You" copy buttons on your audience page):
  - The planning skill `SKILL.md` content (from inside `idea-to-prd-skill.zip`)
  - `BRAND_VOICE.md`
  - `CLAUDE.md` (the project context doc)

If you haven't extracted SKILL.md from the skill .zip yet, do that first. Just unzip the file and grab the `SKILL.md` from inside.

---

## 🗂️ Step 1 · Create the Claude Project

1. Open **claude.ai** in your browser
2. Sign in with your Claude account
3. In the left sidebar, click **Projects**
4. Click **+ New Project** (or "Create Project")
5. Name it: **`100 Cups Demo`**
6. Description (optional but helpful): *"Live demo flow — planning interview + PRD generation for the mastermind"*
7. Click **Create**

**What you'll see:** An empty project workspace with a chat input and a Project Knowledge section on the right side (or a way to access it depending on the current UI).

---

## 🗂️ Step 2 · Upload the Project Knowledge

The **Project Knowledge** section is the magic. Anything you upload here is automatically available as context for every chat in this project.

Find the **Project Knowledge** panel (usually right sidebar). Click **Add files** or drag and drop. Upload these three documents:

| File | What it does |
|---|---|
| **SKILL.md** | The planning interview rules — how to mirror back, when to push back, what questions to ask |
| **BRAND_VOICE.md** | David's voice rules — no em-dashes, no lists of three, etc. — applied to all AI-generated copy |
| **CLAUDE.md** | The project context doc — design system, file structure, decisions made |

**What you'll see:** Three files listed in the Project Knowledge panel. Claude will index them (takes a few seconds).

---

## 🗂️ Step 3 · Add Custom Instructions (Optional but Powerful)

Claude Projects lets you set **Custom Instructions** that apply to every chat in the project. This means you don't need to remind Claude to use the skill at the start of every chat.

Find the **Project Settings** or **Custom Instructions** section. Paste this:

```
For any product idea or brief I drop into this project, follow the workflow described in SKILL.md (the idea-to-prd skill):

1. Mirror back what you heard in plain language
2. Run the Scope Guard to flag feature creep
3. Ask 5 clarifying questions max
4. Generate a complete PRD with a Phase 2 parking lot

Apply BRAND_VOICE.md rules to any user-facing copy in the PRD output.

For meeting transcripts specifically, use the transcript-extraction structure first, then route into the planning skill.
```

Save. Now every chat in this project starts pre-configured.

**Why this matters:** Tomorrow on stage, you don't have to remember to type *"Use the idea-to-prd skill."* It's already on by default. One less thing to think about.

---

## 🗂️ Step 4 · Test the Setup With a Throwaway Conversation

This is the part most people skip and regret. **Run a fake interview now** to make sure everything works.

1. In your `100 Cups Demo` project, click **+ New Chat** (or whatever creates a new conversation in the project)
2. Paste this as your first message:

> I want to test that the planning skill is loaded correctly. Without me dropping a brief, can you tell me: what skill do you have available, what are its rules, and what would you ask me first if I gave you a real product idea?

**What you should see:**

- Claude confirms it can see SKILL.md
- It describes the 5-phase flow (mirror, scope guard, interview, generate, deliver)
- It mentions the brand voice rules from BRAND_VOICE.md
- It says something like *"If you gave me a real idea, I'd start by mirroring it back to you in plain language to make sure I heard it right."*

**If Claude says any of:**
- "I don't see any uploaded files"
- "I don't have a planning skill loaded"
- "What skill?"

→ Go back to Step 2. Files probably didn't upload correctly. Try again.

---

## 🗂️ Step 5 · Run a Practice Mini-Interview (Recommended)

This catches the "Project setup is wrong" issue while there's still time to fix it.

In the same chat, drop a tiny throwaway brief and walk through the interview:

> Here's my brief:
>
> I want to build a tool that lets me track how many cups of coffee I drink per day. I want to see streaks. Maybe a chart. It should sync between my phone and laptop. I'd like to share it with friends as a fun thing. Also it should remind me when I'm under-caffeinated.

**What should happen:**
- Claude mirrors back what it heard
- Claude flags scope creep (the "remind me," the "share with friends," the multi-device sync — too much for an MVP)
- Claude asks 5 questions max
- After your answers, Claude generates a clean PRD

**Walk through it.** Answer the 5 questions casually — this is just to confirm the flow works. After it generates the PRD, **delete this chat** (it's not the real one). The setup is now verified.

---

## 🗂️ Step 6 · Pre-Stage Tomorrow's Real Chat (Optional but Smart)

Open a fresh chat in the `100 Cups Demo` project. Title it something like **`May 8 Live Demo`**. Don't send any messages yet. Just have it open and ready.

Tomorrow when the demo starts, you'll already be in the right place. No clicking around live.

---

## 🚦 Tomorrow's Project Flow (Quick Reference)

For tomorrow morning, this is the order you'll do things in your Claude Project:

```
Phase 1 (10 min) · MEETING MINING
  Open new chat → paste extractor prompt as your first message
  → Then paste the meeting transcript
  → Get structured brief output

Phase 2 (10 min) · PLANNING INTERVIEW
  Same chat (or new one in same project)
  → Paste the structured brief from Phase 1
  → Answer 5 questions
  → Get PRD output
```

After Phase 2, copy the entire PRD, switch to GitHub, save as `PRD.md` in your repo, then switch to Claude Code (Tab 2) and tell it to read PRD.md and build.

---

## 🆘 Troubleshooting

### "I don't see a Projects section in my sidebar"
→ Projects requires a paid plan (Pro, Max, Team, or Enterprise). Free tier doesn't include it. Check your subscription.

### "I see Projects but can't find the Knowledge section"
→ The UI varies. Look for: an attachment icon (paperclip), an "Add files" button, "Project Knowledge," or a settings gear inside the project. Try clicking around the project header.

### "My uploaded files don't seem to be referenced"
→ Try mentioning them by name in your prompt: *"Using SKILL.md, run the planning interview on this brief: [paste]"*. That gives Claude an explicit pointer.

### "The custom instructions aren't being followed"
→ Custom instructions are a hint, not a guarantee. If Claude forgets, just say *"Use the idea-to-prd skill from SKILL.md"* at the start of the chat.

---

## ✅ Setup Complete When

- [ ] `100 Cups Demo` project exists in claude.ai → Projects
- [ ] SKILL.md, BRAND_VOICE.md, CLAUDE.md uploaded as Project Knowledge
- [ ] Custom Instructions set (optional but recommended)
- [ ] Test conversation confirmed Claude can see the skill
- [ ] Practice mini-interview ran successfully (and you deleted the throwaway chat)
- [ ] (Optional) Tomorrow's real chat is pre-opened and ready

When all of these are checked, **you're set for tomorrow's planning phase.**

---

## 💭 One Last Thought

Claude Projects' "knowledge + custom instructions" pattern is doing real work for you here. Without it, you'd have to paste SKILL.md and BRAND_VOICE.md at the start of every chat AND remember to invoke the skill. With it loaded once, the planning interview just *works* every time, automatically.

This pattern — **upload the rules once, run conversations against them many times** — is one of the most underused features in Claude. Now you're using it. Your future self will thank you for taking 15 minutes tonight. ☕
