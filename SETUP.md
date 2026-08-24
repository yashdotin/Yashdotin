# Setup

## What changed in this drop

- **banner.png / banner.svg are gone** — the README doesn't reference them anymore, delete them from the repo if they're still sitting there from the last push.
- **Self-hosted stats/cards addon added**, same idea as the old `github-readme-stats` / `streak-stats` embeds but rendered by scripts that live in this repo instead of a shared public service. When those public instances go down or get rate-limited, every profile using them shows broken images at once — a file committed to your own repo doesn't have that failure mode.
- **Dot-matrix portrait added** at the top of the README (`assets/portrait.svg`), same treatment as Gargi's profile. Generate it from your own photo with `scripts/dotify.py` — see below.
- New folders: `scripts/` (`cards.py`, `radar.py`, `dotify.py`) and two new workflows in `.github/workflows/`: `metrics.yml` and `cards.yml`. `Snake.yml` is untouched.

## Generating your portrait

Needs Pillow: `pip install Pillow`. Pick a photo with a clean-ish background —
square-ish framing works best.

```bash
python scripts/dotify.py your-photo.jpg -o assets/portrait --cols 100 --equalize --detail 0.5 --color
```

- `--color` keeps real photo colours and writes a single `assets/portrait.svg`
  (no dark/light split needed — the README already points at just that one file).
- `--equalize` matters most for a face: a lit face against dark hair has a wider
  tonal range than a dot ramp can show without it, so skip it and the render
  blows out.
- `--cols` is the size/detail dial — 100 is a solid default, 130 is sharper but
  bigger.
- Add `--circle` for a tight head-shot crop, `--square --focus 0.5,0.4` to
  control the crop centre, or `--reveal` for a row-by-row load-in animation.
- Full option list: `python scripts/dotify.py --help`, or see the comments Gargi
  left in her own SETUP.md if you want the exact flag-by-flag reasoning.

Commit `assets/portrait.svg` once you're happy with it — it's a static file,
nothing regenerates it automatically.

## Folder structure (what it should look like when done)

```
Yashdotin/
├── README.md
├── SETUP.md
├── .github/
│   └── workflows/
│       ├── Snake.yml
│       ├── metrics.yml
│       └── cards.yml
├── scripts/
│   ├── cards.py
│   └── radar.py
└── assets/
    ├── skills.json
    ├── projects.json
    ├── radar-dark.svg
    └── radar-light.svg
```

Upload the whole folder, not loose files — GitHub's drag-and-drop only preserves
subfolders if you drop the folder itself onto the upload zone, not individual files
from inside it. Easiest and safest path is still a local clone:

```bash
git clone https://github.com/Yashdotin/Yashdotin.git
# drop these files in, replacing what's there
git add -A && git commit -m "self-hosted stats/cards addon, drop banner"
git push
```

## 1. Check `assets/projects.json`

The repo names in there (`Kriya-AI`, `Aptivio`, `Neural-Nitpicker`, `Wanderlust`) are my
best guess at your actual GitHub repo slugs — fix any that don't match exactly (case
included). If a slug is wrong, the `Charts and cards` workflow just skips that one and
logs `!! <name> not found on the account, skipped` — nothing breaks, that card's `<img>`
in the README just stays broken until the name matches.

## 2. Let Actions write to the repo

Repo → **Settings** → **Actions** → **General** → **Workflow permissions** →
select **Read and write permissions** → Save.

Without this, `metrics.yml` and `cards.yml` fail on push (same requirement the Snake
workflow already had).

## 3. Add the metrics token

`lowlighter/metrics` (used by `metrics.yml`) needs its own token — the built-in
`GITHUB_TOKEN` can't read profile-level contribution data.

1. https://github.com/settings/tokens → **Generate new token (classic)**
2. Scopes: **`read:user`** (add **`repo`** too if you want private repos counted)
3. Copy it, then repo → **Settings** → **Secrets and variables** → **Actions** →
   **New repository secret** → name it **`METRICS_TOKEN`**, paste the value

`cards.py` also uses `METRICS_TOKEN` if it's set (falls back to the built-in token,
which still works — you just lose the contribution/streak tiles on the stat card).

## 4. Run the workflows once

Repo → **Actions** tab → run each of these manually via **Run workflow**:

| workflow | produces | lands in |
|---|---|---|
| **Metrics** | isometric calendar, languages, achievements | `assets/metrics.*.svg` on `main` |
| **Charts and cards** | both radars, stat card, repo cards | `assets/radar*.svg`, `assets/card-*.svg` on `main` |
| **generate snake** (Snake.yml) | snake eating your contribution graph | the `output` branch |

First run of each takes a couple of minutes. After that they're on a schedule
(metrics every 6h, cards daily at 3:30, snake every 6h).

> Until "Charts and cards" has run once, `assets/card-stats-*.svg` and the four
> `assets/card-<repo>-*.svg` files won't exist yet, so those images 404 on the
> profile. That's expected — same as the snake note in the old SETUP.md.

## Tuning it later

- **Skills**: edit `assets/skills.json`, values are 0–100 and self-rated. 5–8 axes
  reads best.
- **Which repos get a card**: edit `assets/projects.json`. Stars/forks/language are
  pulled live from the API every run; the `description` there overrides whatever's
  set on GitHub — worth setting a real description on the repo too since it also
  shows up in your repo list.
- **Language radar**: has no file to edit, it's built from real byte counts across
  your public repos. `--exclude` and `--curve` are set in `cards.yml` if you want to
  tune which languages get counted or how compressed a dominant language is.
- **Colors**: both scripts key off a `THEMES` dict at the top of `scripts/cards.py`
  and `scripts/radar.py` — already set to your amber (`#D4A017`) / teal (`#4FD1C5`) /
  near-black (`#0A0A0A`) palette to match yashs.online.

## If something looks broken

**Images don't load on the profile.** Repo has to be public, and paths in the README
are relative (`assets/…`) — they only resolve once the files are actually pushed to
`main`.

**Metrics workflow fails.** Almost always `METRICS_TOKEN`: missing, expired, or
created as a fine-grained token instead of a classic one.

**Cards workflow runs but repo cards don't show up.** Check the run log for
`!! <repo> not found on the account, skipped` — fix the slug in `projects.json`.

**Snake image 404s.** The Snake workflow hasn't completed a run yet, or step 2 above
(write permissions) got skipped so it couldn't push to the `output` branch.

**Metrics card shows "Failed to retrieve contributions. This is likely a GitHub API
issue."** This is `lowlighter/metrics` itself saying its GraphQL call bounced —
in practice it's almost always the token, not GitHub. Check, in order:

1. **Does `METRICS_TOKEN` exist at all?** Repo → Settings → Secrets and variables →
   Actions. If it's missing, `metrics.yml` falls back to nothing and every plugin
   that needs contribution data fails this way.
2. **Is it a classic token, not fine-grained?** github.com/settings/tokens →
   "Generate new token (classic)". Fine-grained tokens don't expose the
   contribution GraphQL fields `lowlighter/metrics` needs, even with correct repo
   access.
3. **Does it have `read:user` scope?** That's the one that unlocks contribution
   data. Add `repo` too if you want private contributions counted.
4. **Has it expired?** Classic tokens can be set to auto-expire — check the expiry
   date on the token itself, not just that it still shows in your secrets list.
5. Re-run the token through step 4 above (delete the old secret, add a fresh one),
   then re-run the **Metrics** workflow manually from the Actions tab and check the
   run log — it'll name the exact failing plugin if one specific tile is the
   problem rather than the whole card.
