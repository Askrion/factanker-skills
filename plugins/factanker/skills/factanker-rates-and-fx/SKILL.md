---
name: factanker-rates-and-fx
description: Current official interest rates and the euro reference rate with source and date: US effective federal funds rate, Treasury yields, 30- and 15-year mortgage rates, ECB deposit facility rate, euro short-term rate (€STR), ECB bank lending rates for companies, and EUR/USD. Use for cost-of-capital inputs, treasury and liquidity planning, FX exposure notes and any "what is the rate today" question. Free, no API key.
compatibility: Needs the FACTANKER MCP server (https://factanker.com/mcp, remote, no auth).
---

# Rates and FX from central banks (FACTANKER)

Rates live on geographic or currency entities, not on the central bank. Use these identifiers – names like "United States" are ambiguous:

| Entity | Identifier |
|---|---|
| United States | `iso3166_1:US` |
| Germany | `iso3166_1:DE` |
| Euro area | `ecb_ref_area:U2` |
| US dollar (for EUR/USD) | `iso4217:USD` |

## Predicates

| Question | entity | predicate | Source |
|---|---|---|---|
| Effective federal funds rate | `iso3166_1:US` | `us_effr` | NY Fed (daily; value also carries the target range) |
| 10-year / 2-year Treasury yield | `iso3166_1:US` | `treasury_yield_10y`, `treasury_yield_2y` | US Treasury (daily) |
| 30-year / 15-year fixed mortgage rate | `iso3166_1:US` | `mortgage_rate_30y_fixed`, `mortgage_rate_15y_fixed` | Freddie Mac PMMS (weekly) |
| US consumer prices | `iso3166_1:US` | `cpi_u_all_items`, `cpi_u_core` | BLS (monthly index; compute year-on-year from two values) |
| ECB deposit facility rate | `ecb_ref_area:U2` | `ecb_deposit_facility_rate` | ECB |
| Euro short-term rate | `ecb_ref_area:U2` | `ecb_euro_short_term_rate` | ECB (daily) |
| Euro per US dollar reference rate | `iso4217:USD` | `ecb_reference_rate_per_eur` | ECB (daily, 14:15 CET; value = USD per 1 EUR) |

Call `get_facts(entity=..., predicate=...)`. Results are newest first: the first row is the latest value. For daily series the response can be large; read only the first rows.

## Rules

- Always give the date of the value (`period_end`, `as_of` or `week_ending` in the value) and the source. A rate without its date is not an answer.
- `us_effr` is the effective rate, not the target range; the target bounds are inside the value object.
- The prime rate is a convention (top of the Fed target range + 3 percentage points), not an official series. Say so if you derive it.
- EUR/USD from the ECB is a reference rate, not a tradable quote.
- Cite the `fact_url` of each rate you use.
