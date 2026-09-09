# Agent instructions for dbt-state-guide

## What this repo is

A single-file, interactive HTML explainer for dbt State (`index.html`), built
as the companion to a dbt meetup talk in Berlin (September 2026). Hosted on
GitHub Pages with zero build step and zero dependencies — inline CSS/JS, no
external requests, works offline over `file://`. Keep it that way: don't
introduce a bundler, framework, package.json, or any external asset/CDN
reference unless explicitly asked. `.nojekyll` must stay — it stops GitHub
Pages from running Jekyll over the raw files. The audience is dbt
practitioners already fluent with `--defer`, so the content targets the parts
that surprise experienced users, not a beginner explanation.

The model: two sources into two lanes converging on a mart. `raw.orders`
(fresh, updates every 90 min) → `stg_orders` (view) → `int_order_items`
(table) → `fct_orders` (incremental); `raw.customers` (stale, 3 days) →
`stg_customers` (view) → `snap_customer_tier` (snapshot) → `dim_customers`
(table); both lanes → `mart_customer_360`. `assert_order_total_positive` on
`fct_orders` is the test failure that drives scenes 3–5. `stg_orders` and
`stg_customers` are the paired select-* example: `stg_orders`' CTE names its
columns explicitly, so dbt State can resolve `select *` statically and
reuse it; `stg_customers`' CTE is itself a `select *` (star-on-star), so it
rebuilds every run regardless of freshness — still $0 DATT (it's a view),
just a wasted build. `stg_payments` has its own, unrelated always-rebuilds
quirk (Jinja `env_var()` in a column) — a third, deliberately different
reason a view still rebuilds. `DEEPDIVE_STAR` in `index.html` holds the SQL
for all three and is shown by clicking any of the three nodes in scenes 2–3
(`scene.deepDive`); keep its three `why` strings in sync with the node-hover
`nodeNotes` text and this paragraph if the mechanics ever change. Don't
accidentally make `stg_customers` reusable again when touching scene copy
or `evaluateTuning` — its whole point is being the contrast case to
`stg_orders`. Metrics (`tally`,
`economics`, `dattKeys`) are computed at runtime from painted node state —
never hardcode a total; change a node's state and the meters/ledger/log
follow automatically. `economics().wh` is warehouse compute, displayed via
the `credits()` formatter; DATT cost is real dollars via `money()` — never
sum the two into one figure again, that bug is why the ledger's old combined
"total" row was removed. `BILLABLE(id)` in `index.html` is the single place the
view-never-billed carve-out lives — it gates the DATT dot, ledger, log
wording, and meter subtitle. Keep it that way rather than duplicating the
check elsewhere.

## Content accuracy — claim status

Every dbt State claim in `index.html` was fact-checked against the sources in
`README.md`. Re-verify against those sources before changing any behavior,
copy, or pricing/timing claim in the premises, notes, or decision logic
(`evaluateTuning`) — this content should not drift. Warehouse-compute figures
are expressed in credits (a neutral illustrative unit, not real pricing) and
durations are illustrative too; the DATT unit price ($0.094) is real,
published pricing and stays in dollars — see README "A note on the numbers".

Not all claims carry the same confidence — know which is which before editing:

- **Verified in public docs**: views are reused (not rebuilt) when logic is
  unchanged even with new upstream data; a DATT is a distinct target table
  dbt State skips/clones/reuses a test on in a UTC day, unit price $0.094,
  each test is its own target table; test results are reused when the tested
  node is unchanged; `lag_tolerance` only affects data freshness (a SQL
  change rebuilds regardless); Python models and custom materializations are
  never reused. A view using `select *` is only reused when every implicit
  column it expands to can be statically determined without executing the
  query — `select *` over a CTE with an explicit column list (the standard
  staging pattern, and what `stg_orders` uses) qualifies. A `select *`
  sitting over something dbt can't statically resolve rebuilds
  unconditionally. (Formerly listed below as a "known documentation
  conflict" — resolved; see the `views-rebuilt` FAQ in README's Sources.)
- **Illustrative interpretation — plausible but not confirmed wording**:
  `stg_customers` is modeled as the contrast case to `stg_orders` — its CTE
  is itself a `select *` (star-on-star, no explicit column list anywhere in
  the chain), which this build treats as the canonical example of "dbt can't
  statically resolve it." The general rule above (verified) is sourced to
  the `views-rebuilt` FAQ; the specific claim that a nested `select *` is
  what defeats resolution is this build's own extension of that rule, not a
  line quoted from the FAQ. Don't cite the "star-on-star" framing as
  verbatim documented behavior without checking the FAQ's actual wording
  first.
- **Verified only in the dbt-agent-skills repo, not on the public billing
  page — confirm with PMM before this is quoted publicly**: views are never
  billed as DATTs even when reused/cloned (their tests still bill normally,
  this is load-bearing for scene 2's "REUSE, FREE" framing); a reused test
  result surfaces a failure without re-executing the query (scene 5 depends
  on this); deferral itself consumes no DATTs; dbt Core 1.7–1.11 need
  `pip install dbt-state`, it's built into 1.12/v2 and Fusion.
- **Inferred, not documented anywhere — the main open question**: whether
  tests re-run for a reused view when new source data arrives. The build
  assumes **yes** (scene 2: `stg_orders` reused, its `not_null` test still
  executes), reasoned from how dbt State propagates data freshness through
  views and walks upstream past a view to find a real table. No source states
  this outright — verify empirically with `dbt state explain --verbose -s
  <test>` after a source load with no SQL changes before treating it as fact.
- **Modeling assumptions** (not directly stated in docs, adopted for this
  build): a DATT is per target table (database + schema) per day, so dev
  clones count separately from prod skips of the same model — if DATTs are
  actually per-node, scene 5's dbt State cost drops toward zero; sources are
  never DATTs (dbt doesn't build them, they only contribute freshness
  timestamps); `lag_tolerance` is modeled as "rebuild once elapsed time since
  the last upstream data change exceeds the tolerance."

**Before sharing this page more widely than a meetup talk**, resolve the two
PMM-flagged items above — a hosted page makes these claims quotable in a way
a slide deck in a room isn't.

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
