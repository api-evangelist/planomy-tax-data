---
name: Fetch the current US tax and retirement figures
description: >-
  Resolve the latest published tax year from the Planomy manifest, fetch that year's dataset, and
  cache it correctly so repeat runs cost nothing. Use this before any calculation that depends on
  2026 contribution limits, brackets, wage bases, Medicare premiums or RMD ages.
api: openapi/planomy-tax-data-openapi.yml
operations:
  - getTaxDataIndex
  - getTaxDataYear
generated: '2026-08-09'
method: generated
source: openapi/planomy-tax-data-openapi.yml
---

# Fetch the current US tax and retirement figures

Planomy publishes IRS, SSA and CMS figures as two static JSON files. There is no key, no sign-up
and no quota. Both operations are `GET` and read-only, so they are safe to retry.

## Step 1 — resolve the year

Call **`getTaxDataIndex`** — `GET https://planomy.net/tax-data/index.json`.

```
curl -s https://planomy.net/tax-data/index.json
```

Read `latest` (the newest published year) and `years[]` (every year that exists). Build the URL from
`yearUrlTemplate`, substituting the year.

**Never hard-code a year.** A year that is valid but not yet published and a year that is malformed
both return the identical HTML 404 — the manifest is the only way to tell them apart.

## Step 2 — fetch the dataset

Call **`getTaxDataYear`** — `GET https://planomy.net/tax-data/{year}.json`.

```
curl -s https://planomy.net/tax-data/2026.json
```

The response is one complete document (~14 KB) containing `federal`, `fica`, `socialSecurity`,
`medicare`, `contributionLimits`, `phaseOuts`, `rmd`, `retirementContributionLimitDefaults` and
`states`.

## Step 3 — cache on the ETag

Store the `ETag` from the response. On every later run send it back:

```
curl -s -H 'If-None-Match: "<stored-etag>"' https://planomy.net/tax-data/2026.json -o /dev/null -w '%{http_code}'
```

A `304` means nothing changed — keep what you have. This matters: the figures change only a handful
of times a year, driven by when the agencies publish (retirement-plan limits late October or early
November, HSA in spring, Medicare premiums in November). Polling more than daily is waste.

## Reading the values

- Money is a bare number in US dollars — no symbol, no separators. `24500` is $24,500.
- Rates are decimal fractions. `0.062` is 6.2%.
- Brackets are `{min, rate}` where `min` is the lower bound. The upper bound is the **next** entry's
  `min`; the last entry is open-ended.
- Filing-status keys are `single`, `marriedJoint`, `marriedSeparate`, `headOfHousehold` — not every
  section carries all four.

## Failure handling

| Condition | What you get | What to do |
|---|---|---|
| Year not published, or malformed | `404` with `Content-Type: text/html` | Re-read the manifest; do not JSON-parse the body |
| Wrong method (`OPTIONS`, `POST`) | `405` | Use `GET` (or `HEAD` for a cheap freshness check) |
| Nothing changed | `304`, empty body | Success — keep the cached document |
| Edge throttling under abusive load | `403`/`429`/`5xx`, no `Retry-After` | Exponential backoff |

There is no JSON error envelope and no RFC 9457 problem document. **Branch on the status code and
`Content-Type`, never on a parsed body.**

## Cross-origin note

The documentation says no `Access-Control-Allow-Origin` header is sent and tells you to fetch
server-side. As of 2026-08-09 the edge actually returns `access-control-allow-origin: *`, so a plain
browser `fetch()` works. `OPTIONS` returns `405`, so anything that triggers a preflight (custom
headers) still fails. Because the header is undocumented it can be withdrawn without notice — if you
need a guarantee, fetch from your server, build step or scheduled job as the docs advise.

## Related

- `conventions/planomy-tax-data-conventions.yml` — caching, versioning, CORS, rate-limit posture
- `errors/planomy-tax-data-problem-types.yml` — every observed failure mode
- `data-model/planomy-tax-data-data-model.yml` — the entity graph
