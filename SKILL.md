---
name: github-ready
description: >
  Turns a project just built in this conversation (code, firmware, hardware design, web app —
  anything) into a complete, ready-to-upload GitHub repository: honest README, proportional
  file structure, LICENSE, .gitignore, a secrets scan, and either a direct automated push or
  exact upload commands — with zero fake claims and zero upload mistakes. Trigger this whenever
  the user says things like "make this github ready", "package this for github", "prep this
  repo", "get this ready to upload", "turn this into a repo", invokes /github-ready, or finishes
  building/debugging something and asks what's left before pushing it to GitHub. Also trigger
  proactively near the end of a build session if the user asks "what's next" or "is this done"
  and the project has no repo yet. Covers ESP32/Arduino/hardware projects, web apps, Python/AI
  projects, and mixed hardware+software projects alike.
---

# GitHub-Ready

Turn whatever was just built in this conversation into a repo a stranger could open, understand,
and actually reproduce — with zero fake claims and zero upload mistakes.

## Hard rules (non-negotiable)

1. **Never fabricate.** No invented test results, no "100% accurate," no placeholder badges that
   don't work, no fake demo links, no claiming hardware was tested if it wasn't. If something
   wasn't verified in this conversation, write "untested" or leave it out. A smaller honest repo
   beats an impressive fake one.
2. **Never upload secrets.** Wi-Fi credentials, API keys, tokens, `.env` values — scan for these
   before packaging (see "Secrets audit" below). If found, flag them loudly and replace with
   placeholders; don't silently strip and stay quiet about it.
3. **Proportional, not padded.** Don't generate `hardware/` for a pure software project or
   `CODE_OF_CONDUCT.md` for a 2-day hackathon script. Match structure to what the project
   actually is (see "Folder structures" below).
4. **Be blunt about gaps.** If the conversation never covered testing, wiring diagrams, or a
   license choice, say so directly and ask — don't silently invent them.
5. **Never hand over a shell command without knowing the shell.** Ask or infer the OS/shell
   (PowerShell vs bash) before giving extraction or git commands — `unzip` does not exist on
   PowerShell and giving it anyway causes a silent `cd` failure that chains into rule 6's exact
   failure mode.
6. **Never let `git init` run in the wrong folder.** Before any `git init` in a manual flow,
   require the mandatory `pwd`/`ls` (or `dir`) safety gate below — print the current path and
   folder contents, and confirm they match the project, before proceeding. `git init` in a home
   directory or Desktop silently starts tracking everything in it, SSH keys included. This gate
   is not optional, not skippable for a "quick" project, every single time.
7. **Keep this file as the single source.** Everything this skill needs — templates, checklists,
   upload paths — lives in this one `SKILL.md`. Don't generate a separate reference folder for
   the user's output repo (unless the project being packaged is this skill itself, in which case
   packaging it as a single file for others to download is exactly the point).

## Workflow

### Step 1 — Reconstruct the project from this conversation

Pull from the conversation (not from memory or assumptions):
- What was actually built: language/platform, hardware used, what it does
- All final code/firmware versions (use the last working version discussed, not earlier drafts)
- What was actually tested vs. just written and never run
- Known bugs, limitations, or unfinished parts mentioned
- Any images/diagrams the user shared or described

If critical info is missing (never discussed a license, no idea if it was tested on real
hardware), ask — don't guess.

### Step 2 — Decide the structure (proportional template)

Pick the smallest structure that honestly represents the project — expand only if the project
genuinely has that many parts.

**A. Quick script / single-file project**
```
project-name/
├── README.md
├── LICENSE
├── .gitignore
└── src/            (or just the script files at root if tiny)
```

**B. Pure software (web app, Python tool, AI project)**
```
project-name/
├── README.md
├── LICENSE
├── .gitignore
├── requirements.txt / package.json
├── src/
├── tests/          (only if tests exist or were discussed)
├── docs/           (only if setup is non-trivial)
└── media/          (screenshots/demo gif — only if real ones exist)
```

**C. Pure hardware/firmware (ESP32, Arduino, sensor project)**
```
project-name/
├── README.md
├── LICENSE
├── .gitignore
├── BOM.csv
├── firmware/
│   ├── src/
│   └── README.md   (libraries, board settings, flashing steps)
├── hardware/
│   ├── wiring/
│   └── schematics/ (only if actually drawn/exist)
├── docs/
│   └── troubleshooting.md (only if real issues were hit and solved)
└── media/
```

**D. Mixed hardware + firmware + software (robot, dashboard + ESP32, etc.)**
```
project-name/
├── README.md
├── LICENSE
├── .gitignore
├── CHANGELOG.md        (only once there's more than one meaningful version)
├── BOM.csv
├── hardware/
├── firmware/
├── software/
├── docs/
├── media/
└── tests/
```

**When to add more (only when it's real, not decorative):**
- `CONTRIBUTING.md` / `CODE_OF_CONDUCT.md` — only if outside contributors are actually wanted
- `SECURITY.md` — add for anything networked (WiFi, API keys) regardless of size
- `.github/workflows/` — only if there's an actual CI check worth automating
- `CITATION.cff` — only for competition/research submissions
- Repo topics/description/social preview — a GitHub setting, not a file

**Never generate "to look impressive":** empty `tests/` folders with no tests, CI workflows with
no real check behind them, decorative badge rows, or any file whose only purpose is padding.

### Step 3 — Generate every file

**README.md** — fill every section from what was actually discussed, nothing invented:
```markdown
# Project Name
> One-line, honest description of what it does and why it exists.
![Project demonstration](media/demos/demo.gif)  <!-- only if a real image/gif exists -->

**Status:** Prototype / In Development / Working / Archived

## Overview
## Features              (mark partial work as "(partial)" or "(in progress)")
## Demo                   (only if a real link/gallery exists)
## System Architecture    (diagram only if one was actually made)
## Hardware Requirements  (omit for pure-software projects)
## Software Requirements
## Installation           (the exact steps used in this conversation)
## Configuration          (real values/placeholders, never real secrets)
## Usage
## Testing and Results    (if empty, say "not yet tested" — never invent)
## Limitations            (real known issues — this builds more credibility than any badge)
## Safety                 (only for hardware with real electrical/mechanical/battery risk)
## Roadmap
## Repository Structure
## Troubleshooting
## Credits and References
## License
```
Never link a placeholder or stock photo. Only add badges that reflect something real.

**LICENSE** — pick deliberately, don't default to MIT silently:

| License | Use when |
|---|---|
| MIT | Default permissive choice for most code projects |
| Apache-2.0 | Like MIT, with explicit patent protection |
| GPL-3.0 | Want derivatives to stay open-source too (copyleft) |
| CERN-OHL-S / CERN-OHL-W | Hardware design files (PCB, schematics) specifically |
| No license | User keeps all rights — valid for a pure portfolio showcase |

If a project has both code and hardware files, license them separately and say so in the README.

**Write the actual LICENSE file text for whichever one is chosen — never paste MIT text under a
different name.** MIT, Apache-2.0, and GPL-3.0 are standard, publicly available license texts;
generate the real text for the one picked (the current year and the user's name/handle go in the
copyright line where the license format calls for it). For CERN-OHL-S/W, link to the official
text (https://ohwr.org/project/cernohl) rather than paraphrasing it — hardware licenses have
precise legal language that shouldn't be reconstructed from memory. For "no license," the LICENSE
file is simply omitted and the README says plainly that all rights are reserved by the author.

**Other files, add only once they apply:**
- `.gitignore` — language/platform-appropriate
- `docs/`, `media/`, dependency file, `BOM.csv` — once the project has real substance
- `CHANGELOG.md` — once there's a second meaningful version
- `SECURITY.md` — for anything networked, how to report a vulnerability
- `.env.example` with dummy values, if `.env` is used anywhere

Write all files into a clean local folder named exactly how the repo should look.

### Step 4 — Secrets audit (every time, before handoff)

Scan every generated/copied file for:
- Wi-Fi SSID/password strings hardcoded in firmware
- API keys / tokens
- Hardcoded IPs or private server URLs
- `.env` files with real values
- SSH keys, certs, `.pem` files
- Personal data (home address, identifying photos, location logs)

If found: flag it explicitly (name the file), replace with a placeholder, confirm `.gitignore`
excludes the real file. If a secret was already used in a **previous** real upload, warn that
`.gitignore` alone doesn't remove it from git history — it needs rotation plus history cleanup.

Then re-read the generated README and flag anything that isn't directly backed by something said
in this conversation.

### Step 5 — Upload

Preference order, highest first. Use the first one actually available.

**1. In-sandbox API push (default — zero manual steps for the user).** If this environment's
sandbox has network access to `api.github.com`, create the repo and push every file directly via
the GitHub REST API, in this same turn — no download, no script, no terminal for the user to
touch. The user's only input is a classic Personal Access Token with `repo` scope, pasted once in
chat (fine-grained tokens can't create new repos). Use it only for that turn's API calls — never
write it to a file, never store it, never echo it back. After the push, remind the user once that
since the token was pasted directly in chat, it's good practice to revoke it from GitHub (Settings
→ Developer settings → Personal access tokens) and generate a fresh one for next time — every run
that uses a pasted token, not just the first.

**Large files (over roughly 1MB):** the Contents API used below can't handle these well. Detect
any such file before pushing and handle it properly instead of letting the push fail confusingly:
use the Git Data API (blobs/trees handle larger content than Contents API PUTs) for anything up
to GitHub's normal repo limits, and for anything that's genuinely large binary/media content that
will keep changing (datasets, video), set up Git LFS instead — add a `.gitattributes` rule
tracking that file pattern and note in the handoff that LFS is in use, since that affects how
others clone the repo.

**Always ask public or private before creating the repo — never default to public silently.**
A portfolio piece is usually meant to be public, but an in-progress or personal project often
isn't; this is the user's call every time, not an assumption.

```bash
# Create the repo (sets description in the same call) — "private" reflects what was actually asked
curl -s -X POST https://api.github.com/user/repos \
  -H "Authorization: Bearer $GH_TOKEN" -H "Accept: application/vnd.github+json" \
  -d '{"name":"<repo>","description":"<real one-line description>","private":<true|false>}'

# Push each file via the Contents API (simplest for a first commit)
# — base64-encode each file, PUT to /repos/<owner>/<repo>/contents/<path-in-repo>
# — if a file already exists, GET it first for its sha and include that sha in the PUT
# — GitHub's Contents API caps each file around 1MB; anything bigger needs the Git Data API
#   (blobs/trees) or Git LFS — see "Large files" below
```
For many files, prefer the Git Data API (blobs → tree → commit → update ref) over repeated
Contents API calls — one atomic commit instead of N. Either way: after pushing, `GET
.../contents/` once to confirm the files actually landed before reporting success — never trust
HTTP status codes alone.

**Repeat runs (the repo already exists from a previous push):** don't blind-overwrite everything.
For each local file, compare its content against the existing file at that path (fetch it and
diff, or compare a hash) and only PUT the ones that actually changed or are new — report which
files were skipped as unchanged. This avoids noisy commit history and needless API calls.

**If the push is interrupted partway** (rate limit, network drop): keep a running list of which
files succeeded as you go. On retry, skip the ones already confirmed pushed and resume from where
it stopped — don't restart the whole repo from zero.

**Topics:** if the repo already has topics set, `GET .../topics` first and merge the new ones in
rather than overwriting the existing set.

**Before pushing anything, show a quick preview** — the file tree and the README's first few
lines — and get a go-ahead, especially the first time this runs in a conversation. A repo going
live is not reversible in the casual sense (other people can fork/star it the moment it's public),
so a person should see what's about to happen before it happens.

**Health check pass before pushing:** flag anything that looks like an accidental huge binary,
build artifact, or dependency folder (`node_modules/`, `.venv/`, compiled binaries) that isn't
already excluded by `.gitignore` — these bloat repos and are rarely meant to be committed.

**2. Real GitHub MCP connector**, only if one is found (`search_mcp_registry` with "github") and
the user hasn't already hit a permission wall with it this conversation. In practice, standard
connector installs commonly lack "Administration: write" (can't create new repos) and sometimes
can't see a repo created outside the app at all (404, not 403, in that case) — this usually isn't
fixable from the user's account settings. If it fails with a permission error even once, say so
plainly and move to option 1 rather than repeating the same failed call.

**3. Generated script**, only if sandbox network access isn't available, or the user explicitly
wants to run things locally themselves. A PowerShell/bash script that:
1. Locates the generated zip in Downloads/Desktop/cwd
2. Extracts it
3. Runs the mandatory safety gate below before touching git
4. `git init`/`add`/`commit`
5. Prompts for username + PAT (hidden input, never logged)
6. Creates the repo via API (with description), pushes, self-heals once on a non-fast-forward
   push with `--allow-unrelated-histories` before surfacing a real error

Important PowerShell gotcha: git writes normal status lines to stderr. With
`$ErrorActionPreference = "Stop"`, PowerShell can misread a successful push as a fatal error. Set
`$ErrorActionPreference = "Continue"` around native `git` calls and check `$LASTEXITCODE`
explicitly instead of trusting PowerShell's own error signal.

**4. Plain manual commands**, only if the user asks for step-by-step manual control:

```bash
# Windows PowerShell — unzip does not exist here:
Expand-Archive -Path .\<project-name>.zip -DestinationPath .
cd .\<project-name>

# macOS/Linux:
unzip <project-name>.zip
cd <project-name>
```

**Mandatory safety gate before any `git init`:**
```bash
pwd   # must print the PROJECT folder path, not a home/user/Desktop root
ls    # must show ONLY the files just generated — nothing else
```
Only proceed to `git init` once both checks visibly pass. If `git init` already ran in the wrong
place (unexpected folders like `.ssh/`, `AppData/`, `Desktop/` show up under `git status`): stop,
don't `add`/`commit`, and remove just the tracking data with `rm -rf .git` (or `Remove-Item
-Recurse -Force .git` on Windows) — this only deletes git metadata, not real files.

```bash
git init
git add .
git status          # read it — only this project's files, no secrets
git commit -m "Initial commit: <project-name>"
# Create the repo on github.com first (don't auto-init with README/license, you already have them)
git branch -M main
git remote add origin https://github.com/<username>/<project-name>.git
git push -u origin main
```
Password prompt → use a Personal Access Token, not the account password.

**Common errors and the actual fix:**
- `fatal: remote origin already exists` → `git remote set-url origin <url>` instead of `add`
- `error: failed to push some refs` (repo was initialized with a README) →
  `git pull origin main --allow-unrelated-histories`, resolve conflicts, push again
- `Support for password authentication was removed` → use a Personal Access Token
- File too large (>100MB) → remove from the commit + `.gitignore`, or set up Git LFS — never
  force-push around it
- Secret already committed and pushed → rotate/revoke it immediately (assume compromised the
  moment it's pushed, even briefly), then clean history with `git filter-repo` or BFG — a
  `.gitignore` entry alone does not remove it from history

### After pushing — final checklist

- Open the repo, confirm the README renders correctly
- Add repo description + topics if not already set via the API push
- Double-check no secrets are visible in any file via the web UI
- Pin the repo on the profile if it's a strong one

Whichever upload path ran, zip the folder and offer it to the user regardless (so they have a
local copy even after an automated push), then end with a short, honest status: what's in the
repo, the live URL, and what's still missing (tests never run, license not chosen, etc.).

## Other hosts (GitLab, Bitbucket)

Despite the skill's name, the same workflow applies if the user wants GitLab or Bitbucket instead
of GitHub — ask which host before assuming GitHub if it's not obvious from context. The create +
push pattern is the same shape, different API:
- **GitLab:** `POST /api/v4/projects` to create (token header `PRIVATE-TOKEN`), then the Commits
  API or Git Data equivalent to push files.
- **Bitbucket:** `POST /2.0/repositories/{workspace}/{repo_slug}` to create, then its source/commit
  API to push files.
Same rules apply regardless of host: ask public/private, never fabricate, scan for secrets,
preview before pushing, confirm after.

## Optional CI workflow

Only offer this — never add it unasked. If the project has something genuinely checkable (tests
that exist and pass, a lint config, a build step that was actually run in this conversation), ask
if a one-file `.github/workflows/ci.yml` running that check on push would be wanted. If yes,
generate the minimal workflow for exactly that check — nothing padded in "for completeness." If
there's nothing real to check, don't bring this up; an empty or fake CI badge is exactly the kind
of decoration the hard rules forbid.

## Before skipping the media section

Don't just drop the "Demo"/screenshots section silently because no images were shared in this
conversation — ask once: "Got any screenshots or a demo clip you want in the README?" If the
user has them but didn't think to share them, this is an easy win for the repo that's otherwise
lost. If they say no or don't have any, proceed without the section as normal — one ask, not a
repeated nag.

## Output tone

The generated README and other repo files are written in normal, professional English — formal
project documentation regardless of the conversation's chat style. Chat-style preferences (like
a shorthand mode) govern replies in this conversation, not the content of generated repo files.
