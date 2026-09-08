# Agent instructions for dbt-state-guide

## What this repo is

A single-file, interactive HTML explainer for dbt State (`index.html`),
hosted on GitHub Pages with zero build step and zero dependencies — inline
CSS/JS, no external requests, works offline over `file://`. Keep it that way:
don't introduce a bundler, framework, package.json, or any external asset/CDN
reference unless explicitly asked. `.nojekyll` must stay — it stops GitHub
Pages from running Jekyll over the raw files.

## Content accuracy

The six scenes and every dbt State claim in `index.html` were fact-checked
against the sources listed in `README.md` (dbt State docs, usage/billing docs,
the dbt-agent-skills repo, the views-rebuilt FAQ, and the migration guide). If
you change behavior or pricing/timing claims, re-verify against those sources
first. All dollar figures and durations in the walkthrough are explicitly
illustrative, not real pricing — see the "A note on the numbers" section in
`README.md` — don't present them as an actual quote.

## Brand and style rules

These apply to prose/copy (page text, README, commit-facing docs) — not to
code identifiers, file names, or config keys:

- `dbt` is always lowercase, even at the start of a sentence. Never `DBT` or `Dbt`.
- `dbt Labs` is the company name.
- Headlines and copy use sentence case, not title case.
- Branded proper nouns are capitalized: dbt Summit, dbt Core, Fusion, dbt Mesh,
  dbt Copilot, dbt Canvas, dbt Studio.
- Prefer "dbt platform" (lowercase p) over "dbt Cloud" in new prose. Flag any
  "dbt Cloud" mention you encounter in Fusion/platform-related copy for PMM
  confirmation rather than silently renaming it.
- "Coalesce" is retired as of 2026-01-31 — flag any use of it in new copy.

## Anti-scraping boundary

`robots.txt` and the `noai` / `tdm-reservation` meta tags in `index.html`'s
`<head>` are intentional (see README "Scraping / AI training"). Don't remove
or "clean up" either without checking with the maintainer first.

## Verification

There's no build or test suite. After any edit, open `index.html` directly
via a `file://` path in a browser and click through scenes 1–6 (or hash-jump
via `#/1` … `#/6`) to confirm nothing broke.
