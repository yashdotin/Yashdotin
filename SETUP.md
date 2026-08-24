# Setup

This goes into a repo named **exactly** `Yashdotin` — `github.com/Yashdotin/Yashdotin`. That
magic repo's README is what shows on your profile page.

---

## 1. Push it

```bash
git init && git branch -M main
git add -A && git commit -m "profile readme"
git remote add origin https://github.com/Yashdotin/Yashdotin.git
git push -u origin main
```

The repo must be **public** — the SVG assets are loaded by URL, so a private repo shows
broken images.

## 2. Let Actions write to the repo

Repo → **Settings** → **Actions** → **General** → **Workflow permissions** →
select **Read and write permissions** → Save.

Without this the Radar and Snake workflows fail on push.

## 3. Add the metrics token

`lowlighter/metrics` needs its own token — the built-in `GITHUB_TOKEN` can't read profile data.
The same token also unlocks the live language radar and the contribution/streak tiles on the
stat card, so it's worth setting even though only one workflow strictly requires it.

1. https://github.com/settings/tokens → **Generate new token (classic)**
2. Scopes: **`read:user`** (add **`repo`** too if you want private repos counted)
3. Expiry: whatever you're happy re-doing later
4. Copy it, then repo → **Settings** → **Secrets and variables** → **Actions** →
   **New repository secret** → name it **`METRICS_TOKEN`**, paste the value

## 4. Kick off the workflows

Repo → **Actions** tab → enable workflows if prompted, then run each one via
**Run workflow**:

| workflow | produces | lands in |
|---|---|---|
| **Metrics** | 3D isometric calendar, achievements badges | `assets/metrics.*.svg` on `main` |
| **Snake** | snake eating your contribution graph | the `output` branch |
| **Charts and cards** | skill radar, live language radar, stat card, the 4 project cards | `assets/radar*.svg`, `assets/card-*.svg` on `main` |

First run takes a couple of minutes. After that they're on a schedule (metrics every 6h,
snake every 12h, radar daily).

> The snake images are referenced from the `output` branch via `raw.githubusercontent.com`,
> so they'll 404 until the Snake workflow has run once. That's expected.

> I generated `assets/radar-dark.svg` / `assets/radar-light.svg` (the self-rated skill radar)
> locally already — that one's stdlib-only and needed no token. The **language radar**
> (`radar-langs-*`) and the **four repo cards** need to hit the live GitHub API for your
> account, and my sandbox's IP was already rate-limited when I tried, so those will
> generate on first Actions run instead of showing up pre-committed. Nothing to do — just
> run the "Charts and cards" workflow once after pushing.

---

## The repo cards

`assets/projects.json` lists the four repos featured under "selected work": `kriya-ai`,
`aptivio`, `paint-the-unseen`, `neural-nitpicker`. If any of those don't match your actual
repo names exactly, the workflow just skips that one silently (check the Action's log for
a `!! <repo> not found on the account` line) — fix the name in `projects.json` and it'll
pick it up on the next run. Swap in different repos any time by editing that file; stars,
forks, and language are always pulled live.

## The skill radar

Edit `assets/skills.json` and either re-run `python scripts/radar.py --data assets/skills.json
-o assets/radar` locally, or just push — the "Charts and cards" workflow redraws it
automatically whenever `assets/skills.json` changes. Values are 0-100 and self-rated; five
to eight axes reads best.

## The stat and repo cards, in general

`scripts/cards.py` generates these into your own repo on purpose, instead of pointing at
`github-readme-stats` / `github-profile-trophy` — those are shared public instances that
periodically go down or hit quota, and when they do, a chunk of your profile shows broken
images. A file committed to your repo doesn't have that failure mode.

---

## If something looks broken

**Images don't load on the profile.** The repo has to be public, and the paths in the
README are relative (`assets/…`) — those only resolve once the files are actually pushed.

**Metrics workflow fails.** Almost always the `METRICS_TOKEN` secret: missing, expired, or
created as a fine-grained token instead of a classic one.

**Snake images 404.** The Snake workflow hasn't completed yet, or step 2 (write permissions)
was skipped so it couldn't create the `output` branch.

**A repo card is missing.** Check the repo name in `assets/projects.json` matches the actual
GitHub repo name — the workflow log will say `not found on the account` if it doesn't.

**Streak stats show an error.** `streak-stats.demolab.com` is still a shared public instance
(kept as-is since it's a small, low-risk piece); it usually recovers on its own during busy
hours.
