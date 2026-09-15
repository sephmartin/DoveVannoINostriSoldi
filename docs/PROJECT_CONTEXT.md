# DVNS — project context for agents

> Stable, repository-portable context for **DoveVannoINostriSoldi**.
> Read this file before planning, reviewing, implementing, or opening a PR.
> Dated branch, deployment, issue, and source-health claims must be checked
> against the current repository or live service before being reported as true.

## Mission

DoveVannoINostriSoldi (DVNS) is an open-source civic project that makes
official Italian public-money data readable and verifiable.

The guiding question is: **where did this money go?** Every answer keeps the
source, period, scope, unit, freshness, method, and limitations visible. An
unusual value is a lead for verification, not proof of waste, corruption,
fraud, or another offence.

Public site: <https://www.dovevannoinostrisoldi.com>

The repository is a Next.js/React application with HTTP APIs, verified data
artifacts, ETL scripts, browser tests, and a public read-only MCP server. The
repository README and `docs/` are the primary product documentation; this file
preserves the cross-cutting context that is easy to lose between branches.

## Agent start sequence

1. Read this file and `AGENTS.md`.
2. Run `git status --short --branch` and identify the exact branch/worktree.
3. Route to the smallest relevant reference set below.
4. Inspect the current source, tests, and live state before making a claim that
   can drift.
5. Preserve source, period, scope, semantics, provenance, and caveats in the
   change.
6. Before handoff, report the evidence level: local, test/CI, deployed
   readback, or historical.

Completion means that the requested change is implemented in the intended
worktree, its relevant checks have run, and no live/merge/deploy claim is made
without corresponding evidence.

## Non-negotiable data semantics

| Data family | What it measures | What it must not be called |
| --- | --- | --- |
| SIOPE municipal | cash payments/collections | accrual revenue, economic cost, efficiency |
| SSN Conto Economico | aggregated economic-accounting costs | cash payments, named contractors, staffing |
| MEF IRPEF | declared net tax, with statistical secrecy | total tax revenue, evasion, individual responsibility |
| CPT | consolidated territorial public-finance flows | residual fiscal balance |
| PNRR/Italia Domani | projects and assigned financing | realised expenditure or payment |
| OpenCoesione | projects with source-specific cost/payment/status fields | a single undifferentiated national total |
| Company Atlas | territorial aggregates | a nominal company register or individual turnover |

Keep accounting systems separate. Never turn an unavailable, suppressed, or
non-attributed value into zero. Never infer guilt, quality, efficiency, or
responsibility from an indicator alone. A signal page must say what is
observed, how it was calculated, and what remains unknown.

The Company Atlas is aggregate-first. `ATECO 2025` data from chamber/InfoCamere
sources and `ATECO 2007 agg. 2022` data from ISTAT are different classifications;
do not invent a crosswalk. Coverage, licensing, privacy, and source freshness
are part of the dataset contract.

## Provenance and architecture

The data path is:

```text
source registry -> official acquisition -> immutable raw input
  -> normalized records -> semantic metrics -> UI/API/MCP
```

The repository contract is:

- acquire only from documented official sources, with HTTPS, allowlists,
  bounded timeouts, limited retries, observation metadata, and hashes;
- keep raw input immutable and transformations reproducible;
- keep IPA, tax/VAT identifiers when publishable, CIG, CUP, ISTAT codes, and
  native source identifiers distinct; a fuzzy name match is not a fact;
- validate schema, cardinality, nulls, duplicates, ranges, time continuity,
  referential integrity, checksums, and reconciliation before publishing;
- expose source publication time, observation time, ingestion time, freshness,
  scope, and caveats;
- use the same domain contract in UI, API, and MCP instead of duplicating
  fetches or normalization;
- fail visibly when a source is unavailable or changes shape; do not replace
  missing live data with demo values;
- keep ordinary CI deterministic and separate from live source health.

Generated snapshots are not disposable build output: their metadata, hash,
source lock, validation, and interpretation are part of the public evidence
chain. Read the relevant ETL/data contract before editing a generated artifact.

## Routing table

Load only the references relevant to the task:

| Task | Read next |
| --- | --- |
| Any code or data change | `README.md`, `CONTRIBUTING.md`, `docs/ARCHITECTURE.md` |
| ETL, snapshot, source, freshness | `docs/INTEGRATED_SOURCE_LEDGER.md`, `docs/FRESHNESS_AND_REFRESH.md`, the relevant dataset doc and source contract |
| SIOPE or municipal spending | `docs/SIOPE_MUNICIPAL.md`, relevant `src/lib/data/` contract and ETL tests |
| Health accounting or history | `docs/SSN_NATIONAL_HISTORY.md`, the relevant health data contract and route tests |
| Company Atlas or education Atlas | the relevant module doc, source ledger, ETL, and snapshot contract; preserve aggregate scope |
| UI, responsive, accessibility, visual hierarchy | `DESIGN.md`, `docs/UI_HIERARCHY_ARCHITECTURE.md`, the relevant UI audit, component and browser tests |
| MCP, API, client distribution | `docs/MCP.md`, `docs/MCP_DISTRIBUTION.md`, route and HTTP smoke tests |
| Legal, privacy, public wording | `docs/LEGAL_AND_ETHICS.md`, `SECURITY.md`, the source licence and the exact user-provided context |
| Review or PR | `CONTRIBUTING.md`, this file, the relevant contract and current diff |

Environment details belong in `package.json`, scripts, and the repository
docs. This context file records the reasoning and boundaries that commands
alone cannot reveal.

## UI and editorial grammar

The preferred reading order is **data → comparison → context → detail → source**.
Choose a visual for the question:

- geography: map plus a textual legend/table;
- trend: line or point series with interrogable values;
- ordered comparison: bars or dot plot;
- exact lookup: table;
- additive part-to-whole: treemap/donut only with common period, scope,
  denominator, total, text labels, and an equivalent accessible table/list.

Responsive layout changes composition, not meaning. Mobile work must preserve
keyboard access, readable labels, touch targets, announcements, exact values,
source visibility, and the full data contract. A click, build, or screenshot is
not by itself proof of a deployed or accessible result.

## Branch, fork, and PR boundaries

When remotes use the conventional names, `origin` is the working fork and
`upstream` is the official `Italian-Builders-Org/DoveVannoINostriSoldi` source.
Verify the actual URLs before syncing or pushing; do not assume fork/upstream
ancestry or installation permissions.

Prefer one focused PR over a broad redesign. A PR should state:

- the user-visible or data-contract goal;
- the source, period, scope, and semantic boundary;
- the files and tests that prove the change;
- known limits, partial coverage, or follow-up work;
- whether the evidence is local only or verified after deployment.

Do not port an old handoff or patch until current ancestry, upstream changes,
source metadata, and tests show that it is still needed. A GitHub permission
error can come from the active App installation not exposing the repository;
check installation scope separately from apparent user permissions.

## Known historical edges

These are useful clues, not current status:

- SIOPE receipts work previously had a stale handoff that was superseded by an
  upstream integration. Compare fork/upstream ancestry before copying files.
- The health-history work introduced separate chart scales. Recheck the live
  route and current PR/issue state before proposing another chart redesign.
- The `/coesione` contrast report was intentionally phrased as a possible
  DOM/CSS contrast concern until visual or measured WCAG confirmation existed.
- Mobile review found no document-level overflow at the tested widths, while
  navigation discoverability, labels, touch targets, filter announcements, and
  offline/PWA behaviour still required attention.
- A Portale Italia ↔ DVNS MCP bridge was evaluated but was not part of the
  public DVNS contract. If revisited, use a fixed same-origin forwarder with
  strict origin, body, timeout, method, and response handling; never proxy
  arbitrary URLs or credentials.

Re-open the current GitHub issue/PR, worktree, dev server, deployment, or
source endpoint before repeating any of these as a present-tense claim.

## Legal and communication boundary

Public data still requires licence, context, minimisation, accuracy, and
retention checks. Do not collect personal data merely because it is available.
Preserve a correction path and distinguish documented fact, calculation,
signal, missing data, and hypothesis.

For public copy, start from the exact verified post, conversation, or release
context. Keep the wording concrete and proportionate. Drafting is not
publication: external posts, replies, messages, releases, deployments, and
repository writes remain separate actions with separate evidence.

## Portability contract

This file is intentionally portable:

- use repository-relative paths and public/project identifiers;
- use no machine-specific home paths, local ports, credentials, cookies,
  tokens, account IDs, or private session references;
- describe branch/worktree state as a re-checkable condition, not a permanent
  fact;
- keep stable project rules here and dated/live observations in issues, PRs,
  source metadata, or a clearly dated report;
- when this file changes, update the pointer in `AGENTS.md` only if the entry
  path changes, and keep deeper details in the routed docs rather than copying
  them here.

The repository is the portable source of truth. A local ChatGPT/Codex hub may
contain extra checkout inventory, but agents working from a clone should need
only this file, `AGENTS.md`, the routed repository docs, and current live
evidence.
