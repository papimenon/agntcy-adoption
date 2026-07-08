# AGENTS.md — working in this repo

This repo is a single static page: `index.html`. There is no build step, no
framework, no package.json. To preview a change, just open `index.html` in a
browser (or run `python3 -m http.server` and visit it locally).

## What this page is

A long-scroll, interactive page presenting AGNTCY's adoption & usage metrics
(downloads, contributors, ecosystem projects, community programs, partner
integrations, and production use cases), styled in Outshift by Cisco's dark,
gradient-accented visual language. Source of truth for the numbers is the
"AGNTCY — Adoption and Usage" deck; when updating a stat, update it from a
newer version of that deck (or an equivalent primary source) and note the
source in your PR description.

## File layout (everything lives in `index.html`)

- `<style>` block (top of `<head>`): design tokens as CSS custom properties on
  `:root` — `--bg`, `--blue`, `--violet`, `--pink`, `--teal`, `--amber`,
  `--grad` (the signature blue→violet→pink gradient used for headings, chart
  accents, and CTA buttons). Reuse these variables; don't hardcode new colors.
- `<body>`: one `<section id="...">` per content block (`metrics`, `growth`,
  `community-growth`, `ecosystem`, `community`, `membership`, `outreach`,
  `integrations`, `partners`, `mach`, `usecases`). Each section follows the
  same pattern: an `.eyebrow` label, an `<h2>`, then a `.stat-grid`, `.panel`,
  or `.spot-card` layout. Copy an existing section's markup rather than
  inventing a new pattern.
- Single `<script>` at the bottom, in this order:
  1. Scroll progress bar + `.reveal` intersection-observer animations
  2. Animated counters (`.counter` elements, driven by `data-target` /
     `data-suffix` attributes — no JS edits needed to add a new counter)
  3. Dot-nav (auto-generated from every `<section>`)
  4. Chart.js chart configs — one `new Chart(...)` block per chart, each with
     its data as a plain array literal right above the chart config
     (`dlMonths`/`dlGithub`/`dlPypi` for the downloads chart, `contribMonths`/
     `contribVals`, `caipeMonths`/`caipeVals`, `acordaMonths`/`acordaVals`)
  5. Org-name marquees (`orgs1`, `orgs2` arrays)
  6. Quote carousel (`quotes` array of `{text, meta}`)
  7. Use-case grid (`useCases` array of `{title, tags, partner, problem,
     solution, link}` — rendered into `.uc-card` markup by a template
     string; tags must be one of `communication`, `identity`,
     `observability`, `discovery` to work with the filter buttons)

## Adding something new

- **New stat card**: copy a `.stat-card` block, give the counter a new
  `data-target`.
- **New chart**: add a `<canvas id="...">` inside a `.chart-wrap`, then add a
  matching `new Chart(...)` call in the script using the existing charts as a
  template. Only Chart.js is available (already loaded via the pinned CDN
  `<script>` tag in `<head>` — don't add other chart libraries).
- **New use case card**: just add an object to the `useCases` array; the grid
  and filters update automatically.
- **New section**: duplicate an existing `<section>`, give it a unique `id`,
  and it will automatically pick up scroll-reveal, dot-nav, and progress-bar
  behavior — no JS changes required.

## Constraints

- Keep it a single self-contained file — inline all CSS/JS, no external
  fonts (the page uses the system font stack on purpose), no new CDN
  dependencies beyond the existing Chart.js tag.
- Preserve the dark/gradient Outshift visual language — don't introduce a
  light theme or a different accent palette.
- Don't invent numbers. Every stat should trace back to the source deck or
  an equivalent primary source; cite it in the PR description.
