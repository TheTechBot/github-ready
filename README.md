# github-ready

> Claude Skill that turns whatever you just built in a conversation into a complete,
> ready-to-upload GitHub repository — honest README, proportional file structure, secrets audit,
> and exact git/GitHub upload commands. No fake test results, no decorative badges, no invented
> claims.

**Status:** Working, lightly tested. Structure and reference docs validated with Anthropic's
`skill-creator` validator. Not yet run end-to-end against a real multi-part project by anyone but
its author — feedback and bug reports welcome.

## What it does

When triggered (say "make this github ready", "package this for github", or `/github-ready`),
Claude will:
1. Reconstruct the project from the conversation (code, what was tested, known bugs)
2. Pick a repo structure proportional to the project's actual size (a hackathon script doesn't
   get the same tree as a full hardware+firmware+software robot project)
3. Generate README, LICENSE, `.gitignore`, and any other files that genuinely apply
4. Audit every generated file for secrets (WiFi credentials, API keys, tokens) before handoff
5. Hand over exact, filled-in git/GitHub upload commands — not generic placeholders

Full behavior rules live in [`SKILL.md`](SKILL.md); the actual templates and checklists it
pulls from are in [`references/`](references/).

## Install

### Option A — Claude.ai / Claude apps with skill install support
Download the packaged `.skill` file from this repo's [Releases](../../releases) (or build one
yourself, see below) and use the **Save skill** button when it's presented in a Claude
conversation.

### Option B — Claude Code / manual
Copy this folder into your skills directory, e.g.:
```bash
cp -r github-ready ~/.claude/skills/github-ready
```
(Path depends on your Claude Code setup — check your current skills directory.)

### Building a `.skill` package yourself
This repo ships as a plain skill folder (`SKILL.md` + `references/`). To package it:
```bash
zip -r github-ready.skill SKILL.md references/
```
Anthropic's own `skill-creator` toolkit includes a `package_skill.py` script that does this with
extra validation — in this author's environment it failed with a read-only filesystem error when
run from its own install directory. Workaround: run it with the working directory set somewhere
writable, or just use the manual `zip` command above, which produces an equivalent package.

## Known limitations (honest, not modest)
- Only exercised once, on one project, by its own author. Edge cases in very large or unusual
  project types haven't been stress-tested.
- The license guidance in `references/licenses.md` is a starting point, not legal advice.
- The git troubleshooting list in `references/upload-steps.md` covers common errors, not every
  possible git failure mode.
- Has no automated test suite of its own (would need skill-level eval prompts, not code tests —
  see Anthropic's `skill-creator` skill if you want to build those).

## Contributing
Open an issue or PR if you hit a case this skill handles badly — that's the point of putting it
up publicly. No formal contribution process yet; keep PRs small and say what project type you
tested it on.

## License
MIT — see [LICENSE](LICENSE). Use it, modify it, redistribute it.
