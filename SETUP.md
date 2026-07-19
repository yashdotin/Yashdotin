# Setup — read this part first

**The images and snake weren't broken by a bad service — the files were just never pushed.** I checked your live repo directly: `assets/banner.png`, `assets/heart-risk.png`, `assets/megapixel.png`, and `.github/workflows/snake.yml` all return 404. Only `README.md` made it into the repo last time. This is a "files didn't get uploaded" problem, not a "GitHub broke" problem.

## How to upload this correctly

GitHub's web upload button does **not** preserve folder structure if you just drag files in from the repo's root "Add file → Upload files" screen — everything lands flat in the root instead of inside `assets/` or `.github/workflows/`. To do it right on mobile/web:

1. Go to your repo → click **Add file → Create new file**
2. In the filename box, type the **full path including folders**: `assets/banner.png` — GitHub auto-creates the folder when you type a `/` in the name
3. Since `Create new file` is for text, use **Add file → Upload files** instead, but first navigate *into* the `assets` folder (create it first via step 2 with a placeholder, then upload into it) — or just drag the whole `assets` folder from your file manager onto the upload zone; GitHub does preserve structure when you drop an entire folder, just not loose files.
4. Repeat for `.github/workflows/snake.yml` — you'll need to create `.github`, then `workflows` inside it, then upload/create `snake.yml` there.

Easiest path if you're on desktop: clone the repo locally (or use GitHub Desktop), drop these files into the right folders, then commit + push. Folder structure is guaranteed correct that way.

## Folder structure (what it should look like when done)

```
Yashdotin/
├── README.md
├── .github/
│   └── workflows/
│       └── snake.yml
└── assets/
    ├── banner.png
    ├── heart-risk.png
    └── megapixel.png
```

## Replacing the placeholder images

The 3 PNGs in `assets/` are placeholders with text on them. Swap for your own — same filenames:

- **banner.png** — header image, ~1200×300. Match your yashs.online look (JetBrains Mono, amber/teal, terminal feel) so the profile and portfolio feel like one brand.
- **heart-risk.png** — screenshot of the Streamlit app.
- **megapixel.png** — screenshot of MegaPixel Creations.

## Activating the snake

Once `.github/workflows/snake.yml` actually exists in the repo (see above), it runs automatically on the next push to `main`. Check the **Actions** tab to watch it complete — first run can take a couple minutes. It pushes to an `output` branch, which the README already points to, so nothing else needs to change once the file is really there.

