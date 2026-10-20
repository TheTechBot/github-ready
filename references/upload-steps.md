# Upload instructions — fill in `<project-name>` and give these to Finan verbatim

Always confirm these two things with Finan before sending final steps:
1. Does the GitHub repo already exist (empty, created on github.com), or does it need creating?
2. HTTPS with a personal access token, or SSH key already set up? (Changes the push commands.)
3. **What shell/OS** (PowerShell/Windows, bash/Mac, bash/Linux) — commands below differ,
   especially for extracting a zip. Never hand over `unzip` to a PowerShell user; it doesn't exist
   there and the silent failure is exactly what causes step 0 below to go wrong.

## Step 0 — MANDATORY safety gate before `git init`, every time

`git init` tracks the **current working directory**. If a `cd` silently failed (wrong path, zip
never extracted, typo) and nobody caught it, `git init`/`git add .` runs against whatever folder
the shell happens to be sitting in — which can be the user's entire home folder, Desktop, or C:
drive. This has actually happened. Never skip this gate, never shorten it:

```bash
pwd              # or `cd` with no args on Windows — must print the PROJECT folder path, not a
                  # home/user/Desktop root. The last path segment must be the project name.
ls                # or `dir` on Windows — must show ONLY the files you just generated
                  # (README.md, LICENSE, .gitignore, source files...), nothing else: no
                  # AppData, .ssh, Desktop, Documents, etc.
```

Only after both checks visibly pass does `git init` happen. If `pwd`/`ls` don't match, STOP and
fix the `cd` first — don't run `git init`, `git add`, or `git commit` "to see what happens."

If `git init` was already run in the wrong place (this will show as unexpected folders like
`.ssh/`, `AppData/`, `Desktop/` appearing under `git status`'s untracked files): stop immediately,
do not `git add` or `git commit`, and remove just the accidental tracking data:
```bash
# in the WRONG folder where git init was mistakenly run:
rm -rf .git            # macOS/Linux
Remove-Item -Recurse -Force .git   # Windows PowerShell
```
This only deletes git's tracking metadata, not any real files. Then go back to Step 0.

## Extracting the generated zip, by shell

**Windows PowerShell** (`unzip` does not exist here — never suggest it):
```powershell
Expand-Archive -Path .\<project-name>.zip -DestinationPath .
cd .\<project-name>
```

**macOS / Linux (bash/zsh):**
```bash
unzip <project-name>.zip
cd <project-name>
```

Then always run Step 0's `pwd`/`ls` check before continuing, on either OS.

## A. Brand-new repo, HTTPS + personal access token (most common, no SSH setup needed)

```bash
# Confirm no secrets are present (re-run the secrets audit, don't skip)
git init
git add .
git status          # <-- READ this output. Confirm it lists ONLY this project's files, and
                     #     no .env, no real credentials.

# 3. First commit
git commit -m "Initial commit: <project-name>"

# 4. Create the repo ON GITHUB.COM FIRST (do this in the browser):
#    github.com -> New repository -> name it "<project-name>" -> DO NOT
#    initialize with README/.gitignore/license (we already have them) -> Create repository

# 5. Connect and push (GitHub will show you this exact URL after creating the repo —
#    use the URL it gives you, this is just the pattern)
git branch -M main
git remote add origin https://github.com/<your-username>/<project-name>.git
git push -u origin main
```

If prompted for a password at `git push`: GitHub no longer accepts your account password here.
Use a Personal Access Token instead:
`github.com -> Settings -> Developer settings -> Personal access tokens -> Fine-grained tokens ->
Generate new token` (scope: Contents: Read and write, on this repo only). Paste the token as the
password when prompted.

## B. SSH instead of HTTPS (if SSH key is already set up)

Same as above, but step 5 remote line becomes:
```bash
git remote add origin git@github.com:<your-username>/<project-name>.git
git push -u origin main
```
If unsure whether SSH is set up: `ssh -T git@github.com` should return a success message with
your username, not an error.

## C. Repo already exists on GitHub (created empty, nothing pushed yet)
Same as A, steps 1-3, then:
```bash
git branch -M main
git remote add origin <the HTTPS or SSH URL GitHub gave you>
git push -u origin main
```

## D. Updating an existing repo that already has history
```bash
cd path/to/<project-name>
git add .
git status      # confirm what's actually changing, no secrets sneaking in
git commit -m "<clear, specific message — not 'update'>"
git push
```

## Common errors and the actual fix (not generic advice)
- `fatal: remote origin already exists` → `git remote set-url origin <url>` instead of `add`
- `error: failed to push some refs` (repo initialized with a README on GitHub) →
  `git pull origin main --allow-unrelated-histories`, resolve any conflict, then push again
- `Support for password authentication was removed` → use a Personal Access Token as above
- File too large (>100MB) → do NOT force-push around it. Either remove it from the commit
  (`git rm --cached <file>`, add to `.gitignore`) or set up Git LFS if the file genuinely needs
  to be tracked (`git lfs track "<pattern>"`, commit `.gitattributes`, then add/commit the file).
- Accidentally committed a secret and already pushed → rotate/revoke the secret immediately first
  (assume it's compromised the moment it's pushed, even briefly), then clean history with
  `git filter-repo` or the BFG Repo-Cleaner — a `.gitignore` entry alone does not remove it from
  history.

## E. Fully automated path (preferred over manual steps A-D when possible)

No GitHub MCP connector is available in this environment's registry as of this writing —
re-check with `search_mcp_registry` each time in case that's changed, and if a real one shows up,
offer it instead of this script. Until then, the most reliable path is a single generated
PowerShell/bash script that talks directly to the GitHub REST API with Finan's own Personal
Access Token — no connector needed, no manual command-by-command copy-paste to get wrong.

Generate a script (see `assets/setup-github.ps1` pattern) that, in order:
1. Locates the zip in Downloads/Desktop/cwd — don't assume the user is already in the right folder
2. Extracts it
3. **Verifies** the extracted folder contains only the expected project files (same safety gate
   as Step 0 above) before running any git command — abort loudly if anything unexpected is there
4. `git init`/`add`/`commit`
5. Prompts for GitHub username + a **classic** PAT with `repo` scope (fine-grained tokens can't
   create new repos via the API) — reads it hidden, never logs or echoes it, clears it from
   memory when done
6. Creates the repo via `POST https://api.github.com/user/repos` with the real project
   description already set — no manual "add description" step needed
7. Pushes, and if it fails on a non-empty remote, self-heals with
   `git pull --allow-unrelated-histories` then retries once before surfacing a real error

Every step either succeeds visibly or stops with a clear message — never silently continues past
a failed check (this is what caused the `C:\Users\HP` and "file not found" incidents).

Give Finan one line to run it (`powershell -ExecutionPolicy Bypass -File .\setup-github.ps1`),
not a block of commands to type one at a time.

## F. In-sandbox API push (default — Claude does this itself, no script handed to Finan)

This is the preferred path per `SKILL.md` Step 5. Claude runs this from its own bash tool —
Finan never sees a script or a terminal. The PAT is supplied once in chat (classic token, `repo`
scope — fine-grained tokens can't create new repos) and is used only for these calls within the
same turn; never write it to any file in the container or hand it back.

```bash
GH_USER="<finan's github username>"
GH_TOKEN="<the PAT, from chat input only — never echoed, never written to a file>"
REPO="<project-name>"
DESC="<the real one-line project description>"

# 1. Create the repo (sets description in the same call)
curl -s -X POST https://api.github.com/user/repos \
  -H "Authorization: Bearer $GH_TOKEN" -H "Accept: application/vnd.github+json" \
  -d "{\"name\":\"$REPO\",\"description\":\"$DESC\",\"private\":false}"
# Check the response: a "name already exists" error is fine (continue pushing into it);
# any other error (401/403) means stop and tell Finan exactly what the API said.

# 2. Push each file via the Contents API — simplest reliable method for a first commit.
# Repeat this per file (README.md, LICENSE, .gitignore, SKILL.md, references/*.md, source files...):
B64=$(base64 -w0 <path-to-file>)
curl -s -X PUT "https://api.github.com/repos/$GH_USER/$REPO/contents/<path-in-repo>" \
  -H "Authorization: Bearer $GH_TOKEN" -H "Accept: application/vnd.github+json" \
  -d "{\"message\":\"Initial commit: $REPO\",\"content\":\"$B64\",\"branch\":\"main\"}"
# Check each response for a "content" key (success) vs an "message" error key — don't assume
# success silently. If a file already exists (repo wasn't empty), the API returns 422 asking
# for a sha — fetch it with GET .../contents/<path> first, then retry the PUT with that sha.
```

For more than a handful of files, prefer the Git Data API (create a tree + one commit) over many
individual Contents API calls — fewer requests, one atomic commit instead of N:
1. `POST .../git/blobs` for each file's content → get each blob `sha`
2. `POST .../git/trees` with all the blob shas + paths → get a tree `sha`
3. `POST .../git/commits` with that tree sha (and parent commit sha if the repo isn't empty)
4. `PATCH .../git/refs/heads/main` to point `main` at the new commit sha

Either method: after pushing, `GET .../contents/` once to confirm the expected files are actually
there before telling Finan it succeeded — don't report success from HTTP status codes alone.

## After pushing — final checklist to give Finan
- [ ] Open the repo on github.com, confirm README renders correctly
- [ ] Add repo description + topics (Settings gear next to "About")
- [ ] Add a social preview image if one exists (Settings -> General -> Social preview)
- [ ] Double-check no secrets visible in any file via the GitHub web UI
- [ ] Pin the repo on the profile if it's one of the strongest projects
