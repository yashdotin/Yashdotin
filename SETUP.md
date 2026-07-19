# Setup

1. Push both `README.md` and the `assets/` folder to your `Yashdotin/Yashdotin` repo, keeping the same folder structure:

```
Yashdotin/
├── README.md
└── assets/
    └── banner.svg
```

2. That's it — GitHub renders `README.md` on your profile automatically since the repo name matches your username.

## Why this version is more reliable

- The old version used ~25 separate shields.io badge calls for skill icons. GitHub's image cache (camo) grabbed one failed/rate-limited request and served broken icons for all of them afterward. This version uses **skillicons.dev**, which renders a whole row of icons in a single request — far less likely to break, and it looks cleaner too.
- The banner at the top is a local SVG (`assets/banner.svg`) — it lives in your own repo, so it can never go down or get rate-limited like an external badge service can.
- Stats widgets (contribution graph, streak, top languages, trophies) are unchanged — those come from `vercel.app` / `demolab.com` services and were rendering fine in your screenshot.

## Optional: activate the contribution snake

Add this as `.github/workflows/snake.yml` in the same repo:

```yaml
name: generate animated snake
on:
  schedule:
    - cron: "0 */6 * * *"
  workflow_dispatch:
  push:
    branches:
      - main

jobs:
  generate:
    permissions:
      contents: write
    runs-on: ubuntu-latest
    steps:
      - uses: Platane/snk@v3
        id: snake
        with:
          github_user_name: Yashdotin
          outputs: |
            dist/github-contribution-grid-snake.svg
            dist/github-contribution-grid-snake-dark.svg?palette=github-dark

      - uses: crazy-max/ghaction-github-pages@v4
        with:
          target_branch: output
          build_dir: dist
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

Commit that file, and within a few hours the snake animation in your README will start working.
