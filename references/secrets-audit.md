# Secrets audit — run before packaging, every time

Scan every generated/copied file for:
- Wi-Fi SSID/password strings hardcoded in firmware (`ssid = "..."`, `password = "..."`)
- API keys / tokens (OpenAI, Claude, Blynk auth tokens, etc.)
- Hardcoded IP addresses or private server URLs that reveal personal infrastructure
- `.env` files with real values
- SSH keys, certs, `.pem` files
- Personal data — home address, family photos with identifying info, location logs

## What to do when found
1. Flag it to Finan explicitly in the handoff message — name the file and line, don't fix it
   silently and stay quiet.
2. Replace with a placeholder + add/confirm `.gitignore` excludes the real file:
   - `ssid = "YOUR_WIFI_SSID"` / `password = "YOUR_WIFI_PASSWORD"`
   - Create `.env.example` with dummy values if `.env` is used
3. If a secret was already used in a **previous** real upload (not just this packaging), warn
   that adding it to `.gitignore` now does NOT remove it from git history — it needs rotation and
   history cleanup (`git filter-repo` or BFG), not just a `.gitignore` edit.

## Quick grep pass (if a shell/terminal is available to run it on the generated folder)
```bash
grep -rniE "ssid|password|api[_-]?key|secret|token|-----BEGIN" ./project-name --include=*.{ino,py,js,json,yaml,yml,env}
```
Review every hit manually — don't auto-strip without confirming it's actually a secret vs. a
variable name.
