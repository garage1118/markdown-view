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

## Try it locally

No build step — just open `index.html` in a browser. Note that `themes.css`,
`style.css`, and `script.js` load relative to `index.html`, and the app
expects `marked`, `highlight.js`, `mermaid`, `katex`, and `html2pdf.js` to
already be available as globals (loaded via `<script>` tags before
`script.js` runs, or vendored in — see whatever deployment wraps this repo).

## License

MIT — see `LICENSE`. Original work by [Argha](https://github.com/argha5).
