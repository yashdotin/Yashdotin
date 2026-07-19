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

- **Skill icons**: switched to skillicons.dev, which renders each row as one image request instead of ~25 separate shields.io calls. This is what fixed your stack section already.
- **Banner**: now points to the absolute `raw.githubusercontent.com/Yashdotin/Yashdotin/main/assets/banner.svg` URL instead of a relative `./assets/banner.svg` path. Relative paths don't always resolve correctly on GitHub's profile-page rendering — the absolute raw URL always works as long as `assets/banner.svg` exists on your `main` branch.
- **Social links & footer**: dropped shields.io badge images entirely and replaced with plain styled text links. Shields.io broke twice in a row on your end (same badges, same failure) — text links can never "fail to load" since there's no image involved.
- **Removed top-langs and trophy widgets**: these hit GitHub's API more heavily and are the most rate-limit-prone of the stats cards. Not worth keeping if they're going to be broken half the time. The main stats card, streak, and activity graph are the reliable ones and stayed.

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
