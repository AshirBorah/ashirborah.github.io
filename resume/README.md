# Resume / CV source

Versioned LaTeX source for Ashir Borah's professional documents:

- **CV** (academic, multi-page) → `../docs/Ashir-Borah-CV.pdf`
- **Resume** (1-page, industry-targeted) → `../docs/Ashir-Borah-Resume.pdf`

Both build from this directory.

## Build

```bash
make             # build both CV and resume
make cv          # build CV only
make resume      # build resume only
make clean       # remove build/ (keeps committed PDFs)
make watch       # rebuild on every save (requires inotify-tools)
```

Requires a TeX distribution providing `pdflatex` (TeX Live, MacTeX, MiKTeX).
This repo's Makefile runs multiple pdflatex passes; if `latexmk` is installed,
swap in `latexmk -pdf -outdir=build <source>.tex` for fewer rebuilds.

## File map

| File | Purpose |
|------|---------|
| `cv.tex` | Academic CV — header, sections, custom commands |
| `publications.tex` | Full publication list, `\input`-ed from `cv.tex` |
| `resume.tex` | 1-page industry resume (self-contained, condensed) |
| `Makefile` | Build pipeline (`cv`, `resume`, `all`, `clean`, `watch`) |
| `.gitignore` | Ignores `build/` and TeX aux files |
| `archive/` | Older PDFs (LinkedIn export, 2021 Broad-era CV) |

## Editing

- **Add a peer-reviewed publication:** copy a `\pub{authors}{title}{venue}{year}{doi}`
  block in `publications.tex`. DOI is the bare identifier (e.g. `10.1038/s41586-...`);
  the macro adds the `https://doi.org/` prefix. Use `\me` to bold your name,
  `\etal` for the formatted "et al".
- **Add a conference abstract / poster:** use `\abstr{authors}{title}{venue+id}{year}`
  (no DOI required).
- **Update advisors / current role:** Education + Research Experience in `cv.tex`.
- **Refresh citation metrics:** line just before `\input{publications.tex}` in `cv.tex`.
- **Author-name format:** `\me` is defined once at the top of each `.tex` file
  (`\textbf{A.A.~Borah}`). Change there to update everywhere.
- **Sync resume from CV:** `resume.tex` is hand-curated for the 1-page format —
  not auto-generated. After updating `cv.tex` with a new role / honor / publication,
  manually port any items that should appear in the 1-pager.

## Open TODOs

- Verify Karner et al. *Science Advances* DOI (`10.1126/sciadv.aea9061`) — the
  Scholar listing showed an oddly mangled article identifier; double-check by
  clicking the doi link in the built CV.
- Refresh citation metrics in `cv.tex` when Scholar updates (currently 1,125 / h-10 / i10-10
  from a March 2025 snapshot).
- Decide whether to mark which AACR abstracts you presented (vs. were a
  co-author on) — common annotation: "(presenter)" or `†`.

## Provenance

Layout adapted from [Jake Gutierrez's CV template](https://github.com/jakegut/resume)
(MIT license), extended for multi-page academic use with publications, honors,
and teaching sections.
