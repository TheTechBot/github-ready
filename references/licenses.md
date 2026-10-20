# Picking a license — ask, don't default silently

| License | Use when |
|---|---|
| MIT | Default permissive choice for most code projects — simple, widely understood, minimal restrictions |
| Apache-2.0 | Like MIT but with explicit patent protection — worth it if the project could be commercially sensitive |
| GPL-3.0 | Want derivatives to stay open-source too (copyleft) — less common for hobby/portfolio projects |
| CERN-OHL-S / CERN-OHL-W | Hardware design files (PCB, schematics) specifically — not a software license |
| No license | Finan keeps all rights, nobody else may legally reuse/modify/redistribute — valid if the repo is purely a portfolio showcase, not meant to be reused |

If the project has both code and hardware design files, it's fine to license them separately —
e.g. MIT for firmware/software, CERN-OHL-W for schematics/PCB — just say so clearly in the README
under "License."

Default suggestion for Finan's robotics/ESP32/portfolio projects: **MIT for code**, and ask
specifically if hardware design files (schematics, PCB, mechanical CAD) are included before
picking a hardware license — don't silently apply MIT to hardware files.
