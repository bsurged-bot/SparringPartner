# Sparring Partner

A literary discussion partner for writers — built to push back, not write for you.

## What this is

Sparring Partner is a small, transparent set of Python scripts that let you talk through a story with Claude without ever having Claude write your prose. It holds your characters, world, and decisions in plain text files you can read and edit directly. There is no hidden state, no database, no black box — every file it touches is human-readable, and every API call can be inspected before it's sent.

This exists because most AI writing tools optimize for generating text fast. This one optimizes for the opposite: helping a writer think more clearly, by being a sharp, well-informed conversational partner who refuses to do the writing for you.

## Core principles

**Discussion only, never generation.** The assistant will discuss craft, ask questions, hold your characters consistent, and quote published works with citation to illustrate a point. It will not write scenes, complete sentences, or offer "here's how that could go" — even if asked directly. This constraint is in the system prompt and does not bend.

**The writer approves every change.** Nothing gets written into your project bible silently. Session summaries, imported documents, and edits are all shown to you first; you decide what becomes canon.

**One project, one folder.** Every project lives in its own folder under `projects/<name>/`, with its own bible, change log, and session history. Projects never bleed into each other.

**Bible is optional.** If you just want to talk something through without any persistent structure, that's a supported mode, not a workaround.

**Nothing hidden.** Every file is plain text or JSON. Every API payload can be printed and inspected with `/show` before it's sent. You can read the entire system prompt in the source code.

## What's in this repo

| File | Purpose |
|---|---|
| `project_registry.py` | Shared module — resolves project names to folders, keeps the master project list |
| `onboard.py` | Builds a new project bible through conversation, or opens an existing one |
| `import_bible.py` | Converts an existing document (old notes, a summary, a bible in another format) into this app's bible format |
| `sparring_partner.py` | The actual chat loop — the discussion partner itself |

## How a project is structured

```
projects/
  registry.json              ← master list of all your projects
  my-novel/
    bible_static.md          ← world, characters, philosophy (rarely changes)
    bible_active.json        ← current chapter, decisions, open questions
    change_log.json          ← dated record of every edit, with your reasons
    sessions/                ← one JSON file per day you talk to it
    archive/                 ← old sessions you've moved out of active use
```

Every file here is yours. Open `bible_static.md` in any text editor and change it directly — the app never overwrites it without your approval.

## Setup

### 1. Install Python dependencies

```bash
python3 -m pip install anthropic
```

If you hit an "externally managed environment" error (common on newer Debian/Ubuntu):

```bash
python3 -m pip install anthropic --break-system-packages
```

or use a virtual environment:

```bash
python3 -m venv venv
source venv/bin/activate
pip install anthropic
```

This project was built against `anthropic` Python SDK version `0.105.2` or later.

### 2. Get an Anthropic API key

Create one at [console.anthropic.com](https://console.anthropic.com) under API Keys. This requires its own billing setup (separate from any claude.ai subscription) — a few dollars of credit is enough to experiment.

### 3. Set your key

```bash
export ANTHROPIC_API_KEY=your_key_here
```

## Running it — dry run mode (no account needed)

Every script supports a dry run mode that exercises the entire flow — file creation, project folders, the conversation loop, session saving — without making any real API call or requiring an API key at all.

```bash
export DRY_RUN=true
python3 onboard.py
```

In dry run, the assistant's replies are replaced with a placeholder confirming what was received, so you can verify the plumbing works before spending anything. Use this to get comfortable with the structure before connecting a real key.

To go live, just unset it:

```bash
unset DRY_RUN
export ANTHROPIC_API_KEY=your_key_here
```

## Running it — real usage

**Start or open a project:**

```bash
python3 onboard.py
```

You'll get a menu:

```
1 — Start a new project (build a bible through conversation)
2 — Start a new project (no bible, just talk)
3 — Load existing bible and start sparring
4 — Edit an existing bible field
5 — Close a completed short work
```

Pick 1 for a brand new story with nothing written yet — it asks about scope, feeling, characters, and the moment everything changes, one question at a time, then shows you what it heard before saving anything.

Pick 3 if you already have a project set up and just want to keep talking.

**Import an existing document into a bible:**

```bash
python3 import_bible.py
```

Paste in old notes, a summary from another conversation, or a bible in a different format. Any line starting with `#` is treated as an open question / to-do-later item rather than a settled decision, regardless of how it's phrased. You see the proposed bible before anything is saved.

**Jump straight into a known project:**

```bash
python3 sparring_partner.py "My Novel"
```

**Inside any session:**

```
/show       — print the full API payload before the next call, so you see exactly what's sent
/hide       — stop printing it
/status     — show turn count and context size for this session
/summarize  — distill the session into bible-ready notes; you approve before anything is saved
/quit       — exit, session is already saved
```

## Cost

This defaults to Claude Haiku, the cheapest current model, since this is a discussion tool rather than a heavy-reasoning one. Anthropic's API uses prompt caching, so your bible and system prompt — the largest fixed cost — are cached after the first call in a session, cutting that portion of the cost by roughly 90% for every turn after the first. Check current pricing at [anthropic.com](https://www.anthropic.com) before heavy use, as rates can change.

## What this is not

This is not a ghostwriting tool, and it will actively refuse if you ask it to write your story for you — by design, not by accident. If that's what you're looking for, this isn't the right tool.

## License

Add your preferred license here before publishing (MIT is a common, permissive default if you're not sure).
