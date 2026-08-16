# GetLeads filter reference

Exact filter vocabulary for GetLeads, so a proposed draft runs on first paste instead of erroring or silently returning the wrong scope.

Everything here applies to `POST /api/v1/contacts/search`, which is what the GetLeads MCP, CLI, and web app all sit on top of.

## Contents

- [Two structural rules](#two-structural-rules)
- [Role and seniority](#role-and-seniority)
- [Company firmographics](#company-firmographics)
- [Location, all three kinds](#location-all-three-kinds)
- [Person identity and interests](#person-identity-and-interests)
- [Email and phone quality](#email-and-phone-quality)
- [Excludes](#excludes)
- [Search options](#search-options)
- [Signals](#signals)
- [Example bodies](#example-bodies)
- [Cost model](#cost-model)

---

## Two structural rules

1. Filters go inside a nested `filters` object, or as top-level keys. Both work.
2. **Every filter combines with AND.** Each one you add makes the list smaller and more precise. There is no OR across fields.

The practical consequence worth telling the user out loud: a sloppy five-thousand-row pull costs exactly the same as a precise one. Filter quality is cost control.

---

## Role and seniority

| Field | Type | Notes |
|---|---|---|
| `job_titles` | string[] | Keyword, **substring match**. "CEO" also catches "Deputy CEO" |
| `seniority` | string[] | `C-Team`, `VP`, `Director`, `Manager`, `Staff`, `Other` |
| `job_functions` | string[] | Department, e.g. Sales & Business Development, Engineering, Information Technology |
| `personas` | string[] | Buyer personas (CEO / Founder, CTO, DevOps). Derived from title + function + seniority |

**Seniority aliases normalise automatically:** `C-Suite`, `C-Level`, and `Executive` all resolve to `C-Team`. `Vice President`, `SVP`, and `EVP` resolve to `VP`.

Use the canonical values anyway. Unknown persona values return an error listing the valid ones.

---

## Company firmographics

| Field | Type | Notes |
|---|---|---|
| `industries` | string[] | LinkedIn industry names. Legacy names auto-map to current ones. Unknown values error out with a valid list |
| `company_size_min` / `company_size_max` | number | **Overlapping bands, not exact headcount.** See the trap below |
| `employees_min` / `employees_max` | number | Same banding behaviour |
| `revenue` | string[] | `<$1M`, `$1M to <$10M`, `$10M to <$50M`, `$50M to <$100M`, `$100M to <$1B`, `$1B+` |
| `revenue_min` / `revenue_max` | number | USD, range overlap |
| `company_description` | string | Keyword search over the company's LinkedIn About text. Case-insensitive substring, comma-separate to OR |
| `entity_types` | string[] | Public Company, Privately Held, Non Profit, and similar |
| `technologies` | string[] | Tools the company uses (Salesforce, HubSpot). Matches across all technographic fields |
| `has_mobile_app` / `has_web_app` | boolean | Presence of an app |
| `founded_year_min` / `founded_year_max` | number | Founding year |
| `total_funding_min` / `total_funding_max` | number | Total raised, USD |
| `followers_min` / `followers_max` | number | LinkedIn followers |
| `monthly_traffic_min` / `monthly_traffic_max` | number | Total monthly web traffic |
| `domains` | string[] | Company website domains |
| `company_name` | string | Substring match |

**The size trap:** the underlying data has no exact headcount, only LinkedIn size bands. Setting `company_size_min: 10` with `company_size_max: 60` also pulls the entire "1 to 10" bucket, because that band overlaps the minimum. Use band edges instead: `11` with `50` for "11 to 50". Resolve this silently when drafting rather than passing a user's raw range straight through.

---

## Location, all three kinds

This is the highest-value distinction in the whole API, because getting it wrong produces a believable list of the wrong people with no error.

**Where the person lives:**

| Field | Notes |
|---|---|
| `countries` | Person's country |
| `regions` | `NORAM`, `EMEA`, `APAC`, `LATAM` |
| `continents` | North America, Europe, Asia, South America, Africa, Oceania, Antarctica |
| `cities` | Person's city, substring match |
| `states` | Person's state or province, substring match |

**Where the office is:**

| Field | Notes |
|---|---|
| `job_location_country` | Country of the company or office |
| `job_location_state` | Substring match |
| `job_location_city` | Substring match |

**Where the company is headquartered:**

| Field | Notes |
|---|---|
| `headquarters_countries` | Company HQ country |

In remote-heavy industries these three diverge constantly. Ask which one actually matters before picking.

---

## Person identity and interests

| Field | Notes |
|---|---|
| `first_name` / `last_name` | Substring match |
| `email_address` | **Exact** match |
| `linkedin_url` | Substring match |
| `person_description` | Keyword search over the LinkedIn bio. Comma-separate to OR |
| `skills` | Keyword search over listed skills. Comma-separate to OR |

`person_description` and `skills` are underused and often where a non-obvious signal actually lives. Someone describing a specific problem in their own bio is a stronger buying indicator than their job title.

---

## Email and phone quality

| Field | Notes |
|---|---|
| `email_status` | Enum: `VALID`, `CATCH_ALL`, `INVALID`. Use `["VALID"]` for deliverable. **Never send `"verified"`** |
| `require_email` | Only contacts with a non-empty email |
| `require_phone` | Only contacts with a phone number. Defaults to `false` |

Every contact also carries the date its email was last verified, so record freshness is visible rather than assumed.

---

## Excludes

| Field | Notes |
|---|---|
| `exclude_domains` | Exclude company domains |
| `exclude_countries` | Exclude person countries |
| `exclude_headquarters_countries` | Exclude company HQ countries |
| `exclude_industries` | Exclude industries |
| `exclude_job_titles` | Exclude titles containing these keywords |

Excludes are where a good list gets sharp. Existing customers, current pipeline, and competitors all belong in `exclude_domains` on nearly every search.

---

## Search options

Top level, alongside `filters`.

| Field | Notes |
|---|---|
| `limit` | Rows this page. Default 1000, max 5000 |
| `offset` | Skip rows for pagination. Use `next_offset` from the prior response |
| `max_per_company` | 1 to 50. Caps contacts per company, which keeps a list spread across many accounts instead of stacking twenty people from one |
| `columns` | Output columns by display label or internal name |

`max_per_company` is worth setting on almost every search. Without it, a broad query can return most of its rows from a handful of large companies.

---

## Signals

Funding and acquisition signals pull live from RSS: TechCrunch, Sifted, EU-Startups, Tech.eu for funding, and TechCrunch M&A plus PR Newswire for acquisitions. They return structured facts, never article text.

The two documented MCP tools are `list_funding_signals` and `list_acquisition_signals`.

Worth knowing when strategising: these tell you who *raised*, which is the signal everybody already has. The more interesting use is usually inverse or delayed. Who raised nine months ago and has gone quiet since. Who was acquired, which means a leadership team is about to be reshuffled and budgets re-approved.

---

## Example bodies

Minimal:

```json
{
  "filters": {
    "seniority": ["C-Team"],
    "industries": ["Marketing Services"],
    "countries": ["United States"],
    "email_status": ["VALID"]
  },
  "max_per_company": 3,
  "limit": 500
}
```

Sharper, using excludes and a bio keyword rather than title alone:

```json
{
  "filters": {
    "seniority": ["C-Team", "VP"],
    "job_functions": ["Sales & Business Development"],
    "company_size_min": 11,
    "company_size_max": 50,
    "job_location_country": ["United States"],
    "person_description": "outbound, cold email, pipeline",
    "technologies": ["HubSpot"],
    "email_status": ["VALID"],
    "exclude_domains": ["yourcustomer.com", "competitor.com"]
  },
  "max_per_company": 2,
  "limit": 1000
}
```

Note the size values are band edges, not a raw range, and the location filter is explicitly the office rather than the person's home.

---

## Cost model

Two separate currencies, and mixing them up in front of a user is embarrassing because they hit it immediately.

**Credits** cover search, enrichment, and lookups. One credit per record returned, or per successful enrichment. Free: failed enrichments, zero-result searches, unfound phone lookups, and all filter-value lookups. New accounts start with 5,000 contacts free, no card required.

**A prepaid wallet, in actual cash,** covers the two operations that involve live scraping: profile monitoring at $0.012 per engager, and company follower exports at $0.0035 per follower with an $8.75 minimum. These are not covered by the free credits.

When explaining cost to a user, describe the two systems separately rather than implying the free tier covers everything.

---

## Setup, in one line

MCP server URL is `https://app.getleads.io/api/mcp`, streamable HTTP. In Claude, add it under Connectors as a custom connector and sign in with OAuth, no API key needed. In Claude Code, Cursor, or Codex, generate a key at `app.getleads.io/api-keys` and pass it as a Bearer header.

One gotcha worth passing on: in Claude Code, put the literal key in the header rather than an environment variable. Variable substitution in HTTP headers fails silently there and every call gets rejected with no useful error.
