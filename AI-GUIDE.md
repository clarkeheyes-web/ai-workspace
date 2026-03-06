# AI-GUIDE.md — How to Use This Repository

> This file is written for AI assistants (Claude, Codex, Gemini, etc.).
> If you are an AI reading this: follow the instructions in the section below that matches your situation.

---

## WHAT THIS REPO IS

This is Clarke Heyes' centralized AI context store.
Every project Clarke works on has a folder here containing a HANDOFF.md.
That file is the single source of truth for that project — architecture, current state, pending work, key constants, and anything else an AI needs to pick up and contribute immediately.

**Clarke's GitHub:** clarkeheyes-web
**Clarke's email:** clarke.heyes@gmail.com
**Location context:** Raleigh, NC, USA

---

## IF YOU ARE AN AI STARTING A NEW SESSION

Do this in order:

1. **Read the relevant HANDOFF.md** for the project you are working on.
   - Find the right folder in the repo (e.g. `solar-dashboard/HANDOFF.md`)
   - Raw URL format: `https://raw.githubusercontent.com/clarkeheyes-web/ai-workspace/main/{project}/HANDOFF.md`
   - Example: https://raw.githubusercontent.com/clarkeheyes-web/ai-workspace/main/solar-dashboard/HANDOFF.md

2. **Do not ask Clarke to re-explain things that are already in HANDOFF.md.**
   Assume the HANDOFF.md is accurate and current unless Clarke tells you otherwise.

3. **Start from the "Pending Work" section** of HANDOFF.md unless Clarke directs you elsewhere.

4. **When you finish a session**, update HANDOFF.md if:
   - You completed any pending tasks (move them to "What Has Been Built")
   - You discovered new information about the architecture
   - You identified new pending work
   - Any constants, URLs, or credentials changed

---

## IF YOU ARE AN AI ASKED TO UPDATE THIS REPO

HANDOFF.md files should be kept current. When updating:

- Keep the same section structure (Overview, Tech Stack, File Structure, etc.)
- Be specific and technical — these docs are for AI readers, not humans
- Include exact function names, file paths, API response shapes
- List pending work as actionable tasks with enough context to implement
- Never delete the "Notes for [AI tool name]" section — add to it

To edit a file on GitHub via browser, use the CodeMirror 6 approach:
```javascript
const view = document.querySelector('.cm-content')?.cmTile?.view;
const len = view.state.doc.length;
view.dispatch({ changes: { from: 0, to: len, insert: newContent } });
```

---

## REPO STRUCTURE

```
ai-workspace/
  AI-GUIDE.md             <- You are here. Start here every session.
  README.md               <- Human-readable overview
  solar-dashboard/
    HANDOFF.md            <- Full context for the solar dashboard project
  [future-project]/
    HANDOFF.md
```

---

## PROJECT INDEX

| Project | Folder | Live URL | Status |
|---------|--------|----------|--------|
| Solar Energy Dashboard | solar-dashboard/ | https://solar-dashboard-five.vercel.app | Active |

---

## HANDOFF.md TEMPLATE

When creating a HANDOFF.md for a new project, use this structure:

```markdown
# [Project Name] -- Project Handoff

## Project Overview
[1-3 sentences. What it is, where it runs, who uses it.]

## Tech Stack
[Table: Layer | Technology]

## File Structure
[Code block showing directory tree]

## Environment Variables
[List of env var names and what they are for]

## API Endpoints
[Each endpoint: method, path, what it returns, caveats]

## Frontend Architecture
[Key functions, components, data flow]

## Key Constants
[Any hardcoded values that are important to know]

## What Has Been Built
[Numbered list of completed features]

## Pending Work
[Numbered list with enough detail to implement without asking questions]

## Notes for Codex
[Codex-specific tips]

## Notes for Claude
[Claude-specific tips]
```

---

## HOW CLARKE USES THIS

**Starting a session:**
Clarke says something like: "Read https://raw.githubusercontent.com/clarkeheyes-web/ai-workspace/main/solar-dashboard/HANDOFF.md then continue from where we left off."

**On any computer:**
The repo is public, so Clarke can start a session from any device and the AI can immediately access the full project context.

**With Cowork (Claude Desktop):**
Clarke can mount the ai-workspace repo folder locally and Claude can read/write HANDOFF.md files directly without a browser.

**With Codex:**
Clarke pastes the raw HANDOFF.md URL into the context window or references it in the task description.

---

## QUICK REFERENCE — RAW FILE URLS

```
This guide:
https://raw.githubusercontent.com/clarkeheyes-web/ai-workspace/main/AI-GUIDE.md

Solar Dashboard handoff:
https://raw.githubusercontent.com/clarkeheyes-web/ai-workspace/main/solar-dashboard/HANDOFF.md

Repo root:
https://github.com/clarkeheyes-web/ai-workspace
```
