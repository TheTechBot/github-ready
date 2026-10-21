# github-ready

**A Claude Skill that packages up whatever you just built and puts it on GitHub for you —
properly, honestly, and without you having to touch a terminal.**

## The problem this solves

You just finished a project with Claude. Now you want it on GitHub, but:
- You're not sure what files a "proper" repo is supposed to have
- Writing a good README from scratch is tedious
- Copy-pasting `git` commands is easy to get wrong (and once you mess it up, it's a pain to undo)
- Most GitHub READMEs online oversell the project — fake test results, broken demo links, badges
  that don't mean anything

This skill handles all of it, and refuses to lie about your project while doing it.

## What it actually does

Say something like **"make this github ready"** in a Claude conversation after building
something, and it will:

1. **Look at what you actually built** in that conversation — not guess, not assume
2. **Pick the right set of files** for the size of your project (a two-file script doesn't get
   the same treatment as a full robot with hardware, firmware, and software)
3. **Write a real README**, a license, a `.gitignore`, and anything else that genuinely applies
4. **Scan for anything sensitive** — WiFi passwords, API keys, tokens — before anything goes
   online
5. **Put it on GitHub for you.** If Claude's environment allows it, this happens automatically —
   you just give it a GitHub access token when asked, and it creates the repo and pushes
   everything itself. No commands to copy, no terminal.

## How to use it

1. **Install the skill.** Download `SKILL.md` from this repo (that's the only file you need) and
   add it to Claude the way your Claude setup supports adding skills — for claude.ai/the Claude
   apps, this is usually a "Save skill" button that appears when the skill is shared with you; in
   Claude Code or similar tools, it's a skills folder you drop the file into.
2. **Build your project** in a conversation with Claude, like normal.
3. **When you're done, say:** "make this github ready" (or similar — it also responds to
   `/github-ready` if you're using slash commands).
4. **Follow along.** Claude will tell you exactly what it's doing and ask if anything important
   is missing (like which license you want). If it can push directly, it'll ask for a GitHub
   access token at that point — that's normal, and it's the only thing you need to provide.

## What you'll be asked for

- **A license choice**, if you haven't picked one — this isn't filled in automatically, because
  it's your decision to make
- **A GitHub Personal Access Token**, only if the automatic push is being used — this lets Claude
  create the repo and upload the files on your behalf for that one request. It's not saved
  anywhere afterward.

## What this skill will never do

- Invent test results, fake "100% working" claims, or decorative badges that don't mean anything
- Upload a password, API key, or other secret it finds — it flags those instead and tells you
- Pad your repo with empty folders or files just to look more impressive
- Run a command without checking first that it's about to touch the right folder

If something about your project wasn't actually tested or finished, the generated README will
say so plainly instead of pretending otherwise. That's on purpose — a smaller, honest repo is
worth more than an impressive-looking fake one.

## Known limitations

- This is a new skill. It's been used successfully a handful of times, not hundreds — if you hit
  something it handles badly, that's useful to know about.
- The automatic GitHub push needs Claude's environment to have outbound network access to
  GitHub's API. If that's not available where you're using Claude, it falls back to giving you
  a script or step-by-step commands instead — still safe, just not hands-off.
- It's not a substitute for actually testing your project. It documents what you tell it, it
  doesn't verify your code works.

## License

MIT — see [LICENSE](LICENSE). Use it, modify it, share it.
