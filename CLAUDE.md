# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repository is

This is **only the compiled build** of the Street-X website (a Russian-language FPV drone club site), served by GitHub Pages from the `gh-pages` branch at the custom domain `street-x.ru` (see `CNAME`). There is no `package.json`, no source tree, no build, lint or test commands here. Everything under `static/` is webpack output from a Create React App project named `sitestreetx`, with content-hashed filenames.

- `index.html` is the CRA entry page. It loads `static/js/main.<hash>.js` and `static/css/main.<hash>.css`.
- `asset-manifest.json` maps logical asset names to the hashed files.
- `static/media/` holds the images, videos (`PSUTIOUTDOOR.mp4` for the header background, `IvanFPV.mp4`), the `Klukva_version_01.otf` font and the drone SVG.
- `manifest.json` and `logo*.png` are still the CRA defaults.

## Deployment history and the CNAME gotcha

The commit history alternates between "Updates" commits, which are full rebuilds pushed by a deploy tool (likely the `gh-pages` npm package), and "Create CNAME" commits. Each rebuild deletes `CNAME`, and it is then re-added by hand. A deploy without `CNAME` breaks the `street-x.ru` domain. If you replace the build, keep `CNAME` with the content `street-x.ru`.

## Working with the code

The real source lives elsewhere and is not in this repo. The source maps include `sourcesContent`, so you can read the original source from `static/js/main.<hash>.js.map`, for example:

```bash
python3 -c "import json;m=json.load(open('static/js/main.aceec6a2.js.map'));[print('=== '+s+'\n'+c) for s,c in zip(m['sources'],m['sourcesContent']) if 'node_modules' not in s and 'webpack' not in s]"
```

Original app structure (as recovered from the source map):
- `src/App.js` is a single page. It sets up a MUI `ThemeProvider` with responsive `h3` typography and renders a header with the background video, a Telegram join button and a tab panel. Content (the equipment and room cards, the video cards) comes from inline arrays in `App.js`. Styles are in `App.module.sass`.
- `src/Components/` holds the `ComponentAppBar`, `SwipeableTextMobileStepper` (photo carousel built on `react-swipeable-views-react-18-fix`), `QuiltedImageList`, `CustomTabPanel`, `ActionAreaCardImage` and `ActionAreaCardVideo`, and `CustomizedDialogsForEquipment`.
- Stack: React 18, MUI (`@mui/material`, `@mui/icons-material`) with Emotion, Sass modules.

Any content change needs the original CRA project: edit there, run `npm run build`, and redeploy. Hand-editing the minified bundles here is a last resort, and if you do it, the `.map` files will no longer match.

Known oddity: `index.html` contains `<link href="../src/fonts/Klukva_version_01.otf" rel="stylesheet">`, which is a broken path left over from the source `public/index.html`. The font actually loads through the CSS bundle.
