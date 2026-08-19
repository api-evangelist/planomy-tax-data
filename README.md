# Planomy Tax Data (planomy-tax-data)

A free, keyless open-data JSON API publishing the 2026 US retirement and tax figures that drive the Planomy local-first personal-finance planner: federal income-tax brackets and the standard deduction, FICA rates and the Social Security wage base, the COLA and retirement earnings test, Medicare Part A/B/D premiums and deductibles with the full IRMAA tier tables, contribution limits for employer plans, IRA, SIMPLE, HSA, HDHP and health FSA, Roth and traditional-IRA phase-outs, Saver's Credit thresholds, RMD applicable ages, and a set of state income-tax presets. Two static JSON endpoints — a year manifest and one document per tax year — served from a Cloudflare CDN with no key, no sign-up and no quota. Its distinguishing feature is provenance: every section carries a source key that resolves to a named IRS, SSA, CMS or Federal Register document with the date it was last verified, so any single figure can be cited back to the government publication it came from.

**APIs.json:** [https://planomy-tax-data.apievangelist.com/apis.yml](https://planomy-tax-data.apievangelist.com/apis.yml)

## Tags

- tax
- finance
- open-data
- retirement
- government
- personal-finance
- irs
- social-security
- medicare
- json

## Timestamps

- **Created:** 2026-07-28
- **Modified:** 2026-08-09

## APIs

### Planomy Tax Data Tax Data API

Dated, source-attributed US tax and retirement figures.

- **Human URL:** [https://planomy.net/data/](https://planomy.net/data/)
- **Base URL:** `https://planomy.net`

#### Tags

- tax-data

#### Properties

- [OpenAPI](openapi/planomy-tax-data-tax-data-api-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/planomy-tax-data-tax-data-api.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/planomy-tax-data-tax-data-api.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)
- [Documentation](https://planomy.net/data/)
- [API Reference](https://planomy.net/data/#schema)
- [L L Ms Txt](https://planomy.net/llms.txt)
- [JSON Schema](json-schema/planomy-tax-data-tax-year-dataset.json) — [JSON Schema](https://json-schema.org/specification)
- [JSON Schema](json-schema/planomy-tax-data-index.json) — [JSON Schema](https://json-schema.org/specification)
- [Examples](examples/planomy-tax-data-2026.json)
- [Examples](examples/planomy-tax-data-index.json)

## Common Properties

- [Overlay](overlays/planomy-tax-data-overlay.yaml)
- [Agentic Access](agentic-access/planomy-tax-data-agentic-access.yml)
- [Domain Security](security/planomy-tax-data-domain-security.yml)
- [Website](https://planomy.net/)
- [Support](mailto:support@planomy.net)
- [Pricing](https://planomy.net/pricing)
- [Sign Up](https://planomy.net/app/)
- [Terms of Service](https://planomy.net/legal/terms)
- [Privacy Policy](https://planomy.net/legal/privacy)
- [Authentication](authentication/planomy-tax-data-authentication.yml)
- [Conventions](conventions/planomy-tax-data-conventions.yml)
- [Error Catalog](errors/planomy-tax-data-problem-types.yml)
- [Lifecycle](lifecycle/planomy-tax-data-lifecycle.yml)
- [Conformance](conformance/planomy-tax-data-conformance.yml)
- [Data Model](data-model/planomy-tax-data-data-model.yml)
- [Vocabulary](vocabulary/planomy-tax-data-vocabulary.yml)
- [Plans](plans/planomy-tax-data-plans-pricing.yml)
- [Components](components/planomy-tax-data-components.yml)
- [M C P Server](mcp/planomy-tax-data-mcp.yml)
- [Agent Skill](skills/_index.yml)

## Maintainers

**FN:** Planomy
**Email:** support@planomy.net
**URL:** https://planomy.net/data/
