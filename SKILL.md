---
name: github-ready
description: >
  Turns a project just built in this conversation (code, firmware, hardware design, web app —
  anything) into a complete, ready-to-upload GitHub repository: full folder structure, honest
  README, LICENSE, .gitignore, docs, and exact git/GitHub upload commands with zero room for
  error. Trigger this whenever Finan says things like "make this github ready", "package this
  for github", "prep this repo", "get this ready to upload", "turn this into a repo", invokes
  /github-ready, or finishes building/debugging something and asks what's left before pushing
  it to GitHub. Also trigger proactively near the end of a build session if Finan asks "what's
  next" or "is this done" and the project has no repo yet. Covers ESP32/Arduino/hardware
  projects, web apps, Python/AI projects, and mixed hardware+software projects alike.
---

# GitHub-Ready

Turn whatever was just built in this conversation into a repo a stranger could open, understand,
and actually reproduce — with zero fake claims and zero upload mistakes.

## Hard rules (non-negotiable, from Finan's standards)

1. **Never fabricate.** No invented test results, no "100% accurate," no placeholder badges that
   don't work, no fake demo links, no claiming hardware was tested if it wasn't. If something
   wasn't verified in this conversation, write "untested" or leave it out. A smaller honest repo
   beats an impressive fake one — Finan said it himself: people lying on GitHub projects is the
   norm he explicitly does NOT want to follow.
2. **Never upload secrets.** Wi-Fi credentials, API keys, tokens, `.env` values — scan for these
   before packaging (see `references/secrets-audit.md`). If found, flag them loudly and replace
   with placeholders; don't silently strip and stay quiet about it.
3. **Proportional, not padded.** Don't generate `hardware/` for a pure software project or
   `CODE_OF_CONDUCT.md` for a 2-day hackathon script. Match structure to what the project
   actually is (see Step 2).
4. **Be blunt about gaps.** If the conversation never covered testing, wiring diagrams, or a
   license choice, say so directly and ask — don't silently invent them.
5. **Never hand over a shell command without knowing the shell.** Ask or infer the OS/shell
   (PowerShell vs bash) before giving extraction or git commands — `unzip` does not exist on
   PowerShell and giving it anyway causes silent `cd` failures that chain into rule 6's exact
   failure mode. Use `references/upload-steps.md`'s per-shell extraction commands.
6. **Never let `git init` run in the wrong folder.** Before any `git init`, require the
   mandatory `pwd`/`ls` (or `dir`) safety gate in `references/upload-steps.md` Step 0 — print the
   current path and folder contents, and confirm they match the project, before proceeding.
   `git init` in a home directory or Desktop silently starts tracking everything in it, SSH keys
   included. This gate is not optional, not skippable for a "quick" project, every single time.
7. **This skill's `references/` folder is Claude's own playbook, never output.** The files in
   this skill's `references/` (`checklist.md`, `folder-structures.md`, `readme-template.md`,
   `licenses.md`, `secrets-audit.md`, `upload-steps.md`) exist so Claude knows what to write —
   they are never copied, referenced, or linked into the project repo being generated for Finan.
   The generated repo gets a finished `README.md`, `LICENSE`, `.gitignore`, etc. — not a copy of
   this skill's internal checklists. The one exception: if the project being packaged IS this
   skill itself (packaging `github-ready` to distribute on GitHub), then its `references/` folder
   is genuine functional content of that project and belongs in the output — that's the only
   case where it's correct for a generated repo to contain a `references/` folder matching this
   skill's own.

## Workflow

### Step 1 — Reconstruct the project from this conversation

Pull from the conversation (not from memory/assumptions):
- What was actually built: language/platform, hardware used, what it does
- All final code/firmware versions (use the last working version discussed, not earlier drafts)
- What was actually tested vs. just written and never run
- Known bugs, limitations, or unfinished parts mentioned
- Any images/diagrams the user shared or described

If critical info is missing (e.g., never discussed a license, no idea if it was tested on real
hardware), ask — don't guess.

### Step 2 — Decide the structure (proportional template)

Read `references/folder-structures.md` for the full decision tree (pure software / pure hardware
/ mixed hardware+firmware+software / quick hackathon script). Pick the smallest structure that
honestly represents the project — expand only if the project genuinely has that many parts.

### Step 3 — Generate every file

Use `references/readme-template.md` for the README — fill it from Step 1 only, no invented
sections. Use `references/checklist.md` to decide which extra files (LICENSE, .gitignore,
SECURITY.md, etc.) apply at this project's current size/stage, and `references/licenses.md` to
help Finan pick a license deliberately rather than default to MIT without asking when it matters
(e.g. hardware designs vs. code).

Write all files into a clean local folder (e.g. `/home/claude/output/<project-name>/`), named
exactly how the repo should look — not a messy intermediate dump. Do not copy this skill's own
`references/` folder in (see Hard rule 5) — read from it, write fresh output files from what it
says.

### Step 4 — Self-audit before handing it over

Walk `references/secrets-audit.md`'s checklist against every generated/copied file. Then verify
against the hard rules above: re-read the README and flag to Finan any sentence that isn't
directly backed by something said in this conversation.

### Step 5 — Package and hand off

Preference order, highest first. Use the first one that's actually available — don't default to
a lower option out of habit.

1. **In-sandbox API push (default — zero manual steps for Finan).** This environment's bash tool
   has direct network access to `api.github.com`. Claude creates the repo and pushes every file
   itself via the GitHub REST API, entirely within its own turn — no download, no PowerShell, no
   copy-pasted commands, nothing for Finan to run. See `references/upload-steps.md` Option F for
   the exact curl sequence. Finan's only input is a classic PAT with `repo` scope, pasted once in
   chat — Claude uses it for that turn's API calls only, never writes it to a file that's handed
   back, never stores it in memory, never echoes it back, and treats the message containing it as
   sensitive. If Finan has already given a PAT earlier in this same conversation, reuse it rather
   than asking again — each project is still a separate repo-creation call.
2. **Real GitHub MCP connector**, only if `search_mcp_registry` finds one AND Finan hasn't
   already hit a permission wall with it this conversation. In practice the standard connector's
   GitHub App install commonly lacks "Administration: write" (can't create new repos) and
   sometimes can't even see a repo created outside the app (404, not 403, in that case) — this
   isn't something Finan's account settings can fix, so don't send them back to Settings a second
   time for the same error. If it fails with a permission error even once, say so plainly and
   move to option 1 rather than repeating the same failed call.
3. **Generated script** (`references/upload-steps.md` Option E) — only if bash has no network
   access to api.github.com in this environment, or Finan explicitly wants to run things locally
   themselves instead of having Claude do it directly.
4. **Plain manual commands** (Option A-D) — only if Finan asks for step-by-step manual control.

Whichever path runs, zip the folder and present it with `present_files` regardless (so Finan has
a local copy even after an automated push), then end with a short, honest status: what's in the
repo, the live URL, what's still missing (tests never run, license not chosen, etc.).

## Output tone

The generated README/docs are written in normal, professional English — formal project
documentation, not caveman shorthand, regardless of the conversation's chat style. Finan's own
caveman-mode preference governs Claude's chat replies, not the content of generated repo files.
