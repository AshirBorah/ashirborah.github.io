# ashirborah.github.io — site notes

Personal site for Ashir Borah. Static GitHub Pages, hand-written HTML/CSS, no build step.

## File map

- `index.html` — homepage (hero, currently, writing teaser, about, research, publications, experience, contact)
- `cv/index.html` — formal CV page (experience, publications, honors, languages)
- `writing/index.html` — writing channel: single-page router that renders either the post list or an individual post via `?p=<slug>` query param
- `writing/posts/<slug>.md` — individual posts in Markdown with YAML-style frontmatter
- `writing/posts.json` — manifest of all posts; **must** be updated when adding a post (GitHub Pages doesn't expose directory listings, so the index page reads this file)
- `styles.css` — all site styles, shared across every page
- `docs/` — source material the user has shared (resume PDF, LinkedIn export, scholar export)
- `resume/` — user's separate LaTeX CV project. **Do not touch this.** It's owned by the user and builds independently to a PDF.

## Adding a new writing post

Two-file change. The user can do this in <60 seconds.

1. Create `writing/posts/<slug>.md`:

   ```markdown
   ---
   title: Post title
   date: YYYY-MM-DD
   summary: One-line description shown on the index and home teaser.
   ---

   Body in Markdown. Supports GFM: headings, lists, links, **bold**, *italic*,
   `inline code`, fenced code blocks (with language for syntax highlighting),
   blockquotes, tables, images, hr.
   ```

2. Add an entry to `writing/posts.json`:

   ```json
   { "slug": "<slug>", "title": "Post title", "date": "YYYY-MM-DD", "summary": "..." }
   ```

   Order in the array doesn't matter — the JS sorts by `date` descending at render time.

3. Commit and push. The post is live immediately at `/writing/?p=<slug>`.

The home page's "Writing" teaser shows the latest 3 posts automatically (also driven by `posts.json`).

## Architecture (writing channel)

- **Markdown rendering**: [`marked`](https://marked.js.org/) v12.0.2 is vendored in `assets/vendor/marked-12.0.2.min.js`, with its license alongside it. Posts remain readable if the highlighting CDN is unavailable; a formatting failure shows the original Markdown text.
- **Syntax highlighting**: [`highlight.js`](https://highlightjs.org/) v11.9.0 via jsDelivr CDN. Two stylesheets are loaded (`atom-one-light` and `atom-one-dark`); the JS toggles them based on `prefers-color-scheme`.
- **Frontmatter parsing**: hand-rolled regex in `writing/index.html` — supports simple `key: value` pairs (with optional surrounding quotes). Don't use nested YAML or arrays in frontmatter.
- **Routing**: a single `writing/index.html` handles both list view and post view. If `?p=<slug>` is present, it hides the list and fetches `posts/<slug>.md`. Otherwise it fetches `posts.json` and renders the list.
- **No build step**. Resist suggestions to add Jekyll, Vite, MDX, etc. The user explicitly chose client-side rendering for the zero-build authoring flow.

## Design system (quick reference)

- **Palette** (defined in `:root` of `styles.css`):
  - `--bg: #f7f4ed` (warm cream)
  - `--surface: #fdfbf7` (slightly lighter for cards)
  - `--ink: #1a1815` (warm near-black text)
  - `--muted: #6b6660` (warm gray)
  - `--rule: #e2dccd` (subtle hairlines)
  - `--accent: #1f4e4a` (deep teal)
  - `--accent-soft: #1f4e4a14` (transparent teal for backgrounds, code, blockquotes)
  - Auto dark mode via `prefers-color-scheme`.
- **Type**: Fraunces (serif headings) + Inter (sans body) + JetBrains Mono (code), all from Google Fonts. `--serif` and `--sans` CSS vars hold the stacks.
- **Containers**:
  - `.container` → `max-width: 1180px` (wide content like research, publications, experience)
  - `.container-prose` → `max-width: 760px` (narrow prose like currently, contact, writing)
- **Eyebrow**: `.eyebrow` → small uppercase label in accent color, used at the top of every section.

## Don'ts

- Do not introduce a build step (Jekyll, Vite, Astro, etc.) without explicit user approval.
- Do not modify `resume/`. It's the user's separate LaTeX CV project.
- Do not commit generated PDFs or other artifacts to the repo unless asked.
- Don't reach for external state stores (a CMS, database, GitHub API). The site is intentionally a flat directory of static files.

## Verification

After any change, run from the repo root:

```bash
python3 -c "
from html.parser import HTMLParser
class V(HTMLParser):
    def __init__(self):
        super().__init__()
        self.stack = []; self.errors = []
        self.void = {'meta','link','br','hr','img','input','area','base','col','embed','source','track','wbr'}
    def handle_starttag(self, tag, attrs):
        if tag not in self.void: self.stack.append((tag, self.getpos()))
    def handle_endtag(self, tag):
        if not self.stack: self.errors.append(f'orphan </{tag}> at {self.getpos()}'); return
        top, pos = self.stack[-1]
        if top != tag: self.errors.append(f'expected </{top}> opened {pos} got </{tag}> at {self.getpos()}')
        self.stack.pop()
import glob
for f in sorted(glob.glob('**/*.html', recursive=True)):
    if f.startswith('resume/'): continue
    v = V(); v.feed(open(f).read())
    print(f'{f}: {\"OK\" if not v.stack and not v.errors else \"FAIL\"}')
"
```

Then preview locally: `python3 -m http.server` from the repo root, open `http://localhost:8000/`.
