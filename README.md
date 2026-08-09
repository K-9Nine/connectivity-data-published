# Amvia UK Connectivity Data — published feeds

Aggregate statistics on the UK business connectivity market, derived from a
working provider's own quote, order, delivery and billing records
(2015–2026). Everything here is aggregate: prices, delivery times, excess
construction charges and contract status, summarised by region, speed band
and term. No customer, site or circuit is identifiable in any file, and
nothing below a minimum sample size is published at all.

## What is in here

Artifacts only. Each publication is an immutable dated directory:

```
v1/<YYYY-MM-DD>/<feed>.json
v1/<YYYY-MM-DD>/manifest.json     # sha256 + byte length for every file
```

A dated directory is never rewritten. Corrections ship as a new date; the
old one stays exactly as published so a citation never silently changes
underneath the person who made it.

| Feed | What it covers | Licence |
|---|---|---|
| `delivery_lag` | Order-to-live delivery time (median / IQR / p90 days) by service family, speed band, region and era | CC-BY-4.0 |
| `contract_status` | Share of currently-billed circuits already past their contract end | CC-BY-4.0 |
| `ecc_pooled` | Excess construction charges: quoted-amount distribution and an incidence floor | CC-BY-4.0 |
| `benchmark_cells` | Leased-line monthly price cells: region × speed band × term, quoted and transacted | All rights reserved — contact for licence |
| `regional_medians` | Leased-line monthly price medians by region × speed band, across terms | All rights reserved — contact for licence |

Reserved slots appear in `manifest.reserved_feeds` before they have data:
`availability_gap` (awaiting availability-log ingestion) and
`rollover_tenure` (awaiting a pre-registered pooled statistic — see
*Method* below).

## How to cite

**Attribution text:** `Source: Amvia UK Connectivity Data`

**Pin your citation to a commit.** `main` moves as new dates are published;
a commit SHA never does:

```
https://raw.githubusercontent.com/K-9Nine/connectivity-data-published/<commit-sha>/v1/<date>/<feed>.json
```

**For the latest published set**, track `main`:

```
https://raw.githubusercontent.com/K-9Nine/connectivity-data-published/main/v1/<date>/<feed>.json
```

Verify what you fetched against `manifest.json` in the same directory: it
carries a sha256 and byte length per file.

## Method and gating

Every figure passes the same regime before it is written, and the pipeline
aborts rather than degrading if any check fails:

- **Minimum sample size.** No statistic is published from fewer than 5 data
  points. Thin cells are rolled up to a wider granularity and labelled as
  such, or marked `"suppressed": true` — never dropped silently, so a gap
  is always visible as a gap.
- **Confidentiality by construction.** Buy-side and supplier-cost data
  physically cannot enter a feed: the producers are a whitelist validated
  before any query runs, and the serialised output is re-scanned for
  forbidden vocabulary. A violation aborts the entire run before the first
  byte is written.
- **Stated denominators.** Any rate or duration carries the population it
  was computed over (`population`) and the anchor it was measured from
  (`basis`). A percentage without a denominator is not publishable.
- **Pre-registration.** Method, population and gates for a new statistic
  are fixed in code and tests *before* it is computed, so a number cannot
  be tuned after it is seen. Where a statistic was exploratory, it is
  reserved rather than published — a headline figure has to be defensible
  without a footnote, because a licence that permits reuse cannot compel
  the caveat to travel with it.

Every stat also carries `as_of`, `n` and `method_version`; every file
carries the `source_commit` of the pipeline that produced it.

## Fields common to every feed

| Field | Meaning |
|---|---|
| `feed`, `description` | Feed identity |
| `as_of` | The date this set describes |
| `generated_at`, `source_commit` | When it was produced, and from which pipeline commit |
| `method_version` | Semantic version of the computation; a change in method bumps it |
| `min_cell_n` | The suppression threshold in force |
| `terms` | Licence for this feed (see table above) |
| `attribution_text`, `canonical_url` | Present on CC-BY feeds |
| `stats[]` | The statistics, each stamped with `as_of`, `n`, `method_version` and `suppressed` |

## Licence

Split by tier — see [LICENSE](LICENSE). Headline feeds are CC-BY-4.0: use
them freely, including commercially, with attribution. Granular price-cell
feeds are all rights reserved; get in touch for licensing.

## Contact

Licensing, corrections, or questions about method: open an issue on this
repository.
