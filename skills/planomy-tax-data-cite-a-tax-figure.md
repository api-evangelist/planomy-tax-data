---
name: Cite a tax figure back to its federal source
description: >-
  Look up a single 2026 figure — a contribution limit, a bracket, an IRMAA tier, a phase-out range,
  an RMD age — and produce it with a citation to the actual IRS, SSA, CMS or Federal Register
  document it came from, plus the date it was last verified. Use whenever a figure will be shown to
  a person or written into an answer.
api: openapi/planomy-tax-data-openapi.yml
operations:
  - getTaxDataYear
generated: '2026-08-09'
method: generated
source: openapi/planomy-tax-data-openapi.yml
---

# Cite a tax figure back to its federal source

The reason to use this dataset rather than a model's memory is provenance. Every section carries a
`source` key that resolves into the document-level `sources` map, and each entry there names the
publisher, links the canonical government document, and records the date Planomy last checked it.
A figure without that chain should not be published.

## Step 1 — fetch the year

Call **`getTaxDataYear`** — `GET https://planomy.net/tax-data/{year}.json`. Resolve the year from
the manifest first (see the *Fetch the current US tax and retirement figures* skill).

## Step 2 — read the figure and its `source` key

Each section has its own `source`:

| Question | Path | Example (2026) |
|---|---|---|
| 401(k) elective deferral | `contributionLimits.employerPlans.electiveDeferral` | 24500 |
| Age-50 catch-up | `contributionLimits.employerPlans.catchUpAge50` | 8000 |
| Age 60–63 catch-up | `contributionLimits.employerPlans.catchUpAge60To63` | 11250 |
| IRA contribution | `contributionLimits.ira.contribution` | 7500 |
| HSA self-only / family | `contributionLimits.hsa.selfOnly` / `.family` | 4400 / 8750 |
| Health FSA salary reduction | `contributionLimits.healthFsa.salaryReductionLimit` | 3400 |
| Standard deduction | `federal.standardDeduction.<filingStatus>` | 16100 single |
| Federal brackets | `federal.brackets.<filingStatus>[]` | `{min, rate}` |
| Social Security wage base | `fica.socialSecurityWageBase` | 184500 |
| COLA | `socialSecurity.colaRate` | 0.028 |
| Earnings test | `socialSecurity.earningsTest.*` | monthly / annual / withholding |
| Part B premium | `medicare.partBMonthlyPremium` | 202.9 |
| IRMAA tiers | `medicare.irmaaTiers.<filingStatus>[]` | keyed by `magiMin` |
| Roth IRA phase-out | `phaseOuts.rothIra.<filingStatus>` | `{start, end}` |
| RMD start age | `rmd.applicableAges[]` | 73 for 1951–1959, 75 from 1960 |

## Step 3 — resolve the citation

Take the section's `source` string and look it up in the top-level `sources` object:

```
jq -r '.contributionLimits.employerPlans.source as $k
       | "\(.contributionLimits.employerPlans.electiveDeferral) — \(.sources[$k].title) (\(.sources[$k].publisher)) \(.sources[$k].url), retrieved \(.sources[$k].retrieved)"' 2026.json
```

Cite the **government URL**, not planomy.net. Planomy is the transport; the IRS, SSA, CMS or Federal
Register document is the authority.

## Traps to respect

- **IRMAA uses a two-year MAGI lookback.** 2026 premiums key off 2024 MAGI. Never apply current-year
  income to an IRMAA tier.
- **`medicare.notes` documents a deliberate $0.10 divergence** in the fourth IRMAA tier:
  `partBMonthlySurcharge` is derived from the statutory 35/50/65/80/85% cost-sharing multipliers and
  differs from the CMS-published surcharge ($446.30). If you need the CMS number, read
  `partBTotalMonthlyPremium`.
- **`states` is ten presets, not fifty states.** Only the explicit `NONE` key means "no state income
  tax". A state that is simply absent is unmodelled — treating that absence as zero will understate
  tax.
- **The HSA age-55 catch-up has its own citation** (`contributionLimits.hsa.catchUpSource`) because
  it is fixed at $1,000 by statute and is not inflation-indexed.
- **Check freshness before quoting.** `lastUpdated` on the document and `retrieved` on each source
  tell you how stale a figure is. If a figure is close to an agency's publication window and
  `retrieved` predates it, go to the government URL directly.

## Disclaimer to carry

Planomy states its material is educational planning software, not financial, tax or investment
advice. Any answer built on these figures should say the same.

## Related

- `data-model/planomy-tax-data-data-model.yml` — the full field graph and the source relationships
- `examples/planomy-tax-data-2026.json` — a verbatim copy of the live 2026 dataset
