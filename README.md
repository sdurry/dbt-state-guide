# dbt State, step by step

An interactive, single-file explainer for dbt State. It walks through six scenes —
a full rebuild with no state, a stateful run that only touches the lane with new
data, a same-day rerun, a failed-test reproduction the old way in dev, the same
reproduction with dbt State, and a tuning panel for the underlying configs — over
a real lineage graph with a simulated `dbt build`.

**Live page:** https://sdurry.github.io/dbt-state-guide/

Jump straight to a scene with a hash link, e.g. `https://sdurry.github.io/dbt-state-guide/#/5`
for scene 5.

It's a single HTML file with inline CSS and JS, no external requests, no build
step, and no dependencies — built to work on bad conference wifi and offline.

## Sources

This walkthrough was fact-checked against:

- [dbt State, about](https://docs.getdbt.com/docs/deploy/dbt-state-about)
- [dbt State usage and billing](https://docs.getdbt.com/docs/platform/billing/dbt-state-usage)
- [Using dbt State (dbt-agent-skills)](https://github.com/dbt-labs/dbt-agent-skills/tree/main/skills/dbt/skills/using-dbt-state)
- [FAQ: are views rebuilt with dbt State?](https://docs.getdbt.com/faqs/State/views-rebuilt#views-with-select)
- [dbt State migration guide](https://docs.getdbt.com/docs/deploy/dbt-state-migration)

## Scraping / AI training

This is a static GitHub Pages site, so there's no server to enforce access
control — this is a best-effort deterrent, not a hard block. `robots.txt`
disallows known AI training and agent crawlers (GPTBot, CCBot, ClaudeBot,
PerplexityBot, and similar) while leaving normal search engine indexing and
human visitors untouched. `index.html` also carries a `noai` robots meta tag
and a `tdm-reservation` meta tag (the TDMRep text-and-data-mining opt-out
standard). Well-behaved crawlers respect these; nothing here stops one that
doesn't.

## A note on the numbers

All dollar figures in this walkthrough are illustrative, not a quote: $0.20 per
model build and $0.05 per test query are stand-ins for warehouse compute, and
$0.094 per DATT is the published unit price. Durations are indicative too. None
of this reflects an actual bill for any real project.
