# Setup

## Folder structure

```
Yashdotin/                              (your profile repo)
├── README.md
├── .github/
│   └── workflows/
│       └── snake.yml                   ← generates the animated contribution snake
└── assets/
    ├── banner.png                      ← REPLACE: your real header banner (1200×300 recommended)
    ├── heart-risk.png                  ← REPLACE: screenshot of the Heart Risk Streamlit app
    └── megapixel.png                   ← REPLACE: screenshot of MegaPixel Creations
```

Push all of this to `Yashdotin/Yashdotin`, keeping the exact same paths. That's the whole setup — no config needed beyond replacing the 3 placeholder images.

## Replacing the placeholder images

The 3 PNGs in `assets/` right now are just placeholders with text on them so the README doesn't 404 before you add real ones. Swap them out for your own — same filenames, any reasonable size:

- **banner.png** — a header image. Since your portfolio (yashs.online) already has a terminal/code-editor look with JetBrains Mono + amber/teal accents, a screenshot or graphic in that same style will make the profile feel like one connected brand instead of a separate thing.
- **heart-risk.png** — a screenshot of the Streamlit app in action (the prediction form, or a result screen).
- **megapixel.png** — a screenshot of the MegaPixel Creations site (homepage or gallery view).

Recommended: crop screenshots to roughly a 3:2 ratio so they sit cleanly next to the text in the table.

## Why this version has no image-loading glitches

Every image that broke before was a **third-party service being hit with too many parallel requests or getting rate-limited** — not something in your control. This version only keeps external calls to services that:

1. Rendered correctly in your own screenshots already (`github-readme-stats`, `streak-stats`, `activity-graph`, `komarev` view counter), and
2. Are single-request-per-widget, not dozens of small badge calls.

Everything else — banner, project screenshots — is now a **local image in your own repo**, so nothing can rate-limit or cache-fail it. The only way it breaks is if a filename doesn't match, which is why the structure above matters.

## Activating the snake

`.github/workflows/snake.yml` is already included and configured for your username. Once you push it:

- It runs automatically on every push to `main`, and every 6 hours after that on a schedule
- First run can take a few minutes — check the **Actions** tab in your repo to watch it complete
- Once done, it pushes the generated SVG to an `output` branch, which the README already points to

No manual setup beyond pushing the file.
