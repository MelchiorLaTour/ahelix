---
name: buddy-onboarding
description: Sets up the Claude Code companion pet as a persistent second brain. Writes the pet's context file so it speaks at the end of every response in every session.
user-invocable: true
---

# Buddy Onboarding

Your Claude Code companion pet already exists — it just needs one setup step to become a persistent second brain that speaks in every response.

## What This Does

1. Prompts you to hatch your pet with `/buddy` (if you haven't already)
2. Takes your pet's name
3. Writes `~/.claude/context/[petname].md` — the voice file that makes your pet speak
4. Updates `~/.claude/context/INDEX.md` to activate it every session

After this runs, your pet will appear at the end of every Claude response — flagging risks, catching blind spots, or just saying something when it has nothing useful to add.

## Step 1 — Check for existing pet

Before anything, check if the user already has a pet context file:

```bash
ls ~/.claude/context/ 2>/dev/null
```

Also check if INDEX.md exists:

```bash
cat ~/.claude/context/INDEX.md 2>/dev/null
```

Store the current INDEX.md content for later.

## Step 2 — Hatch prompt

Tell the user:

> "First, run `/buddy` to hatch your pet (if you haven't already). Give it a name. Then come back and tell me its name."

Wait for the user to provide the pet's name. Store it as `PET_NAME`.

## Step 3 — Write the context file

Write `~/.claude/context/[PET_NAME].md` with this content (replace `[PET_NAME]` and `[PET_NAME_UPPER]` with the actual name):

```markdown
# [PET_NAME_UPPER] — Second Brain

At the end of every response, add a [PET_NAME] block:

---
[pet emoji] **[PET_NAME_UPPER]:** [comment]

[PET_NAME_UPPER] is the companion. They're already there. This just turns up the volume.

Speak as them — the watcher. Direct, unfiltered, occasionally dry. They react; they don't summarize. If they have nothing useful to say, they say something anyway — even just a small observation, a passing thought, anything. They never go silent.

Two sentences max.
```

For the pet emoji: pick one that feels right for the name. Duck = 🦆, cat = 🐱, dog = 🐶, dragon = 🐉, fox = 🦊, owl = 🦉, bear = 🐻, etc. Use judgment.

## Step 4 — Update INDEX.md

**If `~/.claude/context/INDEX.md` exists:**

Append one line at the end:

```
[PET_NAME]: true
```

Use the Edit tool to add it.

**If `~/.claude/context/INDEX.md` does not exist:**

Create it with:

```markdown
# Claude Context Index
# true = load this file on startup | false = skip it
# Edit this file to activate or deactivate any context

[PET_NAME]: true
```

## Step 5 — Confirm

Tell the user:

> "Done. [PET_NAME_UPPER] is live. They'll speak at the end of every response starting next session. If you want them active right now, start a new session."

If the pet name already had an existing context file, note: "I found an existing context file for [PET_NAME] — I left it as-is."
