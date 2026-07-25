# Markdown Viewer

A fast, client-side markdown viewer/editor with live preview, 22 themes,
Mermaid diagrams, LaTeX/KaTeX math, syntax highlighting, and export to
Markdown/HTML/PDF. Static HTML/CSS/JS, no build step, no backend.

Fork of [`argha5/markdown-view`](https://github.com/argha5/markdown-view)
(MIT). The upstream repo shipped broken as cloned — the "minified assets"
commit deleted the `<script>` tags for marked.js/highlight.js/KaTeX/Mermaid/
html2pdf.js without replacing them, so a fresh clone throws `marked is not
defined` on load — and had a ~2,400-line block of CSS sitting outside any
`<style>` tag due to a lost opening tag from the same edit. This fork fixes
both, along with a dead SEO content block, a set of internal links that
don't resolve to real pages even on the upstream live site, and the
Ahrefs analytics beacon.

It also drops the AI writing assistant (a client-side Groq API integration
requiring a user-supplied key) and the SEO/marketing content upstream ships
— neither serves a general-purpose fork.

Built for a homelab, single-user deployment as a container image — see
[`garage1118/markdown-view`](https://hub.docker.com/r/garage1118/markdown-view)
on Docker Hub.

## Non-Goals

Explicitly cut, and not to be reintroduced without a deliberate decision:

- **AI assistant.** Upstream ships a client-side Groq API integration
  (grammar/rewrite/summarize) that requires a user-supplied API key and
  makes external network calls. Not needed for a private single-user tool.
- **Analytics / trackers.** Upstream embeds an Ahrefs beacon pointed at the
  original author's account. Removed entirely.
- **SEO surface area.** Upstream carries a large unstyled marketing/FAQ
  content block, keyword-stuffed meta tags, JSON-LD structured data,
  `robots.txt`, `sitemap.xml`, and a `/about`, `/privacy`, `/terms`,
  `/cheatsheet` link set that mostly resolves to dead ends (see
  [Provenance](#provenance--deviations-from-upstream)). None of this serves
  a homelab deployment with no public audience.
- **Multi-user / accounts / sync.** Single browser, `localStorage`-only
  persistence. No backend, no database, by design.
- **Build tooling.** No bundler, no framework, no `package.json`. The app
  is plain HTML/CSS/JS; this is a feature to preserve, not a gap to fill.

## Features

| Feature | Notes |
|---|---|
| Live markdown preview | `marked.js` 9.1.6, debounced render |
| 22 visual themes | CSS custom properties, `themes.css` |
| GFM support | tables, task lists, strikethrough |
| Syntax highlighting | `highlight.js` 11.9.0 |
| Mermaid diagrams | `mermaid` 10.6.1 |
| LaTeX/KaTeX math | `katex` 0.16.9 + auto-render |
| Export | Markdown / HTML / PDF (`html2pdf.js` 0.10.1) |
| Table of contents | auto-generated from headings, client-side |
| Command palette | `Ctrl+K`, fuzzy search over commands |
| Find & replace | in-editor, `Ctrl+F` |
| Version history | saved to `localStorage`, no server round-trip |
| Drag-and-drop `.md` import | |
| Resizable split pane, mobile view toggle | |

All of the above work fully offline once dependencies are loaded — no CDN
calls at runtime, and no outbound network calls of any kind (the AI
assistant path, the only thing that ever called out, has been removed
entirely, not just left unconfigured).

## Try it locally

No build step — just open `index.html` in a browser. Note that `themes.css`,
`style.css`, and `script.js` load relative to `index.html`, and the app
expects `marked`, `highlight.js`, `mermaid`, `katex`, and `html2pdf.js` to
already be available as globals (loaded via `<script>` tags before
`script.js` runs, or vendored in — see whatever deployment wraps this repo).
Pinned versions are listed in [Features](#features) above.

## Data & Persistence

`localStorage` only, scoped to whatever origin the app is served on:

| Key | Purpose |
|---|---|
| `markdown_content` | current document |
| `markdown_theme` | selected theme id |
| `markdown_toc_visible` | TOC sidebar state |
| `markdown_history` | version history (JSON array) |

No cookies, no server sessions, no accounts. Clearing browser storage resets
the app to defaults.

## Versioning & Maintenance Posture

Upstream has **no releases, no tags, no changelog**, and its live deployment
has drifted from its own git history (different `script.js`, extra
libraries, functions never committed — confirmed by diffing the deployed
site against the repo). There is no reliable upstream signal to track.

Given that, this fork is treated as **frozen and self-maintained**, not a
tracking branch: don't attempt to re-sync with upstream `main` wholesale —
cherry-pick specific fixes only after independently verifying they're needed
and don't reintroduce the issues below.

## Provenance & Deviations from Upstream

For the record — issues found in the original repo that this fork fixes or
removes, so future maintenance doesn't reintroduce them:

- **Root `index.html` was non-functional as cloned.** The commit that
  "minified" assets deleted the `<script>` tags for marked/highlight.js/
  KaTeX/Mermaid/html2pdf.js without replacing them with anything — the app
  would throw `marked is not defined` on load.
- **~2,400 lines of orphaned CSS** sat in `<head>` outside any `<style>`
  tag (a lost opening tag from the same botched edit), which browsers would
  have dumped as visible garbage text at the top of the page.
- **Dead SEO content block** (`#seoContent`/`#seoArticle`) had a working
  toggle button (`aria-expanded`, chevron icon) but zero CSS and zero JS
  behind it — rendered permanently wide open, pushing the real app off
  screen. Removed outright rather than fixed, per [Non-Goals](#non-goals).
- **`/about`, `/privacy`, `/terms`, `/cheatsheet`, `/tools/markdown-to-pdf`,
  `/tools/markdown-to-html`, `/tools/table-generator`** are not real pages
  on the live site — Cloudflare Pages silently serves `index.html` (200,
  not 404) for any unmatched route, so most of these links just reopen the
  same app. `/tools/markdown-to-pdf/` (with trailing slash) is the one
  exception — a real, distinct static landing page — but it's redundant
  with the app's own Export → PDF button, so it isn't carried over here.
  All internal link cruft is removed; the footer just links to GitHub.
- **Ahrefs analytics** embedded with the original author's tracking key.
  Removed.
- Upstream's deployment is manual (copy files into `dist/`,
  `wrangler pages deploy`), no CI, no tests, no build reproducibility.

## License

MIT — see `LICENSE`. Original work by [Argha](https://github.com/argha5).
