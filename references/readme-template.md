# README.md template

Fill every section from what was actually discussed in the conversation. Delete sections that
don't apply rather than leaving them as empty placeholders. Never fill a section with a plausible
guess — leave it out or mark it "not yet tested / not yet done."

```markdown
# Project Name

> One-line, honest description of what it does and why it exists.

<!-- Only include if a real image/gif exists. Do not use a stock/placeholder image. -->
![Project demonstration](media/demos/demo.gif)

**Status:** Prototype / In Development / Working / Archived

## Overview
What problem this solves, how it works, who it's for. 2-4 sentences, no hype words.

## Features
- Only features that actually exist and work right now.
- Mark anything partially working as "(partial)" or "(in progress)".

## Demo
- [Watch the demonstration](LINK) — only if a real link exists
- [Project gallery](media/) — only if real photos exist

## System Architecture
Plain description of how hardware/firmware/software talk to each other.
Include a diagram only if one was actually made.

## Hardware Requirements
| Component | Quantity | Purpose |
|---|---:|---|
| ... | ... | ... |
(Omit this whole section for pure-software projects.)

## Software Requirements
- OS, language, framework versions actually used.

## Installation
Exact, tested steps — the literal commands used in this conversation, not generic ones.

## Configuration
Pins, addresses, env vars, config files — real values/placeholders, no real secrets.

## Usage
The exact steps to run it, matching what was actually run.

## Testing and Results
State plainly what was tested and what wasn't. "Tested on real hardware" vs.
"compiles but not yet flashed" are very different claims — never blur them.

## Limitations
Real known issues/bugs mentioned in the conversation. This section builds more credibility
than any badge — don't skip it or soften it.

## Safety
Only for hardware with real electrical/mechanical/battery risk.

## Roadmap
- [ ] Next real steps discussed, not aspirational filler.

## Repository Structure
Short explanation of the actual folders present (not the full template — only what exists).

## Troubleshooting
Real errors hit and how they were actually fixed, if any came up in the conversation.

## Credits and References
Libraries, datasheets, tutorials, other people's code actually used — attribute properly.

## License
See [LICENSE](LICENSE).
```

## Rules while filling this in
- If "Testing and Results" would otherwise be empty, don't invent content — write one honest
  line: "Not yet tested on hardware" / "Unit tests not yet written."
- Badges: only add ones that reflect something real (build passing, license type). Never
  decorative badges with no backing status.
- Images: never link a placeholder or stock photo. If no real photo/gif exists yet, drop the
  image line entirely and tell Finan to add one before the first real upload.
