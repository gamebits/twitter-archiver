# AGENTS.md

## Cursor Cloud specific instructions

This repo is a **static, browser-only** Twitter archive generator. It takes a Twitter/X
archive zip (format circa December 2022), processes it entirely in the user's browser, and
downloads a new zip containing a searchable static website. There is no backend, build step,
or package manager.

### Project layout

| Path | Role |
|------|------|
| `index.html` | Generator page — form, file picker, and progress UI |
| `app.js` | Core logic — zip parsing, tweet HTML generation, output zip assembly (~660 lines) |
| `unzipit.min.js` | Vendored [unzipit](https://github.com/greggman/unzipit) — reads input zips |
| `fflate.min.js` | Vendored [fflate](https://github.com/101arrowz/fflate) — writes output zips |
| `FileSaver.min.js` | Vendored download helper |
| `htaccess.txt` | Sample Apache rewrite rules for per-tweet directory URLs |
| `.jshintrc` | JSHint config (`esversion: 6`) |

### Local development

- **No install step.** Open `index.html` directly or serve the directory with any static
  file server, for example:

  ```bash
  python3 -m http.server 8000
  ```

  Then open `http://localhost:8000/`.

- **Lint:** `jshint app.js` (requires JSHint installed globally or via npx). Config is in
  `.jshintrc`.

- **No committed test suite.** Validate changes manually with a small Twitter archive zip.

### Key behavior

- `parseZip()` in `app.js` is the main entry point when the user clicks "Make my archive".
- Input zips are read with **unzipit** (including Zip64 Twitter exports that JSZip could not read).
- Output `archive.zip` is built with **fflate** (pure JS; reliable in Safari).
- Processing is **100% client-side**; archive data never leaves the browser.
- Large archives (>2 GB) may fail due to browser memory limits — this is a known constraint,
  not a bug in the parsing logic alone.

### Privacy and scope (do not regress)

The generator intentionally excludes DMs, public replies to others, circles, moments,
co-tweets, spaces, and other non-basic tweet content. See the privacy notice in `index.html`.

### Upstream

Forked from [dariusk/twitter-archiver](https://github.com/dariusk/twitter-archiver). License:
MIT (`LICENSE.md`).
