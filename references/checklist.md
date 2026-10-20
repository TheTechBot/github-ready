# File checklist — only add what the project's current stage actually needs

## Always (every project, even a hackathon script)
- `README.md`
- `.gitignore` (language/platform-appropriate — e.g. `.pio/`, `*.bin`, `node_modules/`, `__pycache__/`)
- `LICENSE` — chosen deliberately, see `licenses.md`. "No license" is a valid choice if Finan
  wants to keep all rights — but it must be a conscious choice, state it either way.

## Add once the project has real substance
- `docs/` — wiring guides, architecture, troubleshooting (only with real content)
- `media/` — genuine photos/screenshots/demo clips, never stock images
- Dependency file — `requirements.txt`, `platformio.ini`, `package.json`, `libraries.md`
- `BOM.csv` — for any hardware project, real component list with quantities

## Add once there's a second meaningful version
- `CHANGELOG.md`

## Add for anything networked (WiFi, API keys, cloud calls) — don't skip this one
- `SECURITY.md` — how to report a vuln. Small but matters a lot for ESP32/IoT work.
- Confirm `.env.example` exists with dummy values if `.env` is used anywhere (see
  `secrets-audit.md`)

## Add only if outside contributors are actually wanted
- `CONTRIBUTING.md`
- `CODE_OF_CONDUCT.md`

## Add for competition/research submissions (WRO, etc.)
- `CITATION.cff`
- Keep docs/results section especially honest — judges check reproducibility

## GitHub settings, not files (mention these as a step, not a file to generate)
- Repository description + topics (`robotics`, `esp32`, `embedded-systems`, etc.)
- Social preview image
- Enable Issues if bug tracking is wanted
- Branch protection / secret scanning (good default for anything with hardware or API usage)
- Pin the repo + add it to the GitHub profile README once it's solid

## Never generate "to look impressive"
- Empty `tests/` folders with no tests
- `.github/workflows/` with no real CI check behind them
- Decorative badge rows
- Any file whose only purpose is padding the checklist
