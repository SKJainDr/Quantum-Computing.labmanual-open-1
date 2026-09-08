# Quantum Computing Laboratory Manual — I (Online Reader)

A self-contained GitHub Pages site for **Quantum Computing Laboratory Manual — I: Foundational Quantum Experiments** by Dr. S. K. Jain — the lab companion to *Quantum Computers* (Q.C. Series, Vol. I).

This is the **plain** variant: no watermark, no copy-protection deterrents — clean reading experience.

## What's inside

- `index.html` — the reader shell (sidebar TOC, topbar controls, reading pane, on-page TOC)
- `assets/css/style.css` — dark/light theme (CSS variables, toggle persists via `localStorage`)
- `assets/js/app.js` — chapter loading & routing, on-page TOC generation, read-aloud, clean reading experience (no watermark/copy-protection)
- `assets/icons/` — the pen-logo icon set (favicon, apple-touch-icon, PWA icons)
- `content/*.md` — the manual itself: front matter, lab setup, all 15 experiments, assessment guide, and index — one file per chapter, converted from your `.docx` source
- `content/manifest.json` — the chapter list that drives the sidebar (edit titles/order here)
- `content/images/` — all 67 figures extracted from the source document

## Features

Identical engine and functionality to your existing Quantum Computers / Quantum Algorithms sites:

- **Dark / light theme** toggle, remembers your choice
- **Read aloud** (Web Speech API) with sentence highlighting and auto-advance to the next chapter
- **Read aloud from any point** — click any paragraph, heading, list item, or box text in the reading pane to start (or jump) narration from there, instead of always restarting at the top of the chapter
- **Clickable, deep-linkable navigation** — every chapter, every on-page subsection, Prev/Next buttons
- **Filter box** in the sidebar to jump to a chapter/experiment quickly
- **Visitor counter** and **like button**, backed by [Abacus](https://abacus.jasoncameron.dev) (namespace `qc-lab-manual-1-skjain` — unique to this site, won't collide with your other books' counters)
- **"More in this series"** links in the sidebar, pointed at your deployed Volume I, Volume II, and Volume III textbook sites, plus Lab Manual II
- Responsive, collapsible sidebar on mobile

## How the manual content is structured

Each experiment chapter follows the manual's own structure: Background Theory → gate/protocol tables → First Program (simple) → Full Program (complete, commented) → Expected Output → Observation Tables → Discussion Questions → Lab Record Requirements → Viva Voce Q&A — converted straight from the source Word document:

- **Code blocks** — every Qiskit/Python program (First Program and Full Program versions) is rendered as a proper syntax-styled `<pre><code>` block, detected from the source document's boxed/monospace formatting.
- **Console/circuit output** — the expected-output console tables (with box-drawing characters) are preserved as monospace blocks so alignment reads correctly.
- **Figures** — circuit diagrams and visualisation panels are inline `<figure>` elements next to the text that references them.
- **Data tables** — gate sequences, observation tables, the grading rubric, and the glossary/index all render as real HTML tables.
- The large "Table of Contents" section from the original document was intentionally dropped — the sidebar chapter nav (and each chapter's own on-page TOC) already serves that purpose without a redundant, broken-anchor page.

This was built with an automated converter (pandoc + a custom Python pass), not by hand-transcribing the manual, so it's worth a skim rather than treated as pixel-perfect — flag anything that looks off in a specific experiment and I can fix that chapter's `content/NN-experiment-N.md` file directly.

## Publishing to GitHub Pages

1. Create a new GitHub repository (e.g. `quantum-computing-lab-manual-1`).
2. Copy everything in this folder into the repo root and push:
   ```bash
   git init
   git add .
   git commit -m "Quantum Computing Laboratory Manual I — online reader"
   git branch -M main
   git remote add origin https://github.com/<your-username>/<repo-name>.git
   git push -u origin main
   ```
3. **Settings → Pages → Source → Deploy from a branch → `main` / `(root)`** → Save.
4. Live at `https://<your-username>.github.io/<repo-name>/` within a minute or two.

### Testing locally before you push

```bash
python3 -m http.server 8000
# then open http://localhost:8000
```
(Opening `index.html` directly by double-clicking will **not** work — the browser blocks local `fetch()`.)

## Icon / logo

Uses your glasses-branded icon set (matching the "plain" convention already established for Volume I/II). Once deployed and loaded at least once, creating a desktop shortcut from your browser (Chrome/Edge: **⋮ → Create shortcut**) will automatically pick up this favicon.

## Cross-linking

`assets/js/app.js` → `SERIES_LINKS` already points at:
- Volume I — Quantum Computers: `https://skjaindr.github.io/Quantum-Computing.book-open-1/`
- Volume II — Quantum Algorithms & Complexity: `https://skjaindr.github.io/Quantum-Computing.book-open-2/`

- Lab Manual II — Advanced Quantum Algorithms & Hardware: `https://skjaindr.github.io/quantum-computing-lab-manual-2-site-plain/`

If you'd like your textbook sites to link back to this Lab Manual too, add an entry to their own `SERIES_LINKS` array pointing at wherever you deploy this repo.
