# Folder structures — pick the smallest honest fit

## A. Quick hackathon / single-script project
```
project-name/
├── README.md
├── LICENSE
├── .gitignore
└── src/            (or just the script files at root if tiny)
```
Nothing else, unless the project genuinely grows.

## B. Pure software (web app, Python tool, AI project)
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

## C. Pure hardware/firmware (ESP32, Arduino, sensor project)
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

## D. Mixed hardware + firmware + software (robot, SpeleoBot-style, dashboard + ESP32)
```
project-name/
├── README.md
├── LICENSE
├── .gitignore
├── CHANGELOG.md        (only once there's more than one meaningful version)
├── BOM.csv
├── hardware/
│   ├── wiring/
│   └── schematics/
├── firmware/
│   ├── src/
│   └── README.md
├── software/
│   ├── src/
│   ├── tests/
│   └── requirements.txt
├── docs/
│   ├── architecture.md
│   ├── sensor-calibration.md   (only if calibration was actually done)
│   └── troubleshooting.md
├── media/
│   ├── photos/
│   └── demos/
└── tests/
```

## When to add more (only when it's real, not decorative)
- `CONTRIBUTING.md` / `CODE_OF_CONDUCT.md` — only if Finan actually wants outside contributors
- `SECURITY.md` — add for anything networked (ESP32 + WiFi, web app with API keys) regardless of
  size — this one is worth the extra file even for small projects
- `.github/workflows/` — only if there's an actual CI check worth automating (e.g. a lint/build
  check), not as a checkbox
- `CITATION.cff` — only for competition/research projects (WRO submissions qualify)
- Repo topics/description/social preview — mention as a GitHub Settings step, not a file
